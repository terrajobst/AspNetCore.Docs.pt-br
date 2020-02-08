---
title: Filtros no ASP.NET Core
author: Rick-Anderson
description: Saiba como os filtros funcionam e como usá-los no ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2020
uid: mvc/controllers/filters
ms.openlocfilehash: c4bb9d5746e494106ead6ad5bbf972bbcc5a39f1
ms.sourcegitcommit: 0e21d4f8111743bcb205a2ae0f8e57910c3e8c25
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77034059"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="6fa61-103">Filtros no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6fa61-103">Filters in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6fa61-104">Por [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="6fa61-104">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="6fa61-105">Os *Filtros* no ASP.NET Core permitem a execução de código antes ou depois de determinados estágios do pipeline de processamento de solicitações.</span><span class="sxs-lookup"><span data-stu-id="6fa61-105">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="6fa61-106">O filtros internos lidam com tarefas como:</span><span class="sxs-lookup"><span data-stu-id="6fa61-106">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="6fa61-107">Autorização (impedir o acesso a recursos aos quais o usuário não está autorizado).</span><span class="sxs-lookup"><span data-stu-id="6fa61-107">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="6fa61-108">Cache de resposta (causar um curto-circuito do pipeline de solicitação para retornar uma resposta armazenada em cache).</span><span class="sxs-lookup"><span data-stu-id="6fa61-108">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="6fa61-109">É possível criar filtros personalizados para lidar com interesses paralelos.</span><span class="sxs-lookup"><span data-stu-id="6fa61-109">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="6fa61-110">Entre os exemplos de interesses paralelos estão o tratamento de erros, cache, configuração, autorização e registro em log.</span><span class="sxs-lookup"><span data-stu-id="6fa61-110">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="6fa61-111">Filtros evitam a duplicação do código.</span><span class="sxs-lookup"><span data-stu-id="6fa61-111">Filters avoid duplicating code.</span></span> <span data-ttu-id="6fa61-112">Por exemplo, um filtro de exceção de tratamento de erro poderia consolidar o tratamento de erro.</span><span class="sxs-lookup"><span data-stu-id="6fa61-112">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="6fa61-113">Este documento se aplica ao Razor Pages, a controladores de API e controladores com exibição.</span><span class="sxs-lookup"><span data-stu-id="6fa61-113">This document applies to Razor Pages, API controllers, and controllers with views.</span></span> <span data-ttu-id="6fa61-114">Os filtros não funcionam diretamente com os [componentes do Razor](xref:blazor/components).</span><span class="sxs-lookup"><span data-stu-id="6fa61-114">Filters don't work directly with [Razor components](xref:blazor/components).</span></span> <span data-ttu-id="6fa61-115">Um filtro só pode afetar indiretamente um componente quando:</span><span class="sxs-lookup"><span data-stu-id="6fa61-115">A filter can only indirectly affect a component when:</span></span>

* <span data-ttu-id="6fa61-116">O componente é inserido em uma página ou exibição.</span><span class="sxs-lookup"><span data-stu-id="6fa61-116">The component is embedded in a page or view.</span></span>
* <span data-ttu-id="6fa61-117">A página ou o controlador/modo de exibição usa o filtro.</span><span class="sxs-lookup"><span data-stu-id="6fa61-117">The page or controller/view uses the filter.</span></span>

<span data-ttu-id="6fa61-118">[Exibir ou baixar exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="6fa61-118">[View or download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="6fa61-119">Como os filtros funcionam</span><span class="sxs-lookup"><span data-stu-id="6fa61-119">How filters work</span></span>

<span data-ttu-id="6fa61-120">Os filtros são executados dentro do *pipeline de invocação de ações do ASP.NET Core*, às vezes chamado de *pipeline de filtros*.</span><span class="sxs-lookup"><span data-stu-id="6fa61-120">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span> <span data-ttu-id="6fa61-121">O pipeline de filtros é executado após o ASP.NET Core selecionar a ação a ser executada.</span><span class="sxs-lookup"><span data-stu-id="6fa61-121">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![A solicitação é processada por meio de outro middleware, middleware de roteamento, seleção de ação e o pipeline de invocação de ação.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="6fa61-124">Tipos de filtro</span><span class="sxs-lookup"><span data-stu-id="6fa61-124">Filter types</span></span>

<span data-ttu-id="6fa61-125">Cada tipo de filtro é executado em um estágio diferente no pipeline de filtros:</span><span class="sxs-lookup"><span data-stu-id="6fa61-125">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="6fa61-126">[Filtros de autorização](#authorization-filters) são executados primeiro e são usados para determinar se o usuário tem autorização para a solicitação.</span><span class="sxs-lookup"><span data-stu-id="6fa61-126">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="6fa61-127">Os filtros de autorização de circuito curto do pipeline se a solicitação não for autorizada.</span><span class="sxs-lookup"><span data-stu-id="6fa61-127">Authorization filters short-circuit the pipeline if the request is not authorized.</span></span>

* <span data-ttu-id="6fa61-128">[Filtros de recurso](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="6fa61-128">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="6fa61-129">Execute após a autorização.</span><span class="sxs-lookup"><span data-stu-id="6fa61-129">Run after authorization.</span></span>  
  * <span data-ttu-id="6fa61-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> executa o código antes do resto do pipeline de filtro.</span><span class="sxs-lookup"><span data-stu-id="6fa61-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> runs code before the rest of the filter pipeline.</span></span> <span data-ttu-id="6fa61-131">Por exemplo, `OnResourceExecuting` executa o código antes da Associação de modelo.</span><span class="sxs-lookup"><span data-stu-id="6fa61-131">For example, `OnResourceExecuting` runs code before model binding.</span></span>
  * <span data-ttu-id="6fa61-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> executa o código após a conclusão do restante do pipeline.</span><span class="sxs-lookup"><span data-stu-id="6fa61-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> runs code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="6fa61-133">[Filtros de ação](#action-filters):</span><span class="sxs-lookup"><span data-stu-id="6fa61-133">[Action filters](#action-filters):</span></span>

  * <span data-ttu-id="6fa61-134">Execute o código imediatamente antes e depois que um método de ação for chamado.</span><span class="sxs-lookup"><span data-stu-id="6fa61-134">Run code immediately before and after an action method is called.</span></span>
  * <span data-ttu-id="6fa61-135">Pode alterar os argumentos passados para uma ação.</span><span class="sxs-lookup"><span data-stu-id="6fa61-135">Can change the arguments passed into an action.</span></span>
  * <span data-ttu-id="6fa61-136">Pode alterar o resultado retornado da ação.</span><span class="sxs-lookup"><span data-stu-id="6fa61-136">Can change the result returned from the action.</span></span>
  * <span data-ttu-id="6fa61-137">**Não** têm suporte no Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="6fa61-137">Are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="6fa61-138">Os [filtros de exceção](#exception-filters) aplicam políticas globais a exceções sem tratamento que ocorrem antes da gravação do corpo da resposta.</span><span class="sxs-lookup"><span data-stu-id="6fa61-138">[Exception filters](#exception-filters) apply global policies to unhandled exceptions that occur before the response body has been written to.</span></span>

* <span data-ttu-id="6fa61-139">Os [filtros de resultado](#result-filters) executam o código imediatamente antes e após a execução dos resultados da ação.</span><span class="sxs-lookup"><span data-stu-id="6fa61-139">[Result filters](#result-filters) run code immediately before and after the execution of action results.</span></span> <span data-ttu-id="6fa61-140">Eles são executados somente quando o método de ação é executado com êxito.</span><span class="sxs-lookup"><span data-stu-id="6fa61-140">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="6fa61-141">Eles são úteis para a lógica que precisa envolver a execução da exibição ou do formatador.</span><span class="sxs-lookup"><span data-stu-id="6fa61-141">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="6fa61-142">O diagrama a seguir mostra como os tipos de filtro interagem no pipeline de filtros.</span><span class="sxs-lookup"><span data-stu-id="6fa61-142">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![A solicitação é processada por meio de Filtros de autorização, Filtros de recurso, Model binding, Filtros de ação, Execução de ação e Conversão do resultado de ação, Filtros de exceção, Filtros de resultado e Execução de resultado.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="6fa61-145">Implementação</span><span class="sxs-lookup"><span data-stu-id="6fa61-145">Implementation</span></span>

<span data-ttu-id="6fa61-146">Os filtros dão suporte a implementações síncronas e assíncronas por meio de diferentes definições de interface.</span><span class="sxs-lookup"><span data-stu-id="6fa61-146">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="6fa61-147">Os filtros síncronos executam o código antes e depois do estágio do pipeline.</span><span class="sxs-lookup"><span data-stu-id="6fa61-147">Synchronous filters run code before and after their pipeline stage.</span></span> <span data-ttu-id="6fa61-148">Por exemplo, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> é chamado antes que o método de ação seja chamado.</span><span class="sxs-lookup"><span data-stu-id="6fa61-148">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> is called before the action method is called.</span></span> <span data-ttu-id="6fa61-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> é chamado após o método de ação retornar.</span><span class="sxs-lookup"><span data-stu-id="6fa61-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> is called after the action method returns.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="6fa61-150">Os filtros assíncronos definem um método `On-Stage-ExecutionAsync`.</span><span class="sxs-lookup"><span data-stu-id="6fa61-150">Asynchronous filters define an `On-Stage-ExecutionAsync` method.</span></span> <span data-ttu-id="6fa61-151">Por exemplo <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span><span class="sxs-lookup"><span data-stu-id="6fa61-151">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="6fa61-152">No código anterior, o `SampleAsyncActionFilter` tem um <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) que executa o método de ação.</span><span class="sxs-lookup"><span data-stu-id="6fa61-152">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) that executes the action method.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="6fa61-153">Vários estágios do filtro</span><span class="sxs-lookup"><span data-stu-id="6fa61-153">Multiple filter stages</span></span>

<span data-ttu-id="6fa61-154">É possível implementar interfaces para vários estágios do filtro em uma única classe.</span><span class="sxs-lookup"><span data-stu-id="6fa61-154">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="6fa61-155">Por exemplo, a classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> implementa:</span><span class="sxs-lookup"><span data-stu-id="6fa61-155">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements:</span></span>

* <span data-ttu-id="6fa61-156">Síncrono: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> e <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span><span class="sxs-lookup"><span data-stu-id="6fa61-156">Synchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> and  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span></span>
* <span data-ttu-id="6fa61-157">Assíncrono: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> e <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="6fa61-157">Asynchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

<span data-ttu-id="6fa61-158">Implemente **ou** a versão assíncrona ou a versão síncrona de uma interface de filtro, **não** ambas.</span><span class="sxs-lookup"><span data-stu-id="6fa61-158">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="6fa61-159">Primeiro, o runtime verifica se o filtro implementa a interface assíncrona e, se for esse o caso, a chama.</span><span class="sxs-lookup"><span data-stu-id="6fa61-159">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="6fa61-160">Caso contrário, ela chama os métodos da interface síncrona.</span><span class="sxs-lookup"><span data-stu-id="6fa61-160">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="6fa61-161">Se as interfaces síncrona e assíncrona forem implementadas em uma classe, somente o método assíncrono será chamado.</span><span class="sxs-lookup"><span data-stu-id="6fa61-161">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="6fa61-162">Ao usar classes abstratas como <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, substitua apenas os métodos síncronos ou o método assíncrono para cada tipo de filtro.</span><span class="sxs-lookup"><span data-stu-id="6fa61-162">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, override only the synchronous methods or the asynchronous method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="6fa61-163">Atributos de filtro internos</span><span class="sxs-lookup"><span data-stu-id="6fa61-163">Built-in filter attributes</span></span>

<span data-ttu-id="6fa61-164">O ASP.NET Core inclui filtros internos baseados em atributos que podem ser organizados em subclasses e personalizados.</span><span class="sxs-lookup"><span data-stu-id="6fa61-164">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="6fa61-165">Por exemplo, o filtro de resultado a seguir adiciona um cabeçalho à resposta:</span><span class="sxs-lookup"><span data-stu-id="6fa61-165">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="6fa61-166">Os atributos permitem que os filtros aceitem argumentos, conforme mostrado no exemplo acima.</span><span class="sxs-lookup"><span data-stu-id="6fa61-166">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="6fa61-167">Aplique o `AddHeaderAttribute` a um controlador ou método de ação e especifique o nome e o valor do cabeçalho HTTP:</span><span class="sxs-lookup"><span data-stu-id="6fa61-167">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<span data-ttu-id="6fa61-168">Use uma ferramenta como as [ferramentas de desenvolvedor do navegador](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) para examinar os cabeçalhos.</span><span class="sxs-lookup"><span data-stu-id="6fa61-168">Use a tool such as the [browser developer tools](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) to examine the headers.</span></span> <span data-ttu-id="6fa61-169">Em **cabeçalhos de resposta**, `author: Rick Anderson` é exibido.</span><span class="sxs-lookup"><span data-stu-id="6fa61-169">Under **Response Headers**, `author: Rick Anderson` is displayed.</span></span>

<span data-ttu-id="6fa61-170">O código a seguir implementa um `ActionFilterAttribute` que:</span><span class="sxs-lookup"><span data-stu-id="6fa61-170">The following code implements an `ActionFilterAttribute` that:</span></span>

* <span data-ttu-id="6fa61-171">Lê o título e o nome do sistema de configuração.</span><span class="sxs-lookup"><span data-stu-id="6fa61-171">Reads the title and name from the configuration system.</span></span> <span data-ttu-id="6fa61-172">Ao contrário do exemplo anterior, o código a seguir não exige que os parâmetros de filtro sejam adicionados ao código.</span><span class="sxs-lookup"><span data-stu-id="6fa61-172">Unlike the previous sample, the following code doesn't require filter parameters to be added to the code.</span></span>
* <span data-ttu-id="6fa61-173">Adiciona o título e o nome ao cabeçalho de resposta.</span><span class="sxs-lookup"><span data-stu-id="6fa61-173">Adds the title and name to the response header.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

<span data-ttu-id="6fa61-174">As opções de configuração são fornecidas no [sistema de configuração](xref:fundamentals/configuration/index) usando o [padrão de opções](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="6fa61-174">The configuration options are provided from the [configuration system](xref:fundamentals/configuration/index) using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="6fa61-175">Por exemplo, no arquivo *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="6fa61-175">For example, from the *appsettings.json* file:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/appsettings.json)]

<span data-ttu-id="6fa61-176">No `StartUp.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="6fa61-176">In the `StartUp.ConfigureServices`:</span></span>

* <span data-ttu-id="6fa61-177">A classe `PositionOptions` é adicionada ao contêiner de serviço com a área de configuração `"Position"`.</span><span class="sxs-lookup"><span data-stu-id="6fa61-177">The `PositionOptions` class is added to the service container with the `"Position"` configuration area.</span></span>
* <span data-ttu-id="6fa61-178">O `MyActionFilterAttribute` é adicionado ao contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="6fa61-178">The `MyActionFilterAttribute` is added to the service container.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

<span data-ttu-id="6fa61-179">O código a seguir mostra a classe `PositionOptions`:</span><span class="sxs-lookup"><span data-stu-id="6fa61-179">The following code shows the `PositionOptions` class:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

<span data-ttu-id="6fa61-180">O código a seguir aplica o `MyActionFilterAttribute` ao método `Index2`:</span><span class="sxs-lookup"><span data-stu-id="6fa61-180">The following code applies the `MyActionFilterAttribute` to the `Index2` method:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

<span data-ttu-id="6fa61-181">Em **cabeçalhos de resposta**, `author: Rick Anderson`e `Editor: Joe Smith` são exibidos quando o ponto de extremidade `Sample/Index2` é chamado.</span><span class="sxs-lookup"><span data-stu-id="6fa61-181">Under **Response Headers**, `author: Rick Anderson`, and `Editor: Joe Smith` is displayed when the `Sample/Index2` endpoint is called.</span></span>

<span data-ttu-id="6fa61-182">O código a seguir aplica o `MyActionFilterAttribute` e o `AddHeaderAttribute` à página Razor:</span><span class="sxs-lookup"><span data-stu-id="6fa61-182">The following code applies the `MyActionFilterAttribute` and the `AddHeaderAttribute` to the Razor Page:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="6fa61-183">Os filtros não podem ser aplicados aos métodos do manipulador de páginas do Razor.</span><span class="sxs-lookup"><span data-stu-id="6fa61-183">Filters cannot be applied to Razor Page handler methods.</span></span> <span data-ttu-id="6fa61-184">Eles podem ser aplicados ao modelo de página do Razor ou globalmente.</span><span class="sxs-lookup"><span data-stu-id="6fa61-184">They can be applied either to the Razor Page model or globally.</span></span>

<span data-ttu-id="6fa61-185">Várias interfaces de filtro têm atributos correspondentes que podem ser usados como classes base para implementações personalizadas.</span><span class="sxs-lookup"><span data-stu-id="6fa61-185">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="6fa61-186">Atributos de filtro:</span><span class="sxs-lookup"><span data-stu-id="6fa61-186">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="6fa61-187">Escopos e ordem de execução dos filtros</span><span class="sxs-lookup"><span data-stu-id="6fa61-187">Filter scopes and order of execution</span></span>

<span data-ttu-id="6fa61-188">Um filtro pode ser adicionado ao pipeline com um de três *escopos*:</span><span class="sxs-lookup"><span data-stu-id="6fa61-188">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="6fa61-189">Usando um atributo em uma ação do controlador.</span><span class="sxs-lookup"><span data-stu-id="6fa61-189">Using an attribute on a controller action.</span></span> <span data-ttu-id="6fa61-190">Atributos de filtro não podem ser aplicados a métodos de manipuladores de Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="6fa61-190">Filter attributes cannot be applied to Razor Pages handler methods.</span></span>
* <span data-ttu-id="6fa61-191">Usando um atributo em um controlador ou em uma página Razor.</span><span class="sxs-lookup"><span data-stu-id="6fa61-191">Using an attribute on a controller or Razor Page.</span></span>
* <span data-ttu-id="6fa61-192">Globalmente para todos os controladores, ações e Razor Pages, conforme mostrado no código a seguir:</span><span class="sxs-lookup"><span data-stu-id="6fa61-192">Globally for all controllers, actions, and Razor Pages as shown in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a><span data-ttu-id="6fa61-193">Ordem padrão de execução</span><span class="sxs-lookup"><span data-stu-id="6fa61-193">Default order of execution</span></span>

<span data-ttu-id="6fa61-194">Quando há vários filtros para um determinado estágio do pipeline, o escopo determina a ordem padrão de execução dos filtros.</span><span class="sxs-lookup"><span data-stu-id="6fa61-194">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="6fa61-195">Filtros globais circundam filtros de classe, que, por sua vez, circundam filtros de método.</span><span class="sxs-lookup"><span data-stu-id="6fa61-195">Global filters surround class filters, which in turn surround method filters.</span></span>

<span data-ttu-id="6fa61-196">Como resultado do aninhamento de filtro, o código *posterior* dos filtros é executado na ordem inversa do código *anterior*.</span><span class="sxs-lookup"><span data-stu-id="6fa61-196">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="6fa61-197">A sequência de filtro:</span><span class="sxs-lookup"><span data-stu-id="6fa61-197">The filter sequence:</span></span>

* <span data-ttu-id="6fa61-198">O código *anterior* dos filtros globais.</span><span class="sxs-lookup"><span data-stu-id="6fa61-198">The *before* code of global filters.</span></span>
  * <span data-ttu-id="6fa61-199">O código *anterior* dos filtros de página do controlador e do Razor.</span><span class="sxs-lookup"><span data-stu-id="6fa61-199">The *before* code of controller and Razor Page filters.</span></span>
    * <span data-ttu-id="6fa61-200">O código *anterior* dos filtros de método de ação.</span><span class="sxs-lookup"><span data-stu-id="6fa61-200">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="6fa61-201">O código *posterior* dos filtros de método de ação.</span><span class="sxs-lookup"><span data-stu-id="6fa61-201">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="6fa61-202">O código *After* dos filtros de página do controlador e do Razor.</span><span class="sxs-lookup"><span data-stu-id="6fa61-202">The *after* code of controller and Razor Page filters.</span></span>
* <span data-ttu-id="6fa61-203">O código *posterior* dos filtros globais.</span><span class="sxs-lookup"><span data-stu-id="6fa61-203">The *after* code of global filters.</span></span>
  
<span data-ttu-id="6fa61-204">O exemplo a seguir ilustra a ordem na qual os métodos de filtro são chamados para filtros de ação síncrona.</span><span class="sxs-lookup"><span data-stu-id="6fa61-204">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="6fa61-205">Sequência</span><span class="sxs-lookup"><span data-stu-id="6fa61-205">Sequence</span></span> | <span data-ttu-id="6fa61-206">Escopo do filtro</span><span class="sxs-lookup"><span data-stu-id="6fa61-206">Filter scope</span></span> | <span data-ttu-id="6fa61-207">Método Filter</span><span class="sxs-lookup"><span data-stu-id="6fa61-207">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="6fa61-208">1</span><span class="sxs-lookup"><span data-stu-id="6fa61-208">1</span></span> | <span data-ttu-id="6fa61-209">Global</span><span class="sxs-lookup"><span data-stu-id="6fa61-209">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="6fa61-210">2</span><span class="sxs-lookup"><span data-stu-id="6fa61-210">2</span></span> | <span data-ttu-id="6fa61-211">Controlador ou página Razor</span><span class="sxs-lookup"><span data-stu-id="6fa61-211">Controller or Razor Page</span></span>| `OnActionExecuting` |
| <span data-ttu-id="6fa61-212">3</span><span class="sxs-lookup"><span data-stu-id="6fa61-212">3</span></span> | <span data-ttu-id="6fa61-213">Método</span><span class="sxs-lookup"><span data-stu-id="6fa61-213">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="6fa61-214">4</span><span class="sxs-lookup"><span data-stu-id="6fa61-214">4</span></span> | <span data-ttu-id="6fa61-215">Método</span><span class="sxs-lookup"><span data-stu-id="6fa61-215">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="6fa61-216">5</span><span class="sxs-lookup"><span data-stu-id="6fa61-216">5</span></span> | <span data-ttu-id="6fa61-217">Controlador ou página Razor</span><span class="sxs-lookup"><span data-stu-id="6fa61-217">Controller or Razor Page</span></span> | `OnActionExecuted` |
| <span data-ttu-id="6fa61-218">6</span><span class="sxs-lookup"><span data-stu-id="6fa61-218">6</span></span> | <span data-ttu-id="6fa61-219">Global</span><span class="sxs-lookup"><span data-stu-id="6fa61-219">Global</span></span> | `OnActionExecuted` |

### <a name="controller-level-filters"></a><span data-ttu-id="6fa61-220">Filtros de nível de controlador</span><span class="sxs-lookup"><span data-stu-id="6fa61-220">Controller level filters</span></span>

<span data-ttu-id="6fa61-221">Cada controlador que herda da classe base <xref:Microsoft.AspNetCore.Mvc.Controller> inclui os métodos [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) e [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="6fa61-221">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="6fa61-222">Estes métodos:</span><span class="sxs-lookup"><span data-stu-id="6fa61-222">These methods:</span></span>

* <span data-ttu-id="6fa61-223">Encapsulam os filtros executados para uma determinada ação.</span><span class="sxs-lookup"><span data-stu-id="6fa61-223">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="6fa61-224">`OnActionExecuting` é chamado antes de qualquer um dos filtros da ação.</span><span class="sxs-lookup"><span data-stu-id="6fa61-224">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="6fa61-225">`OnActionExecuted` é chamado após todos os filtros da ação.</span><span class="sxs-lookup"><span data-stu-id="6fa61-225">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="6fa61-226">`OnActionExecutionAsync` é chamado antes de qualquer um dos filtros da ação.</span><span class="sxs-lookup"><span data-stu-id="6fa61-226">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="6fa61-227">O código no filtro após `next` é executado após o método de ação.</span><span class="sxs-lookup"><span data-stu-id="6fa61-227">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="6fa61-228">Por exemplo, no download de exemplo, `MySampleActionFilter` é aplicado globalmente na inicialização.</span><span class="sxs-lookup"><span data-stu-id="6fa61-228">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="6fa61-229">`TestController`:</span><span class="sxs-lookup"><span data-stu-id="6fa61-229">The `TestController`:</span></span>

* <span data-ttu-id="6fa61-230">Aplica o `SampleActionFilterAttribute` (`[SampleActionFilter]`) à ação `FilterTest2`.</span><span class="sxs-lookup"><span data-stu-id="6fa61-230">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="6fa61-231">Substitui `OnActionExecuting` e `OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="6fa61-231">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

<span data-ttu-id="6fa61-232">Navegar até `https://localhost:5001/Test2/FilterTest2` executa o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="6fa61-232">Navigating to `https://localhost:5001/Test2/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="6fa61-233">Filtros de nível de controlador definem a propriedade [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) como `int.MinValue`.</span><span class="sxs-lookup"><span data-stu-id="6fa61-233">Controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue`.</span></span> <span data-ttu-id="6fa61-234">Os filtros de nível de controlador **não** podem ser definidos para execução após os filtros aplicados aos métodos.</span><span class="sxs-lookup"><span data-stu-id="6fa61-234">Controller level filters can **not** be set to run after filters applied to methods.</span></span> <span data-ttu-id="6fa61-235">A ordem é explicada na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="6fa61-235">Order is explained in the next section.</span></span>

<span data-ttu-id="6fa61-236">Para Razor Pages, confira [Implementar filtros do Razor Page substituindo os métodos de filtro](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="6fa61-236">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="6fa61-237">Substituindo a ordem padrão</span><span class="sxs-lookup"><span data-stu-id="6fa61-237">Overriding the default order</span></span>

<span data-ttu-id="6fa61-238">É possível substituir a sequência padrão de execução implementando <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span><span class="sxs-lookup"><span data-stu-id="6fa61-238">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="6fa61-239">`IOrderedFilter` expõe a propriedade <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> que tem precedência sobre o escopo para determinar a ordem da execução.</span><span class="sxs-lookup"><span data-stu-id="6fa61-239">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="6fa61-240">Um filtro com um valor de `Order` menor:</span><span class="sxs-lookup"><span data-stu-id="6fa61-240">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="6fa61-241">Executa o código *anterior* antes de um filtro com um valor mais alto de `Order`.</span><span class="sxs-lookup"><span data-stu-id="6fa61-241">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="6fa61-242">Executa o código *posterior* após um filtro com um valor mais alto de `Order`.</span><span class="sxs-lookup"><span data-stu-id="6fa61-242">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="6fa61-243">A propriedade `Order` é definida com um parâmetro de construtor:</span><span class="sxs-lookup"><span data-stu-id="6fa61-243">The `Order` property is set with a constructor parameter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

<span data-ttu-id="6fa61-244">Considere os dois filtros de ação no controlador a seguir:</span><span class="sxs-lookup"><span data-stu-id="6fa61-244">Consider the two action filters in the following controller:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="6fa61-245">Um filtro global é adicionado no `StartUp.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="6fa61-245">A global filter is added in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

<span data-ttu-id="6fa61-246">Os 3 filtros são executados na seguinte ordem:</span><span class="sxs-lookup"><span data-stu-id="6fa61-246">The 3 filters run in the following order:</span></span>

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MySampleActionFilter.OnActionExecuted`
  * `MyAction2FilterAttribute.OnResultExecuting`
* `Test2Controller.OnActionExecuted`

<span data-ttu-id="6fa61-247">A propriedade `Order` substitui o escopo ao determinar a ordem na qual os filtros serão executados.</span><span class="sxs-lookup"><span data-stu-id="6fa61-247">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="6fa61-248">Os filtros são classificados primeiro pela ordem e o escopo é usado para desempatar.</span><span class="sxs-lookup"><span data-stu-id="6fa61-248">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="6fa61-249">Todos os filtros internos implementam `IOrderedFilter` e definem o valor de `Order` padrão como 0.</span><span class="sxs-lookup"><span data-stu-id="6fa61-249">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="6fa61-250">Como mencionado anteriormente, os filtros no nível do controlador definem a propriedade [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) como `int.MinValue` para filtros internos, o escopo determina a ordem, a menos que `Order` esteja definido como um valor diferente de zero.</span><span class="sxs-lookup"><span data-stu-id="6fa61-250">As mentioned previously, controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue` For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

<span data-ttu-id="6fa61-251">No código anterior, `MySampleActionFilter` tem escopo global para que ele seja executado antes `MyAction2FilterAttribute`, que tem o escopo do controlador.</span><span class="sxs-lookup"><span data-stu-id="6fa61-251">In the preceding code, `MySampleActionFilter` has global scope so it runs before `MyAction2FilterAttribute`, which has controller scope.</span></span> <span data-ttu-id="6fa61-252">Para fazer `MyAction2FilterAttribute` executar primeiro, defina o pedido como `int.MinValue`:</span><span class="sxs-lookup"><span data-stu-id="6fa61-252">To make `MyAction2FilterAttribute` run first, set the order to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

<span data-ttu-id="6fa61-253">Para fazer com que o filtro global `MySampleActionFilter` executado primeiro, defina `Order` como `int.MinValue`:</span><span class="sxs-lookup"><span data-stu-id="6fa61-253">To make the global filter `MySampleActionFilter` run first, set `Order` to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="6fa61-254">Cancelamento e curto-circuito</span><span class="sxs-lookup"><span data-stu-id="6fa61-254">Cancellation and short-circuiting</span></span>

<span data-ttu-id="6fa61-255">O pipeline de filtro pode sofrer um curto-circuito por meio da configuração da propriedade <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> no parâmetro <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> fornecido ao método do filtro.</span><span class="sxs-lookup"><span data-stu-id="6fa61-255">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="6fa61-256">Por exemplo, o filtro de Recurso a seguir impede que o resto do pipeline seja executado:</span><span class="sxs-lookup"><span data-stu-id="6fa61-256">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="6fa61-257">No código a seguir, os filtros `ShortCircuitingResourceFilter` e `AddHeader` têm como destino o método de ação `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="6fa61-257">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="6fa61-258">`ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="6fa61-258">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="6fa61-259">É executado primeiro, porque ele é um filtro de recurso e `AddHeader` é um filtro de ação.</span><span class="sxs-lookup"><span data-stu-id="6fa61-259">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="6fa61-260">Causa um curto-circuito no restante do pipeline.</span><span class="sxs-lookup"><span data-stu-id="6fa61-260">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="6fa61-261">Portanto, o filtro `AddHeader` nunca é executado para a ação `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="6fa61-261">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="6fa61-262">Esse comportamento seria o mesmo se os dois filtros fossem aplicados no nível do método de ação, desde que `ShortCircuitingResourceFilter` fosse executado primeiro.</span><span class="sxs-lookup"><span data-stu-id="6fa61-262">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="6fa61-263">O `ShortCircuitingResourceFilter` é executado primeiro, devido ao seu tipo de filtro ou pelo uso explícito da propriedade `Order`.</span><span class="sxs-lookup"><span data-stu-id="6fa61-263">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

## <a name="dependency-injection"></a><span data-ttu-id="6fa61-264">Injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="6fa61-264">Dependency injection</span></span>

<span data-ttu-id="6fa61-265">Filtros podem ser adicionados por tipo ou por instância.</span><span class="sxs-lookup"><span data-stu-id="6fa61-265">Filters can be added by type or by instance.</span></span> <span data-ttu-id="6fa61-266">Se você adicionar uma instância, ela será usada para cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="6fa61-266">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="6fa61-267">Se um tipo for adicionado, ele será ativado pelo tipo.</span><span class="sxs-lookup"><span data-stu-id="6fa61-267">If a type is added, it's type-activated.</span></span> <span data-ttu-id="6fa61-268">Um filtro ativado por tipo significa:</span><span class="sxs-lookup"><span data-stu-id="6fa61-268">A type-activated filter means:</span></span>

* <span data-ttu-id="6fa61-269">Uma instância é criada para cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="6fa61-269">An instance is created for each request.</span></span>
* <span data-ttu-id="6fa61-270">Qualquer dependência de construtor é preenchida pela [injeção de dependência](xref:fundamentals/dependency-injection) (DI).</span><span class="sxs-lookup"><span data-stu-id="6fa61-270">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="6fa61-271">Filtros que são implementados como atributos e adicionados diretamente a classes de controlador ou métodos de ação não podem ter dependências de construtor fornecidas pela DI ([injeção de dependência](xref:fundamentals/dependency-injection)).</span><span class="sxs-lookup"><span data-stu-id="6fa61-271">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="6fa61-272">Dependências de construtor não podem ser fornecidas pela DI porque:</span><span class="sxs-lookup"><span data-stu-id="6fa61-272">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="6fa61-273">Os atributos precisam ter os parâmetros de construtor fornecidos quando são aplicados.</span><span class="sxs-lookup"><span data-stu-id="6fa61-273">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="6fa61-274">Essa é uma limitação do funcionamento dos atributos.</span><span class="sxs-lookup"><span data-stu-id="6fa61-274">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="6fa61-275">Os filtros a seguir são compatíveis com dependências de construtor fornecidas pela injeção de dependência:</span><span class="sxs-lookup"><span data-stu-id="6fa61-275">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="6fa61-276"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementado no atributo.</span><span class="sxs-lookup"><span data-stu-id="6fa61-276"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="6fa61-277">Os filtros anteriores podem ser aplicados a um método de ação ou controlador:</span><span class="sxs-lookup"><span data-stu-id="6fa61-277">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="6fa61-278">Os agentes estão disponíveis na DI.</span><span class="sxs-lookup"><span data-stu-id="6fa61-278">Loggers are available from DI.</span></span> <span data-ttu-id="6fa61-279">No entanto, evite criar e usar filtros apenas para fins de registro em log.</span><span class="sxs-lookup"><span data-stu-id="6fa61-279">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="6fa61-280">O [registro em log da estrutura interna](xref:fundamentals/logging/index) normalmente fornece o que é necessário para o registro em log.</span><span class="sxs-lookup"><span data-stu-id="6fa61-280">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="6fa61-281">Registro em log adicionado aos filtros:</span><span class="sxs-lookup"><span data-stu-id="6fa61-281">Logging added to filters:</span></span>

* <span data-ttu-id="6fa61-282">Deve se concentrar em questões de domínio de negócios ou comportamento específico ao filtro.</span><span class="sxs-lookup"><span data-stu-id="6fa61-282">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="6fa61-283">**Não** deve registrar ações ou outros eventos da estrutura.</span><span class="sxs-lookup"><span data-stu-id="6fa61-283">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="6fa61-284">Os filtros internos registram ações de log e eventos de estrutura.</span><span class="sxs-lookup"><span data-stu-id="6fa61-284">The built-in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="6fa61-285">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="6fa61-285">ServiceFilterAttribute</span></span>

<span data-ttu-id="6fa61-286">Os tipos de implementação do filtro de serviço são registrados em `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="6fa61-286">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="6fa61-287">Um <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> recupera uma instância do filtro da DI.</span><span class="sxs-lookup"><span data-stu-id="6fa61-287">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="6fa61-288">O código a seguir mostra `AddHeaderResultServiceFilter`:</span><span class="sxs-lookup"><span data-stu-id="6fa61-288">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="6fa61-289">No código a seguir, `AddHeaderResultServiceFilter` é adicionado ao contêiner de DI:</span><span class="sxs-lookup"><span data-stu-id="6fa61-289">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

<span data-ttu-id="6fa61-290">No código a seguir, o atributo `ServiceFilter` recupera uma instância do filtro `AddHeaderResultServiceFilter` da DI:</span><span class="sxs-lookup"><span data-stu-id="6fa61-290">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="6fa61-291">Ao usar `ServiceFilterAttribute`, configurar [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="6fa61-291">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="6fa61-292">Fornece uma dica de que a instância de filtro *pode* ser reutilizada fora do escopo de solicitação em que foi criada.</span><span class="sxs-lookup"><span data-stu-id="6fa61-292">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="6fa61-293">O runtime do ASP.NET Core não garante:</span><span class="sxs-lookup"><span data-stu-id="6fa61-293">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="6fa61-294">Que uma única instância do filtro será criada.</span><span class="sxs-lookup"><span data-stu-id="6fa61-294">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="6fa61-295">Que o filtro não será solicitado novamente no contêiner de DI em algum momento posterior.</span><span class="sxs-lookup"><span data-stu-id="6fa61-295">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="6fa61-296">Não deve ser usado com um filtro que dependa dos serviços com um tempo de vida diferente de singleton.</span><span class="sxs-lookup"><span data-stu-id="6fa61-296">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="6fa61-297"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="6fa61-297"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="6fa61-298">`IFilterFactory` expõe o método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> para criar uma instância de <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="6fa61-298">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="6fa61-299">`CreateInstance` carrega o tipo especificado na DI.</span><span class="sxs-lookup"><span data-stu-id="6fa61-299">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="6fa61-300">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="6fa61-300">TypeFilterAttribute</span></span>

<span data-ttu-id="6fa61-301">O <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> é semelhante ao <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, mas seu tipo não é resolvido diretamente por meio do contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="6fa61-301"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="6fa61-302">Ele cria uma instância do tipo usando <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="6fa61-302">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="6fa61-303">Como os tipos `TypeFilterAttribute` não são resolvidos diretamente do contêiner de DI:</span><span class="sxs-lookup"><span data-stu-id="6fa61-303">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="6fa61-304">Os tipos referenciados usando o `TypeFilterAttribute` não precisam ser registrados no contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="6fa61-304">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="6fa61-305">Eles têm suas dependências atendidas pelo contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="6fa61-305">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="6fa61-306">Opcionalmente, o `TypeFilterAttribute` pode aceitar argumentos de construtor para o tipo.</span><span class="sxs-lookup"><span data-stu-id="6fa61-306">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="6fa61-307">Ao usar `TypeFilterAttribute`, configurar [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="6fa61-307">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="6fa61-308">Fornece uma dica de que a instância de filtro *pode* ser reutilizada fora do escopo de solicitação em que foi criada.</span><span class="sxs-lookup"><span data-stu-id="6fa61-308">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="6fa61-309">O runtime do ASP.NET Core não fornece garantias de que uma única instância do filtro será criada.</span><span class="sxs-lookup"><span data-stu-id="6fa61-309">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="6fa61-310">Não deve ser usado com um filtro que dependa dos serviços com um tempo de vida diferente de singleton.</span><span class="sxs-lookup"><span data-stu-id="6fa61-310">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="6fa61-311">O exemplo a seguir mostra como passar argumentos para um tipo usando `TypeFilterAttribute`:</span><span class="sxs-lookup"><span data-stu-id="6fa61-311">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="6fa61-312">Filtros de autorização</span><span class="sxs-lookup"><span data-stu-id="6fa61-312">Authorization filters</span></span>

<span data-ttu-id="6fa61-313">Filtros de autorização:</span><span class="sxs-lookup"><span data-stu-id="6fa61-313">Authorization filters:</span></span>

* <span data-ttu-id="6fa61-314">São os primeiros filtros executados no pipeline de filtro.</span><span class="sxs-lookup"><span data-stu-id="6fa61-314">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="6fa61-315">Controlam o acesso aos métodos de ação.</span><span class="sxs-lookup"><span data-stu-id="6fa61-315">Control access to action methods.</span></span>
* <span data-ttu-id="6fa61-316">Têm um método anterior, mas não têm um método posterior.</span><span class="sxs-lookup"><span data-stu-id="6fa61-316">Have a before method, but no after method.</span></span>

<span data-ttu-id="6fa61-317">Filtros de autorização personalizados exigem uma estrutura de autorização personalizada.</span><span class="sxs-lookup"><span data-stu-id="6fa61-317">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="6fa61-318">Prefira configurar as políticas de autorização ou escrever uma política de autorização personalizada em vez de escrever um filtro personalizado.</span><span class="sxs-lookup"><span data-stu-id="6fa61-318">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="6fa61-319">O filtro de autorização interno:</span><span class="sxs-lookup"><span data-stu-id="6fa61-319">The built-in authorization filter:</span></span>

* <span data-ttu-id="6fa61-320">Chama o sistema de autorização.</span><span class="sxs-lookup"><span data-stu-id="6fa61-320">Calls the authorization system.</span></span>
* <span data-ttu-id="6fa61-321">Não autoriza solicitações.</span><span class="sxs-lookup"><span data-stu-id="6fa61-321">Does not authorize requests.</span></span>

<span data-ttu-id="6fa61-322">**Não** gera exceções dentro de filtros de autorização:</span><span class="sxs-lookup"><span data-stu-id="6fa61-322">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="6fa61-323">A exceção não será tratada.</span><span class="sxs-lookup"><span data-stu-id="6fa61-323">The exception will not be handled.</span></span>
* <span data-ttu-id="6fa61-324">Os filtros de exceção não tratarão a exceção.</span><span class="sxs-lookup"><span data-stu-id="6fa61-324">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="6fa61-325">Considere a possibilidade de emitir um desafio quando ocorrer uma exceção em um filtro de autorização.</span><span class="sxs-lookup"><span data-stu-id="6fa61-325">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="6fa61-326">Saiba mais sobre [Autorização](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="6fa61-326">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="6fa61-327">Filtros de recurso</span><span class="sxs-lookup"><span data-stu-id="6fa61-327">Resource filters</span></span>

<span data-ttu-id="6fa61-328">Filtros de recurso:</span><span class="sxs-lookup"><span data-stu-id="6fa61-328">Resource filters:</span></span>

* <span data-ttu-id="6fa61-329">Implementam a interface <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.</span><span class="sxs-lookup"><span data-stu-id="6fa61-329">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="6fa61-330">A execução encapsula grande parte do pipeline de filtro.</span><span class="sxs-lookup"><span data-stu-id="6fa61-330">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="6fa61-331">Somente os [Filtros de autorização](#authorization-filters) são executados antes dos filtros de recurso.</span><span class="sxs-lookup"><span data-stu-id="6fa61-331">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="6fa61-332">Os filtros de recursos são úteis para causar um curto-circuito na maior parte do pipeline.</span><span class="sxs-lookup"><span data-stu-id="6fa61-332">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="6fa61-333">Por exemplo, um filtro de cache pode evitar o restante do pipeline em uma ocorrência no cache.</span><span class="sxs-lookup"><span data-stu-id="6fa61-333">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="6fa61-334">Exemplos de filtros de recurso:</span><span class="sxs-lookup"><span data-stu-id="6fa61-334">Resource filter examples:</span></span>

* <span data-ttu-id="6fa61-335">[O filtro de recurso em curto-circuito](#short-circuiting-resource-filter) mostrado anteriormente.</span><span class="sxs-lookup"><span data-stu-id="6fa61-335">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="6fa61-336">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="6fa61-336">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="6fa61-337">Impede o model binding de acessar os dados do formulário.</span><span class="sxs-lookup"><span data-stu-id="6fa61-337">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="6fa61-338">Usado para uploads de arquivos grandes para impedir que os dados de formulário sejam lidos na memória.</span><span class="sxs-lookup"><span data-stu-id="6fa61-338">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="6fa61-339">Filtros de ação</span><span class="sxs-lookup"><span data-stu-id="6fa61-339">Action filters</span></span>

<span data-ttu-id="6fa61-340">Os filtros de ação **não** se aplicam ao Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="6fa61-340">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="6fa61-341">O Razor Pages é compatível com <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> e <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter>.</span><span class="sxs-lookup"><span data-stu-id="6fa61-341">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="6fa61-342">Para obter mais informações, confira [Métodos de filtro para Páginas Razor](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="6fa61-342">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="6fa61-343">Filtros de ação:</span><span class="sxs-lookup"><span data-stu-id="6fa61-343">Action filters:</span></span>

* <span data-ttu-id="6fa61-344">Implementam a interface <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.</span><span class="sxs-lookup"><span data-stu-id="6fa61-344">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="6fa61-345">A execução deles envolve a execução de métodos de ação.</span><span class="sxs-lookup"><span data-stu-id="6fa61-345">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="6fa61-346">O código a seguir mostra um exemplo de filtro de ação:</span><span class="sxs-lookup"><span data-stu-id="6fa61-346">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="6fa61-347">A classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> fornece as seguintes propriedades:</span><span class="sxs-lookup"><span data-stu-id="6fa61-347">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="6fa61-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments>-permite ler as entradas para um método de ação.</span><span class="sxs-lookup"><span data-stu-id="6fa61-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables reading the inputs to an action method.</span></span>
* <span data-ttu-id="6fa61-349"><xref:Microsoft.AspNetCore.Mvc.Controller> – permite a manipulação da instância do controlador.</span><span class="sxs-lookup"><span data-stu-id="6fa61-349"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="6fa61-350"><xref:System.Web.Mvc.ActionExecutingContext.Result> – configuração de `Result` execução de curtos-circuitos do método de ação e dos filtros de ação posteriores.</span><span class="sxs-lookup"><span data-stu-id="6fa61-350"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="6fa61-351">Gerar uma exceção em um método de ação:</span><span class="sxs-lookup"><span data-stu-id="6fa61-351">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="6fa61-352">Impede a execução de filtros subsequentes.</span><span class="sxs-lookup"><span data-stu-id="6fa61-352">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="6fa61-353">Ao contrário da configuração `Result`, é tratada como uma falha e não como um resultado bem-sucedido.</span><span class="sxs-lookup"><span data-stu-id="6fa61-353">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="6fa61-354">A classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> fornece `Controller` e `Result`, além das seguintes propriedades:</span><span class="sxs-lookup"><span data-stu-id="6fa61-354">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="6fa61-355"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> – verdadeiro se a execução da ação tiver sofrido curto-circuito por outro filtro.</span><span class="sxs-lookup"><span data-stu-id="6fa61-355"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="6fa61-356"><xref:System.Web.Mvc.ActionExecutedContext.Exception> – não será nulo se a ação ou um filtro de ação executado antes tiver apresentado uma exceção.</span><span class="sxs-lookup"><span data-stu-id="6fa61-356"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="6fa61-357">Definir essa propriedade como nulo:</span><span class="sxs-lookup"><span data-stu-id="6fa61-357">Setting this property to null:</span></span>

  * <span data-ttu-id="6fa61-358">Trata efetivamente a exceção.</span><span class="sxs-lookup"><span data-stu-id="6fa61-358">Effectively handles the exception.</span></span>
  * <span data-ttu-id="6fa61-359">`Result` é executado como se tivesse retornado do método de ação.</span><span class="sxs-lookup"><span data-stu-id="6fa61-359">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="6fa61-360">Para um `IAsyncActionFilter`, uma chamada para o <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span><span class="sxs-lookup"><span data-stu-id="6fa61-360">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="6fa61-361">Executa todos os próximos filtros de ação e o método de ação.</span><span class="sxs-lookup"><span data-stu-id="6fa61-361">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="6fa61-362">Retorna `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="6fa61-362">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="6fa61-363">Para fazer um curto-circuito, atribua <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> a uma instância de resultado e não chame o `next` (`ActionExecutionDelegate`).</span><span class="sxs-lookup"><span data-stu-id="6fa61-363">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="6fa61-364">A estrutura fornece um <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> abstrato que você pode colocar em uma subclasse.</span><span class="sxs-lookup"><span data-stu-id="6fa61-364">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="6fa61-365">O filtro de ação `OnActionExecuting` pode ser usado para:</span><span class="sxs-lookup"><span data-stu-id="6fa61-365">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="6fa61-366">Validar o estado do modelo.</span><span class="sxs-lookup"><span data-stu-id="6fa61-366">Validate model state.</span></span>
* <span data-ttu-id="6fa61-367">Retornar um erro se o estado for inválido.</span><span class="sxs-lookup"><span data-stu-id="6fa61-367">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="6fa61-368">O método `OnActionExecuted` é executado após o método de ação:</span><span class="sxs-lookup"><span data-stu-id="6fa61-368">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="6fa61-369">E pode ver e manipular os resultados da ação por meio da propriedade <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result>.</span><span class="sxs-lookup"><span data-stu-id="6fa61-369">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="6fa61-370"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> é definido como verdadeiro se a execução da ação tiver sofrido curto-circuito por outro filtro.</span><span class="sxs-lookup"><span data-stu-id="6fa61-370"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="6fa61-371"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> é definido como um valor não nulo se a ação ou um filtro de ação posterior tiver apresentado uma exceção.</span><span class="sxs-lookup"><span data-stu-id="6fa61-371"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="6fa61-372">Definindo `Exception` como nulo:</span><span class="sxs-lookup"><span data-stu-id="6fa61-372">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="6fa61-373">Trata efetivamente uma exceção.</span><span class="sxs-lookup"><span data-stu-id="6fa61-373">Effectively handles an exception.</span></span>
  * <span data-ttu-id="6fa61-374">`ActionExecutedContext.Result` é executado como se fosse retornado normalmente do método de ação.</span><span class="sxs-lookup"><span data-stu-id="6fa61-374">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="6fa61-375">Filtros de exceção</span><span class="sxs-lookup"><span data-stu-id="6fa61-375">Exception filters</span></span>

<span data-ttu-id="6fa61-376">Filtros de exceção:</span><span class="sxs-lookup"><span data-stu-id="6fa61-376">Exception filters:</span></span>

* <span data-ttu-id="6fa61-377">Implementam <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span><span class="sxs-lookup"><span data-stu-id="6fa61-377">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span>
* <span data-ttu-id="6fa61-378">Podem ser usados para implementar políticas de tratamento de erro comuns.</span><span class="sxs-lookup"><span data-stu-id="6fa61-378">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="6fa61-379">O exemplo de filtro de exceção a seguir usa uma exibição de erro personalizada para mostrar detalhes sobre exceções que ocorrem quando o aplicativo está em desenvolvimento:</span><span class="sxs-lookup"><span data-stu-id="6fa61-379">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="6fa61-380">O código a seguir testa o filtro de exceção:</span><span class="sxs-lookup"><span data-stu-id="6fa61-380">The following code tests the exception filter:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

<span data-ttu-id="6fa61-381">Filtros de exceção:</span><span class="sxs-lookup"><span data-stu-id="6fa61-381">Exception filters:</span></span>

* <span data-ttu-id="6fa61-382">Não têm eventos anteriores nem posteriores.</span><span class="sxs-lookup"><span data-stu-id="6fa61-382">Don't have before and after events.</span></span>
* <span data-ttu-id="6fa61-383">Implementam <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span><span class="sxs-lookup"><span data-stu-id="6fa61-383">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="6fa61-384">Tratam as exceções sem tratamento que ocorrem no Razor Page ou na criação do controlador, no [model binding](xref:mvc/models/model-binding), nos filtros de ação ou nos métodos de ação.</span><span class="sxs-lookup"><span data-stu-id="6fa61-384">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="6fa61-385">**Não** capturam as exceções que ocorrem em filtros de recurso, em filtros de resultado ou na execução do resultado de MVC.</span><span class="sxs-lookup"><span data-stu-id="6fa61-385">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="6fa61-386">Para tratar uma exceção, defina a propriedade <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> como `true` ou grave uma resposta.</span><span class="sxs-lookup"><span data-stu-id="6fa61-386">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="6fa61-387">Isso interrompe a propagação da exceção.</span><span class="sxs-lookup"><span data-stu-id="6fa61-387">This stops propagation of the exception.</span></span> <span data-ttu-id="6fa61-388">Um filtro de exceção não pode transformar uma exceção em "êxito".</span><span class="sxs-lookup"><span data-stu-id="6fa61-388">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="6fa61-389">Somente um filtro de ação pode fazer isso.</span><span class="sxs-lookup"><span data-stu-id="6fa61-389">Only an action filter can do that.</span></span>

<span data-ttu-id="6fa61-390">Filtros de exceção:</span><span class="sxs-lookup"><span data-stu-id="6fa61-390">Exception filters:</span></span>

* <span data-ttu-id="6fa61-391">São bons para interceptar as exceções que ocorrem nas ações.</span><span class="sxs-lookup"><span data-stu-id="6fa61-391">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="6fa61-392">Não são tão flexíveis quanto o middleware de tratamento de erro.</span><span class="sxs-lookup"><span data-stu-id="6fa61-392">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="6fa61-393">Prefira o middleware para tratamento de exceção.</span><span class="sxs-lookup"><span data-stu-id="6fa61-393">Prefer middleware for exception handling.</span></span> <span data-ttu-id="6fa61-394">Use filtros de exceção apenas quando o tratamento de erros *for diferente* com base no método de ação chamado.</span><span class="sxs-lookup"><span data-stu-id="6fa61-394">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="6fa61-395">Por exemplo, um aplicativo pode ter métodos de ação para os pontos de extremidade da API e para modos de exibição/HTML.</span><span class="sxs-lookup"><span data-stu-id="6fa61-395">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="6fa61-396">Os pontos de extremidade da API podem retornar informações de erro como JSON, enquanto as ações baseadas em modo de exibição podem retornar uma página de erro como HTML.</span><span class="sxs-lookup"><span data-stu-id="6fa61-396">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="6fa61-397">Filtros de resultado</span><span class="sxs-lookup"><span data-stu-id="6fa61-397">Result filters</span></span>

<span data-ttu-id="6fa61-398">Filtros de resultado:</span><span class="sxs-lookup"><span data-stu-id="6fa61-398">Result filters:</span></span>

* <span data-ttu-id="6fa61-399">Implemente uma interface:</span><span class="sxs-lookup"><span data-stu-id="6fa61-399">Implement an interface:</span></span>
  * <span data-ttu-id="6fa61-400"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="6fa61-400"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="6fa61-401"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="6fa61-401"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="6fa61-402">A execução deles envolve a execução de resultados de ação.</span><span class="sxs-lookup"><span data-stu-id="6fa61-402">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="6fa61-403">IResultFilter e IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="6fa61-403">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="6fa61-404">O código a seguir mostra um filtro de resultado que adiciona um cabeçalho HTTP:</span><span class="sxs-lookup"><span data-stu-id="6fa61-404">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="6fa61-405">O tipo de resultado que está sendo executado depende da ação.</span><span class="sxs-lookup"><span data-stu-id="6fa61-405">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="6fa61-406">Uma ação que retorna uma exibição inclui todo o processamento do Razor como parte do <xref:Microsoft.AspNetCore.Mvc.ViewResult> que está sendo executado.</span><span class="sxs-lookup"><span data-stu-id="6fa61-406">An action returning a view includes all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="6fa61-407">Um método de API pode executar alguma serialização como parte da execução do resultado.</span><span class="sxs-lookup"><span data-stu-id="6fa61-407">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="6fa61-408">Saiba mais sobre [os resultados da ação](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="6fa61-408">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="6fa61-409">Os filtros de resultado são executados somente quando um filtro de ação ou Action produz um resultado de ação.</span><span class="sxs-lookup"><span data-stu-id="6fa61-409">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="6fa61-410">Os filtros de resultado não são executados quando:</span><span class="sxs-lookup"><span data-stu-id="6fa61-410">Result filters are not executed when:</span></span>

* <span data-ttu-id="6fa61-411">Um filtro de autorização ou filtro de recursos de curto-circuitos do pipeline.</span><span class="sxs-lookup"><span data-stu-id="6fa61-411">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="6fa61-412">Um filtro de exceção trata uma exceção produzindo um resultado de ação.</span><span class="sxs-lookup"><span data-stu-id="6fa61-412">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="6fa61-413">O método <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> pode fazer o curto-circuito da execução do resultado da ação e dos filtros de resultados posteriores definindo <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> como `true`.</span><span class="sxs-lookup"><span data-stu-id="6fa61-413">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="6fa61-414">Grave no objeto de resposta ao fazer um curto-circuito para evitar gerar uma resposta vazia.</span><span class="sxs-lookup"><span data-stu-id="6fa61-414">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="6fa61-415">Gerando uma exceção no `IResultFilter.OnResultExecuting`:</span><span class="sxs-lookup"><span data-stu-id="6fa61-415">Throwing an exception in `IResultFilter.OnResultExecuting`:</span></span>

* <span data-ttu-id="6fa61-416">Impede a execução do resultado da ação e dos filtros subsequentes.</span><span class="sxs-lookup"><span data-stu-id="6fa61-416">Prevents execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="6fa61-417">É tratado como uma falha em vez de um resultado bem-sucedido.</span><span class="sxs-lookup"><span data-stu-id="6fa61-417">Is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="6fa61-418">Quando o método <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> é executado, a resposta provavelmente já foi enviada ao cliente.</span><span class="sxs-lookup"><span data-stu-id="6fa61-418">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has probably already been sent to the client.</span></span> <span data-ttu-id="6fa61-419">Se a resposta já tiver sido enviada ao cliente, ela não poderá ser alterada.</span><span class="sxs-lookup"><span data-stu-id="6fa61-419">If the response has already been sent to the client, it cannot be changed.</span></span>

<span data-ttu-id="6fa61-420">`ResultExecutedContext.Canceled` será definido como `true` se a execução do resultado da ação tiver sofrido curto-circuito por outro filtro.</span><span class="sxs-lookup"><span data-stu-id="6fa61-420">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="6fa61-421">`ResultExecutedContext.Exception` será definido como um valor não nulo se o resultado da ação ou um filtro de resultado posterior tiver gerado uma exceção.</span><span class="sxs-lookup"><span data-stu-id="6fa61-421">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="6fa61-422">A definição de `Exception` como NULL trata efetivamente de uma exceção e impede que a exceção seja gerada novamente mais tarde no pipeline.</span><span class="sxs-lookup"><span data-stu-id="6fa61-422">Setting `Exception` to null effectively handles an exception and prevents the exception from being thrown again later in the pipeline.</span></span> <span data-ttu-id="6fa61-423">Não há nenhuma maneira confiável para gravar dados em uma resposta ao manipular uma exceção em um filtro de resultado.</span><span class="sxs-lookup"><span data-stu-id="6fa61-423">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="6fa61-424">Se os cabeçalhos tiverem sido liberados para o cliente quando o resultado de uma ação gerar uma exceção, não haverá mecanismo confiável para enviar um código de falha.</span><span class="sxs-lookup"><span data-stu-id="6fa61-424">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="6fa61-425">Para um <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, uma chamada para `await next` no <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executa qualquer filtro de resultado posterior e o resultado da ação.</span><span class="sxs-lookup"><span data-stu-id="6fa61-425">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="6fa61-426">Para causar um curto-circuito, defina [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) como `true` e não chame o `ResultExecutionDelegate`:</span><span class="sxs-lookup"><span data-stu-id="6fa61-426">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="6fa61-427">A estrutura fornece um `ResultFilterAttribute` abstrato que você pode colocar em uma subclasse.</span><span class="sxs-lookup"><span data-stu-id="6fa61-427">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="6fa61-428">A classe [AddHeaderAttribute](#add-header-attribute) mostrada anteriormente é um exemplo de atributo de filtro de resultado.</span><span class="sxs-lookup"><span data-stu-id="6fa61-428">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="6fa61-429">IAlwaysRunResultFilter e IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="6fa61-429">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="6fa61-430">As interfaces <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> e <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> declaram uma implementação <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> que é executada para todos os resultados da ação.</span><span class="sxs-lookup"><span data-stu-id="6fa61-430">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="6fa61-431">Isso inclui resultados de ação produzidos por:</span><span class="sxs-lookup"><span data-stu-id="6fa61-431">This includes action results produced by:</span></span>

* <span data-ttu-id="6fa61-432">Filtros de autorização e filtros de recursos que curto-Circuit.</span><span class="sxs-lookup"><span data-stu-id="6fa61-432">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="6fa61-433">Filtros de exceção.</span><span class="sxs-lookup"><span data-stu-id="6fa61-433">Exception filters.</span></span>

<span data-ttu-id="6fa61-434">Por exemplo, o filtro a seguir sempre é executado e define o resultado de uma ação (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) com um código de status *422 Entidade Não Processável* quando ocorre falha na negociação de conteúdo:</span><span class="sxs-lookup"><span data-stu-id="6fa61-434">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="6fa61-435">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="6fa61-435">IFilterFactory</span></span>

<span data-ttu-id="6fa61-436"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="6fa61-436"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="6fa61-437">Portanto, uma instância `IFilterFactory` pode ser usada como uma instância `IFilterMetadata` em qualquer parte do pipeline de filtro.</span><span class="sxs-lookup"><span data-stu-id="6fa61-437">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="6fa61-438">Quando o runtime se prepara para invocar o filtro, tenta convertê-lo em um `IFilterFactory`.</span><span class="sxs-lookup"><span data-stu-id="6fa61-438">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="6fa61-439">Se essa conversão for bem-sucedida, o método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> será chamado para criar a instância `IFilterMetadata` invocada.</span><span class="sxs-lookup"><span data-stu-id="6fa61-439">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="6fa61-440">Isso fornece um design flexível, porque o pipeline de filtro preciso não precisa ser definido explicitamente quando o aplicativo é iniciado.</span><span class="sxs-lookup"><span data-stu-id="6fa61-440">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="6fa61-441">Implemente `IFilterFactory` usando implementações personalizadas de atributo como outra abordagem à criação de filtros:</span><span class="sxs-lookup"><span data-stu-id="6fa61-441">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="6fa61-442">O filtro é aplicado no código a seguir:</span><span class="sxs-lookup"><span data-stu-id="6fa61-442">The filter is applied in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

<span data-ttu-id="6fa61-443">Teste o código anterior executando o [exemplo de download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span><span class="sxs-lookup"><span data-stu-id="6fa61-443">Test the preceding code by running the [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span></span>

* <span data-ttu-id="6fa61-444">Invocar as ferramentas de desenvolvedor F12.</span><span class="sxs-lookup"><span data-stu-id="6fa61-444">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="6fa61-445">Navegue até `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="6fa61-445">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="6fa61-446">As ferramentas de desenvolvedor F12 exibem os seguintes cabeçalhos de resposta adicionados pelo código de exemplo:</span><span class="sxs-lookup"><span data-stu-id="6fa61-446">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="6fa61-447">**autor:** `Rick Anderson`</span><span class="sxs-lookup"><span data-stu-id="6fa61-447">**author:** `Rick Anderson`</span></span>
* <span data-ttu-id="6fa61-448">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="6fa61-448">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="6fa61-449">**interno:** `My header`</span><span class="sxs-lookup"><span data-stu-id="6fa61-449">**internal:** `My header`</span></span>

<span data-ttu-id="6fa61-450">O código anterior cria o cabeçalho de resposta **interno:** `My header`.</span><span class="sxs-lookup"><span data-stu-id="6fa61-450">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="6fa61-451">IFilterFactory implementado em um atributo</span><span class="sxs-lookup"><span data-stu-id="6fa61-451">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="6fa61-452">Filtros que implementam `IFilterFactory` são úteis para filtros que:</span><span class="sxs-lookup"><span data-stu-id="6fa61-452">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="6fa61-453">Não exigem a passagem de parâmetros.</span><span class="sxs-lookup"><span data-stu-id="6fa61-453">Don't require passing parameters.</span></span>
* <span data-ttu-id="6fa61-454">Tenha dependências de construtor que precisem ser atendidas pela DI.</span><span class="sxs-lookup"><span data-stu-id="6fa61-454">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="6fa61-455"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="6fa61-455"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="6fa61-456">`IFilterFactory` expõe o método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> para criar uma instância de <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="6fa61-456">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="6fa61-457">`CreateInstance` carrega o tipo especificado do contêiner de serviços (DI).</span><span class="sxs-lookup"><span data-stu-id="6fa61-457">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="6fa61-458">O código a seguir mostra três abordagens para aplicar o `[SampleActionFilter]`:</span><span class="sxs-lookup"><span data-stu-id="6fa61-458">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="6fa61-459">No código anterior, decorar o método com `[SampleActionFilter]` é a abordagem preferida para aplicar o `SampleActionFilter`.</span><span class="sxs-lookup"><span data-stu-id="6fa61-459">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="6fa61-460">Usando middleware no pipeline de filtros</span><span class="sxs-lookup"><span data-stu-id="6fa61-460">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="6fa61-461">Filtros de recursos funcionam como [middleware](xref:fundamentals/middleware/index), no sentido em que envolvem a execução de tudo o que vem depois no pipeline.</span><span class="sxs-lookup"><span data-stu-id="6fa61-461">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="6fa61-462">Mas os filtros diferem do middleware, pois fazem parte do tempo de execução, o que significa que eles têm acesso ao contexto e às construções.</span><span class="sxs-lookup"><span data-stu-id="6fa61-462">But filters differ from middleware in that they're part of the runtime, which means that they have access to context and constructs.</span></span>

<span data-ttu-id="6fa61-463">Para usar o middleware como um filtro, crie um tipo com um método `Configure` que especifica o middleware para injeção no pipeline de filtros.</span><span class="sxs-lookup"><span data-stu-id="6fa61-463">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="6fa61-464">O exemplo a seguir usa o middleware de localização para estabelecer a cultura atual para uma solicitação:</span><span class="sxs-lookup"><span data-stu-id="6fa61-464">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="6fa61-465">Use <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> para executar o middleware:</span><span class="sxs-lookup"><span data-stu-id="6fa61-465">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="6fa61-466">Filtros de middleware são executados no mesmo estágio do pipeline de filtros que filtros de recurso, antes do model binding e depois do restante do pipeline.</span><span class="sxs-lookup"><span data-stu-id="6fa61-466">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="6fa61-467">Próximas ações</span><span class="sxs-lookup"><span data-stu-id="6fa61-467">Next actions</span></span>

* <span data-ttu-id="6fa61-468">Consulte [métodos de filtro para Razor Pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="6fa61-468">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="6fa61-469">Para fazer experiências com filtros, [baixe, teste e modifique o exemplo do GitHub](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span><span class="sxs-lookup"><span data-stu-id="6fa61-469">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6fa61-470">Por [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="6fa61-470">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="6fa61-471">Os *Filtros* no ASP.NET Core permitem a execução de código antes ou depois de determinados estágios do pipeline de processamento de solicitações.</span><span class="sxs-lookup"><span data-stu-id="6fa61-471">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="6fa61-472">O filtros internos lidam com tarefas como:</span><span class="sxs-lookup"><span data-stu-id="6fa61-472">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="6fa61-473">Autorização (impedir o acesso a recursos aos quais o usuário não está autorizado).</span><span class="sxs-lookup"><span data-stu-id="6fa61-473">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="6fa61-474">Cache de resposta (causar um curto-circuito do pipeline de solicitação para retornar uma resposta armazenada em cache).</span><span class="sxs-lookup"><span data-stu-id="6fa61-474">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="6fa61-475">É possível criar filtros personalizados para lidar com interesses paralelos.</span><span class="sxs-lookup"><span data-stu-id="6fa61-475">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="6fa61-476">Entre os exemplos de interesses paralelos estão o tratamento de erros, cache, configuração, autorização e registro em log.</span><span class="sxs-lookup"><span data-stu-id="6fa61-476">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="6fa61-477">Filtros evitam a duplicação do código.</span><span class="sxs-lookup"><span data-stu-id="6fa61-477">Filters avoid duplicating code.</span></span> <span data-ttu-id="6fa61-478">Por exemplo, um filtro de exceção de tratamento de erro poderia consolidar o tratamento de erro.</span><span class="sxs-lookup"><span data-stu-id="6fa61-478">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="6fa61-479">Este documento se aplica ao Razor Pages, a controladores de API e controladores com exibição.</span><span class="sxs-lookup"><span data-stu-id="6fa61-479">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="6fa61-480">[Exibir ou baixar exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="6fa61-480">[View or download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="6fa61-481">Como os filtros funcionam</span><span class="sxs-lookup"><span data-stu-id="6fa61-481">How filters work</span></span>

<span data-ttu-id="6fa61-482">Os filtros são executados dentro do *pipeline de invocação de ações do ASP.NET Core*, às vezes chamado de *pipeline de filtros*.</span><span class="sxs-lookup"><span data-stu-id="6fa61-482">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="6fa61-483">O pipeline de filtros é executado após o ASP.NET Core selecionar a ação a ser executada.</span><span class="sxs-lookup"><span data-stu-id="6fa61-483">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![A solicitação é processada por meio de Outro Middleware, do Middleware de Roteamento, da Seleção de Ação e do Pipeline de Invocação de Ações do ASP.NET Core.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="6fa61-486">Tipos de filtro</span><span class="sxs-lookup"><span data-stu-id="6fa61-486">Filter types</span></span>

<span data-ttu-id="6fa61-487">Cada tipo de filtro é executado em um estágio diferente no pipeline de filtros:</span><span class="sxs-lookup"><span data-stu-id="6fa61-487">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="6fa61-488">[Filtros de autorização](#authorization-filters) são executados primeiro e são usados para determinar se o usuário tem autorização para a solicitação.</span><span class="sxs-lookup"><span data-stu-id="6fa61-488">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="6fa61-489">Os filtros de autorização podem causar um curto-circuito do pipeline se uma solicitação não for autorizada.</span><span class="sxs-lookup"><span data-stu-id="6fa61-489">Authorization filters short-circuit the pipeline if the request is unauthorized.</span></span>

* <span data-ttu-id="6fa61-490">[Filtros de recurso](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="6fa61-490">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="6fa61-491">Execute após a autorização.</span><span class="sxs-lookup"><span data-stu-id="6fa61-491">Run after authorization.</span></span>  
  * <span data-ttu-id="6fa61-492"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> pode executar o código antes do restante do pipeline de filtros.</span><span class="sxs-lookup"><span data-stu-id="6fa61-492"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> can run code before the rest of the filter pipeline.</span></span> <span data-ttu-id="6fa61-493">Por exemplo, `OnResourceExecuting` pode executar o código antes do model binding.</span><span class="sxs-lookup"><span data-stu-id="6fa61-493">For example, `OnResourceExecuting` can run code before model binding.</span></span>
  * <span data-ttu-id="6fa61-494"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> pode executar o código após a conclusão do restante do pipeline.</span><span class="sxs-lookup"><span data-stu-id="6fa61-494"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> can run code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="6fa61-495">[Filtros de ação](#action-filters) podem executar código imediatamente antes e depois de um método de ação individual ser chamado.</span><span class="sxs-lookup"><span data-stu-id="6fa61-495">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="6fa61-496">Eles podem ser usados para manipular os argumentos passados para uma ação, bem como o resultado da ação.</span><span class="sxs-lookup"><span data-stu-id="6fa61-496">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span> <span data-ttu-id="6fa61-497">Os filtros de ação **não** são compatíveis com o Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="6fa61-497">Action filters are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="6fa61-498">[Filtros de exceção](#exception-filters) são usados para aplicar políticas globais para exceções sem tratamento que ocorrem antes que qualquer coisa tenha sido gravada no corpo da resposta.</span><span class="sxs-lookup"><span data-stu-id="6fa61-498">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="6fa61-499">[Filtros de resposta](#result-filters) podem executar código imediatamente antes e depois da execução de resultados de ações individuais.</span><span class="sxs-lookup"><span data-stu-id="6fa61-499">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="6fa61-500">Eles são executados somente quando o método de ação é executado com êxito.</span><span class="sxs-lookup"><span data-stu-id="6fa61-500">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="6fa61-501">Eles são úteis para a lógica que precisa envolver a execução da exibição ou do formatador.</span><span class="sxs-lookup"><span data-stu-id="6fa61-501">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="6fa61-502">O diagrama a seguir mostra como os tipos de filtro interagem no pipeline de filtros.</span><span class="sxs-lookup"><span data-stu-id="6fa61-502">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![A solicitação é processada por meio de Filtros de autorização, Filtros de recurso, Model binding, Filtros de ação, Execução de ação e Conversão do resultado de ação, Filtros de exceção, Filtros de resultado e Execução de resultado.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="6fa61-505">Implementação</span><span class="sxs-lookup"><span data-stu-id="6fa61-505">Implementation</span></span>

<span data-ttu-id="6fa61-506">Os filtros dão suporte a implementações síncronas e assíncronas por meio de diferentes definições de interface.</span><span class="sxs-lookup"><span data-stu-id="6fa61-506">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="6fa61-507">Os filtros síncronos podem executar o código antes (`On-Stage-Executing`) e depois (`On-Stage-Executed`) do estágio de pipeline.</span><span class="sxs-lookup"><span data-stu-id="6fa61-507">Synchronous filters can run code before (`On-Stage-Executing`) and after (`On-Stage-Executed`) their pipeline stage.</span></span> <span data-ttu-id="6fa61-508">Por exemplo, `OnActionExecuting` é chamado antes que o método de ação seja chamado.</span><span class="sxs-lookup"><span data-stu-id="6fa61-508">For example, `OnActionExecuting` is called before the action method is called.</span></span> <span data-ttu-id="6fa61-509">`OnActionExecuted` é chamado após o método de ação retornar.</span><span class="sxs-lookup"><span data-stu-id="6fa61-509">`OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="6fa61-510">Os filtros assíncronos definem um método `On-Stage-ExecutionAsync`:</span><span class="sxs-lookup"><span data-stu-id="6fa61-510">Asynchronous filters define an `On-Stage-ExecutionAsync` method:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="6fa61-511">No código anterior, o `SampleAsyncActionFilter` tem um <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) que executa o método de ação.</span><span class="sxs-lookup"><span data-stu-id="6fa61-511">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)  that executes the action method.</span></span>  <span data-ttu-id="6fa61-512">Cada um dos métodos `On-Stage-ExecutionAsync` usa um `FilterType-ExecutionDelegate` que executa o estágio de pipeline do filtro.</span><span class="sxs-lookup"><span data-stu-id="6fa61-512">Each of the `On-Stage-ExecutionAsync` methods take a `FilterType-ExecutionDelegate` that executes the filter's pipeline stage.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="6fa61-513">Vários estágios do filtro</span><span class="sxs-lookup"><span data-stu-id="6fa61-513">Multiple filter stages</span></span>

<span data-ttu-id="6fa61-514">É possível implementar interfaces para vários estágios do filtro em uma única classe.</span><span class="sxs-lookup"><span data-stu-id="6fa61-514">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="6fa61-515">Por exemplo, a classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> implementa `IActionFilter`, `IResultFilter` e os respectivos equivalentes assíncronos.</span><span class="sxs-lookup"><span data-stu-id="6fa61-515">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

<span data-ttu-id="6fa61-516">Implemente **ou** a versão assíncrona ou a versão síncrona de uma interface de filtro, **não** ambas.</span><span class="sxs-lookup"><span data-stu-id="6fa61-516">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="6fa61-517">Primeiro, o runtime verifica se o filtro implementa a interface assíncrona e, se for esse o caso, a chama.</span><span class="sxs-lookup"><span data-stu-id="6fa61-517">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="6fa61-518">Caso contrário, ela chama os métodos da interface síncrona.</span><span class="sxs-lookup"><span data-stu-id="6fa61-518">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="6fa61-519">Se as interfaces síncrona e assíncrona forem implementadas em uma classe, somente o método assíncrono será chamado.</span><span class="sxs-lookup"><span data-stu-id="6fa61-519">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="6fa61-520">Ao usar classes abstratas como <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, substitua apenas os métodos síncronos ou o método assíncrono para cada tipo de filtro.</span><span class="sxs-lookup"><span data-stu-id="6fa61-520">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="6fa61-521">Atributos de filtro internos</span><span class="sxs-lookup"><span data-stu-id="6fa61-521">Built-in filter attributes</span></span>

<span data-ttu-id="6fa61-522">O ASP.NET Core inclui filtros internos baseados em atributos que podem ser organizados em subclasses e personalizados.</span><span class="sxs-lookup"><span data-stu-id="6fa61-522">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="6fa61-523">Por exemplo, o filtro de resultado a seguir adiciona um cabeçalho à resposta:</span><span class="sxs-lookup"><span data-stu-id="6fa61-523">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="6fa61-524">Os atributos permitem que os filtros aceitem argumentos, conforme mostrado no exemplo acima.</span><span class="sxs-lookup"><span data-stu-id="6fa61-524">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="6fa61-525">Aplique o `AddHeaderAttribute` a um controlador ou método de ação e especifique o nome e o valor do cabeçalho HTTP:</span><span class="sxs-lookup"><span data-stu-id="6fa61-525">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

<span data-ttu-id="6fa61-526">Várias interfaces de filtro têm atributos correspondentes que podem ser usados como classes base para implementações personalizadas.</span><span class="sxs-lookup"><span data-stu-id="6fa61-526">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="6fa61-527">Atributos de filtro:</span><span class="sxs-lookup"><span data-stu-id="6fa61-527">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="6fa61-528">Escopos e ordem de execução dos filtros</span><span class="sxs-lookup"><span data-stu-id="6fa61-528">Filter scopes and order of execution</span></span>

<span data-ttu-id="6fa61-529">Um filtro pode ser adicionado ao pipeline com um de três *escopos*:</span><span class="sxs-lookup"><span data-stu-id="6fa61-529">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="6fa61-530">Usando um atributo em uma ação.</span><span class="sxs-lookup"><span data-stu-id="6fa61-530">Using an attribute on an action.</span></span>
* <span data-ttu-id="6fa61-531">Usando um atributo em um controlador.</span><span class="sxs-lookup"><span data-stu-id="6fa61-531">Using an attribute on a controller.</span></span>
* <span data-ttu-id="6fa61-532">Globalmente para todos os controladores e ações, como mostra o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="6fa61-532">Globally for all controllers and actions as shown in the following code:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="6fa61-533">O código anterior adiciona três filtros globalmente usando a coleção [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters).</span><span class="sxs-lookup"><span data-stu-id="6fa61-533">The preceding code adds three filters globally using the [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) collection.</span></span>

### <a name="default-order-of-execution"></a><span data-ttu-id="6fa61-534">Ordem padrão de execução</span><span class="sxs-lookup"><span data-stu-id="6fa61-534">Default order of execution</span></span>

<span data-ttu-id="6fa61-535">Quando há vários filtros *do mesmo tipo*, o escopo determina a ordem padrão de execução do filtro.</span><span class="sxs-lookup"><span data-stu-id="6fa61-535">When there are multiple filters *of the same type*, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="6fa61-536">Filtros globais envolvem filtros de classe.</span><span class="sxs-lookup"><span data-stu-id="6fa61-536">Global filters surround class filters.</span></span> <span data-ttu-id="6fa61-537">Filtros de classe envolvem filtros de método.</span><span class="sxs-lookup"><span data-stu-id="6fa61-537">Class filters surround method filters.</span></span>

<span data-ttu-id="6fa61-538">Como resultado do aninhamento de filtro, o código *posterior* dos filtros é executado na ordem inversa do código *anterior*.</span><span class="sxs-lookup"><span data-stu-id="6fa61-538">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="6fa61-539">A sequência de filtro:</span><span class="sxs-lookup"><span data-stu-id="6fa61-539">The filter sequence:</span></span>

* <span data-ttu-id="6fa61-540">O código *anterior* dos filtros globais.</span><span class="sxs-lookup"><span data-stu-id="6fa61-540">The *before* code of global filters.</span></span>
  * <span data-ttu-id="6fa61-541">O código *anterior* dos filtros de controlador.</span><span class="sxs-lookup"><span data-stu-id="6fa61-541">The *before* code of controller filters.</span></span>
    * <span data-ttu-id="6fa61-542">O código *anterior* dos filtros de método de ação.</span><span class="sxs-lookup"><span data-stu-id="6fa61-542">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="6fa61-543">O código *posterior* dos filtros de método de ação.</span><span class="sxs-lookup"><span data-stu-id="6fa61-543">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="6fa61-544">O código *posterior* dos filtros de controlador.</span><span class="sxs-lookup"><span data-stu-id="6fa61-544">The *after* code of controller filters.</span></span>
* <span data-ttu-id="6fa61-545">O código *posterior* dos filtros globais.</span><span class="sxs-lookup"><span data-stu-id="6fa61-545">The *after* code of global filters.</span></span>
  
<span data-ttu-id="6fa61-546">O exemplo a seguir ilustra a ordem na qual os métodos de filtro são chamados para filtros de ação síncrona.</span><span class="sxs-lookup"><span data-stu-id="6fa61-546">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="6fa61-547">Sequência</span><span class="sxs-lookup"><span data-stu-id="6fa61-547">Sequence</span></span> | <span data-ttu-id="6fa61-548">Escopo do filtro</span><span class="sxs-lookup"><span data-stu-id="6fa61-548">Filter scope</span></span> | <span data-ttu-id="6fa61-549">Método Filter</span><span class="sxs-lookup"><span data-stu-id="6fa61-549">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="6fa61-550">1</span><span class="sxs-lookup"><span data-stu-id="6fa61-550">1</span></span> | <span data-ttu-id="6fa61-551">Global</span><span class="sxs-lookup"><span data-stu-id="6fa61-551">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="6fa61-552">2</span><span class="sxs-lookup"><span data-stu-id="6fa61-552">2</span></span> | <span data-ttu-id="6fa61-553">Controller</span><span class="sxs-lookup"><span data-stu-id="6fa61-553">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="6fa61-554">3</span><span class="sxs-lookup"><span data-stu-id="6fa61-554">3</span></span> | <span data-ttu-id="6fa61-555">Método</span><span class="sxs-lookup"><span data-stu-id="6fa61-555">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="6fa61-556">4</span><span class="sxs-lookup"><span data-stu-id="6fa61-556">4</span></span> | <span data-ttu-id="6fa61-557">Método</span><span class="sxs-lookup"><span data-stu-id="6fa61-557">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="6fa61-558">5</span><span class="sxs-lookup"><span data-stu-id="6fa61-558">5</span></span> | <span data-ttu-id="6fa61-559">Controller</span><span class="sxs-lookup"><span data-stu-id="6fa61-559">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="6fa61-560">6</span><span class="sxs-lookup"><span data-stu-id="6fa61-560">6</span></span> | <span data-ttu-id="6fa61-561">Global</span><span class="sxs-lookup"><span data-stu-id="6fa61-561">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="6fa61-562">Esta sequência mostra:</span><span class="sxs-lookup"><span data-stu-id="6fa61-562">This sequence shows:</span></span>

* <span data-ttu-id="6fa61-563">O filtro de método está aninhado no filtro de controlador.</span><span class="sxs-lookup"><span data-stu-id="6fa61-563">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="6fa61-564">O filtro de controlador está aninhado no filtro global.</span><span class="sxs-lookup"><span data-stu-id="6fa61-564">The controller filter is nested within the global filter.</span></span>

### <a name="controller-and-razor-page-level-filters"></a><span data-ttu-id="6fa61-565">Filtros no nível do controlador e do Razor Page</span><span class="sxs-lookup"><span data-stu-id="6fa61-565">Controller and Razor Page level filters</span></span>

<span data-ttu-id="6fa61-566">Cada controlador que herda da classe base <xref:Microsoft.AspNetCore.Mvc.Controller> inclui os métodos [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) e [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="6fa61-566">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="6fa61-567">Estes métodos:</span><span class="sxs-lookup"><span data-stu-id="6fa61-567">These methods:</span></span>

* <span data-ttu-id="6fa61-568">Encapsulam os filtros executados para uma determinada ação.</span><span class="sxs-lookup"><span data-stu-id="6fa61-568">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="6fa61-569">`OnActionExecuting` é chamado antes de qualquer um dos filtros da ação.</span><span class="sxs-lookup"><span data-stu-id="6fa61-569">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="6fa61-570">`OnActionExecuted` é chamado após todos os filtros da ação.</span><span class="sxs-lookup"><span data-stu-id="6fa61-570">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="6fa61-571">`OnActionExecutionAsync` é chamado antes de qualquer um dos filtros da ação.</span><span class="sxs-lookup"><span data-stu-id="6fa61-571">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="6fa61-572">O código no filtro após `next` é executado após o método de ação.</span><span class="sxs-lookup"><span data-stu-id="6fa61-572">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="6fa61-573">Por exemplo, no download de exemplo, `MySampleActionFilter` é aplicado globalmente na inicialização.</span><span class="sxs-lookup"><span data-stu-id="6fa61-573">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="6fa61-574">`TestController`:</span><span class="sxs-lookup"><span data-stu-id="6fa61-574">The `TestController`:</span></span>

* <span data-ttu-id="6fa61-575">Aplica o `SampleActionFilterAttribute` (`[SampleActionFilter]`) à ação `FilterTest2`.</span><span class="sxs-lookup"><span data-stu-id="6fa61-575">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="6fa61-576">Substitui `OnActionExecuting` e `OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="6fa61-576">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="6fa61-577">Navegar até `https://localhost:5001/Test/FilterTest2` executa o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="6fa61-577">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="6fa61-578">Para Razor Pages, confira [Implementar filtros do Razor Page substituindo os métodos de filtro](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="6fa61-578">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="6fa61-579">Substituindo a ordem padrão</span><span class="sxs-lookup"><span data-stu-id="6fa61-579">Overriding the default order</span></span>

<span data-ttu-id="6fa61-580">É possível substituir a sequência padrão de execução implementando <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span><span class="sxs-lookup"><span data-stu-id="6fa61-580">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="6fa61-581">`IOrderedFilter` expõe a propriedade <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> que tem precedência sobre o escopo para determinar a ordem da execução.</span><span class="sxs-lookup"><span data-stu-id="6fa61-581">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="6fa61-582">Um filtro com um valor de `Order` menor:</span><span class="sxs-lookup"><span data-stu-id="6fa61-582">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="6fa61-583">Executa o código *anterior* antes de um filtro com um valor mais alto de `Order`.</span><span class="sxs-lookup"><span data-stu-id="6fa61-583">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="6fa61-584">Executa o código *posterior* após um filtro com um valor mais alto de `Order`.</span><span class="sxs-lookup"><span data-stu-id="6fa61-584">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="6fa61-585">A propriedade `Order` pode ser definida com um parâmetro de construtor:</span><span class="sxs-lookup"><span data-stu-id="6fa61-585">The `Order` property can be set with a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="6fa61-586">Considere os mesmos filtros de 3 ações mostrados no exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="6fa61-586">Consider the same 3 action filters shown in the preceding example.</span></span> <span data-ttu-id="6fa61-587">Se a propriedade `Order` do controlador e os filtros globais estiverem definidos como 1 e 2, respectivamente, a ordem de execução será invertida.</span><span class="sxs-lookup"><span data-stu-id="6fa61-587">If the `Order` property of the controller and global filters is set to 1 and 2 respectively, the order of execution is reversed.</span></span>

| <span data-ttu-id="6fa61-588">Sequência</span><span class="sxs-lookup"><span data-stu-id="6fa61-588">Sequence</span></span> | <span data-ttu-id="6fa61-589">Escopo do filtro</span><span class="sxs-lookup"><span data-stu-id="6fa61-589">Filter scope</span></span> | <span data-ttu-id="6fa61-590">Propriedade `Order`</span><span class="sxs-lookup"><span data-stu-id="6fa61-590">`Order` property</span></span> | <span data-ttu-id="6fa61-591">Método Filter</span><span class="sxs-lookup"><span data-stu-id="6fa61-591">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="6fa61-592">1</span><span class="sxs-lookup"><span data-stu-id="6fa61-592">1</span></span> | <span data-ttu-id="6fa61-593">Método</span><span class="sxs-lookup"><span data-stu-id="6fa61-593">Method</span></span> | <span data-ttu-id="6fa61-594">0</span><span class="sxs-lookup"><span data-stu-id="6fa61-594">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="6fa61-595">2</span><span class="sxs-lookup"><span data-stu-id="6fa61-595">2</span></span> | <span data-ttu-id="6fa61-596">Controller</span><span class="sxs-lookup"><span data-stu-id="6fa61-596">Controller</span></span> | <span data-ttu-id="6fa61-597">1</span><span class="sxs-lookup"><span data-stu-id="6fa61-597">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="6fa61-598">3</span><span class="sxs-lookup"><span data-stu-id="6fa61-598">3</span></span> | <span data-ttu-id="6fa61-599">Global</span><span class="sxs-lookup"><span data-stu-id="6fa61-599">Global</span></span> | <span data-ttu-id="6fa61-600">2</span><span class="sxs-lookup"><span data-stu-id="6fa61-600">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="6fa61-601">4</span><span class="sxs-lookup"><span data-stu-id="6fa61-601">4</span></span> | <span data-ttu-id="6fa61-602">Global</span><span class="sxs-lookup"><span data-stu-id="6fa61-602">Global</span></span> | <span data-ttu-id="6fa61-603">2</span><span class="sxs-lookup"><span data-stu-id="6fa61-603">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="6fa61-604">5</span><span class="sxs-lookup"><span data-stu-id="6fa61-604">5</span></span> | <span data-ttu-id="6fa61-605">Controller</span><span class="sxs-lookup"><span data-stu-id="6fa61-605">Controller</span></span> | <span data-ttu-id="6fa61-606">1</span><span class="sxs-lookup"><span data-stu-id="6fa61-606">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="6fa61-607">6</span><span class="sxs-lookup"><span data-stu-id="6fa61-607">6</span></span> | <span data-ttu-id="6fa61-608">Método</span><span class="sxs-lookup"><span data-stu-id="6fa61-608">Method</span></span> | <span data-ttu-id="6fa61-609">0</span><span class="sxs-lookup"><span data-stu-id="6fa61-609">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="6fa61-610">A propriedade `Order` substitui o escopo ao determinar a ordem na qual os filtros serão executados.</span><span class="sxs-lookup"><span data-stu-id="6fa61-610">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="6fa61-611">Os filtros são classificados primeiro pela ordem e o escopo é usado para desempatar.</span><span class="sxs-lookup"><span data-stu-id="6fa61-611">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="6fa61-612">Todos os filtros internos implementam `IOrderedFilter` e definem o valor de `Order` padrão como 0.</span><span class="sxs-lookup"><span data-stu-id="6fa61-612">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="6fa61-613">Para os filtros internos, o escopo determina a ordem, a menos que você defina `Order` com um valor diferente de zero.</span><span class="sxs-lookup"><span data-stu-id="6fa61-613">For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="6fa61-614">Cancelamento e curto-circuito</span><span class="sxs-lookup"><span data-stu-id="6fa61-614">Cancellation and short-circuiting</span></span>

<span data-ttu-id="6fa61-615">O pipeline de filtro pode sofrer um curto-circuito por meio da configuração da propriedade <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> no parâmetro <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> fornecido ao método do filtro.</span><span class="sxs-lookup"><span data-stu-id="6fa61-615">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="6fa61-616">Por exemplo, o filtro de Recurso a seguir impede que o resto do pipeline seja executado:</span><span class="sxs-lookup"><span data-stu-id="6fa61-616">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="6fa61-617">No código a seguir, os filtros `ShortCircuitingResourceFilter` e `AddHeader` têm como destino o método de ação `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="6fa61-617">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="6fa61-618">`ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="6fa61-618">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="6fa61-619">É executado primeiro, porque ele é um filtro de recurso e `AddHeader` é um filtro de ação.</span><span class="sxs-lookup"><span data-stu-id="6fa61-619">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="6fa61-620">Causa um curto-circuito no restante do pipeline.</span><span class="sxs-lookup"><span data-stu-id="6fa61-620">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="6fa61-621">Portanto, o filtro `AddHeader` nunca é executado para a ação `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="6fa61-621">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="6fa61-622">Esse comportamento seria o mesmo se os dois filtros fossem aplicados no nível do método de ação, desde que `ShortCircuitingResourceFilter` fosse executado primeiro.</span><span class="sxs-lookup"><span data-stu-id="6fa61-622">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="6fa61-623">O `ShortCircuitingResourceFilter` é executado primeiro, devido ao seu tipo de filtro ou pelo uso explícito da propriedade `Order`.</span><span class="sxs-lookup"><span data-stu-id="6fa61-623">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="6fa61-624">Injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="6fa61-624">Dependency injection</span></span>

<span data-ttu-id="6fa61-625">Filtros podem ser adicionados por tipo ou por instância.</span><span class="sxs-lookup"><span data-stu-id="6fa61-625">Filters can be added by type or by instance.</span></span> <span data-ttu-id="6fa61-626">Se você adicionar uma instância, ela será usada para cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="6fa61-626">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="6fa61-627">Se um tipo for adicionado, ele será ativado pelo tipo.</span><span class="sxs-lookup"><span data-stu-id="6fa61-627">If a type is added, it's type-activated.</span></span> <span data-ttu-id="6fa61-628">Um filtro ativado por tipo significa:</span><span class="sxs-lookup"><span data-stu-id="6fa61-628">A type-activated filter means:</span></span>

* <span data-ttu-id="6fa61-629">Uma instância é criada para cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="6fa61-629">An instance is created for each request.</span></span>
* <span data-ttu-id="6fa61-630">Qualquer dependência de construtor é preenchida pela [injeção de dependência](xref:fundamentals/dependency-injection) (DI).</span><span class="sxs-lookup"><span data-stu-id="6fa61-630">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="6fa61-631">Filtros que são implementados como atributos e adicionados diretamente a classes de controlador ou métodos de ação não podem ter dependências de construtor fornecidas pela DI ([injeção de dependência](xref:fundamentals/dependency-injection)).</span><span class="sxs-lookup"><span data-stu-id="6fa61-631">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="6fa61-632">Dependências de construtor não podem ser fornecidas pela DI porque:</span><span class="sxs-lookup"><span data-stu-id="6fa61-632">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="6fa61-633">Os atributos precisam ter os parâmetros de construtor fornecidos quando são aplicados.</span><span class="sxs-lookup"><span data-stu-id="6fa61-633">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="6fa61-634">Essa é uma limitação do funcionamento dos atributos.</span><span class="sxs-lookup"><span data-stu-id="6fa61-634">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="6fa61-635">Os filtros a seguir são compatíveis com dependências de construtor fornecidas pela injeção de dependência:</span><span class="sxs-lookup"><span data-stu-id="6fa61-635">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="6fa61-636"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementado no atributo.</span><span class="sxs-lookup"><span data-stu-id="6fa61-636"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="6fa61-637">Os filtros anteriores podem ser aplicados a um método de ação ou controlador:</span><span class="sxs-lookup"><span data-stu-id="6fa61-637">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="6fa61-638">Os agentes estão disponíveis na DI.</span><span class="sxs-lookup"><span data-stu-id="6fa61-638">Loggers are available from DI.</span></span> <span data-ttu-id="6fa61-639">No entanto, evite criar e usar filtros apenas para fins de registro em log.</span><span class="sxs-lookup"><span data-stu-id="6fa61-639">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="6fa61-640">O [registro em log da estrutura interna](xref:fundamentals/logging/index) normalmente fornece o que é necessário para o registro em log.</span><span class="sxs-lookup"><span data-stu-id="6fa61-640">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="6fa61-641">Registro em log adicionado aos filtros:</span><span class="sxs-lookup"><span data-stu-id="6fa61-641">Logging added to filters:</span></span>

* <span data-ttu-id="6fa61-642">Deve se concentrar em questões de domínio de negócios ou comportamento específico ao filtro.</span><span class="sxs-lookup"><span data-stu-id="6fa61-642">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="6fa61-643">**Não** deve registrar ações ou outros eventos da estrutura.</span><span class="sxs-lookup"><span data-stu-id="6fa61-643">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="6fa61-644">Os filtros internos registram ações e eventos da estrutura.</span><span class="sxs-lookup"><span data-stu-id="6fa61-644">The built in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="6fa61-645">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="6fa61-645">ServiceFilterAttribute</span></span>

<span data-ttu-id="6fa61-646">Os tipos de implementação do filtro de serviço são registrados em `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="6fa61-646">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="6fa61-647">Um <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> recupera uma instância do filtro da DI.</span><span class="sxs-lookup"><span data-stu-id="6fa61-647">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="6fa61-648">O código a seguir mostra `AddHeaderResultServiceFilter`:</span><span class="sxs-lookup"><span data-stu-id="6fa61-648">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="6fa61-649">No código a seguir, `AddHeaderResultServiceFilter` é adicionado ao contêiner de DI:</span><span class="sxs-lookup"><span data-stu-id="6fa61-649">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="6fa61-650">No código a seguir, o atributo `ServiceFilter` recupera uma instância do filtro `AddHeaderResultServiceFilter` da DI:</span><span class="sxs-lookup"><span data-stu-id="6fa61-650">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="6fa61-651">Ao usar `ServiceFilterAttribute`, configurar [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="6fa61-651">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="6fa61-652">Fornece uma dica de que a instância de filtro *pode* ser reutilizada fora do escopo de solicitação em que foi criada.</span><span class="sxs-lookup"><span data-stu-id="6fa61-652">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="6fa61-653">O runtime do ASP.NET Core não garante:</span><span class="sxs-lookup"><span data-stu-id="6fa61-653">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="6fa61-654">Que uma única instância do filtro será criada.</span><span class="sxs-lookup"><span data-stu-id="6fa61-654">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="6fa61-655">Que o filtro não será solicitado novamente no contêiner de DI em algum momento posterior.</span><span class="sxs-lookup"><span data-stu-id="6fa61-655">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="6fa61-656">Não deve ser usado com um filtro que dependa dos serviços com um tempo de vida diferente de singleton.</span><span class="sxs-lookup"><span data-stu-id="6fa61-656">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="6fa61-657"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="6fa61-657"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="6fa61-658">`IFilterFactory` expõe o método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> para criar uma instância de <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="6fa61-658">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="6fa61-659">`CreateInstance` carrega o tipo especificado na DI.</span><span class="sxs-lookup"><span data-stu-id="6fa61-659">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="6fa61-660">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="6fa61-660">TypeFilterAttribute</span></span>

<span data-ttu-id="6fa61-661">O <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> é semelhante ao <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, mas seu tipo não é resolvido diretamente por meio do contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="6fa61-661"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="6fa61-662">Ele cria uma instância do tipo usando <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="6fa61-662">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="6fa61-663">Como os tipos `TypeFilterAttribute` não são resolvidos diretamente do contêiner de DI:</span><span class="sxs-lookup"><span data-stu-id="6fa61-663">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="6fa61-664">Os tipos referenciados usando o `TypeFilterAttribute` não precisam ser registrados no contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="6fa61-664">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="6fa61-665">Eles têm suas dependências atendidas pelo contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="6fa61-665">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="6fa61-666">Opcionalmente, o `TypeFilterAttribute` pode aceitar argumentos de construtor para o tipo.</span><span class="sxs-lookup"><span data-stu-id="6fa61-666">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="6fa61-667">Ao usar `TypeFilterAttribute`, configurar [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="6fa61-667">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="6fa61-668">Fornece uma dica de que a instância de filtro *pode* ser reutilizada fora do escopo de solicitação em que foi criada.</span><span class="sxs-lookup"><span data-stu-id="6fa61-668">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="6fa61-669">O runtime do ASP.NET Core não fornece garantias de que uma única instância do filtro será criada.</span><span class="sxs-lookup"><span data-stu-id="6fa61-669">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="6fa61-670">Não deve ser usado com um filtro que dependa dos serviços com um tempo de vida diferente de singleton.</span><span class="sxs-lookup"><span data-stu-id="6fa61-670">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="6fa61-671">O exemplo a seguir mostra como passar argumentos para um tipo usando `TypeFilterAttribute`:</span><span class="sxs-lookup"><span data-stu-id="6fa61-671">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="6fa61-672">Filtros de autorização</span><span class="sxs-lookup"><span data-stu-id="6fa61-672">Authorization filters</span></span>

<span data-ttu-id="6fa61-673">Filtros de autorização:</span><span class="sxs-lookup"><span data-stu-id="6fa61-673">Authorization filters:</span></span>

* <span data-ttu-id="6fa61-674">São os primeiros filtros executados no pipeline de filtro.</span><span class="sxs-lookup"><span data-stu-id="6fa61-674">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="6fa61-675">Controlam o acesso aos métodos de ação.</span><span class="sxs-lookup"><span data-stu-id="6fa61-675">Control access to action methods.</span></span>
* <span data-ttu-id="6fa61-676">Têm um método anterior, mas não têm um método posterior.</span><span class="sxs-lookup"><span data-stu-id="6fa61-676">Have a before method, but no after method.</span></span>

<span data-ttu-id="6fa61-677">Filtros de autorização personalizados exigem uma estrutura de autorização personalizada.</span><span class="sxs-lookup"><span data-stu-id="6fa61-677">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="6fa61-678">Prefira configurar as políticas de autorização ou escrever uma política de autorização personalizada em vez de escrever um filtro personalizado.</span><span class="sxs-lookup"><span data-stu-id="6fa61-678">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="6fa61-679">O filtro de autorização interno:</span><span class="sxs-lookup"><span data-stu-id="6fa61-679">The built-in authorization filter:</span></span>

* <span data-ttu-id="6fa61-680">Chama o sistema de autorização.</span><span class="sxs-lookup"><span data-stu-id="6fa61-680">Calls the authorization system.</span></span>
* <span data-ttu-id="6fa61-681">Não autoriza solicitações.</span><span class="sxs-lookup"><span data-stu-id="6fa61-681">Does not authorize requests.</span></span>

<span data-ttu-id="6fa61-682">**Não** gera exceções dentro de filtros de autorização:</span><span class="sxs-lookup"><span data-stu-id="6fa61-682">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="6fa61-683">A exceção não será tratada.</span><span class="sxs-lookup"><span data-stu-id="6fa61-683">The exception will not be handled.</span></span>
* <span data-ttu-id="6fa61-684">Os filtros de exceção não tratarão a exceção.</span><span class="sxs-lookup"><span data-stu-id="6fa61-684">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="6fa61-685">Considere a possibilidade de emitir um desafio quando ocorrer uma exceção em um filtro de autorização.</span><span class="sxs-lookup"><span data-stu-id="6fa61-685">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="6fa61-686">Saiba mais sobre [Autorização](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="6fa61-686">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="6fa61-687">Filtros de recurso</span><span class="sxs-lookup"><span data-stu-id="6fa61-687">Resource filters</span></span>

<span data-ttu-id="6fa61-688">Filtros de recurso:</span><span class="sxs-lookup"><span data-stu-id="6fa61-688">Resource filters:</span></span>

* <span data-ttu-id="6fa61-689">Implementam a interface <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.</span><span class="sxs-lookup"><span data-stu-id="6fa61-689">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="6fa61-690">A execução encapsula grande parte do pipeline de filtro.</span><span class="sxs-lookup"><span data-stu-id="6fa61-690">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="6fa61-691">Somente os [Filtros de autorização](#authorization-filters) são executados antes dos filtros de recurso.</span><span class="sxs-lookup"><span data-stu-id="6fa61-691">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="6fa61-692">Os filtros de recursos são úteis para causar um curto-circuito na maior parte do pipeline.</span><span class="sxs-lookup"><span data-stu-id="6fa61-692">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="6fa61-693">Por exemplo, um filtro de cache pode evitar o restante do pipeline em uma ocorrência no cache.</span><span class="sxs-lookup"><span data-stu-id="6fa61-693">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="6fa61-694">Exemplos de filtros de recurso:</span><span class="sxs-lookup"><span data-stu-id="6fa61-694">Resource filter examples:</span></span>

* <span data-ttu-id="6fa61-695">[O filtro de recurso em curto-circuito](#short-circuiting-resource-filter) mostrado anteriormente.</span><span class="sxs-lookup"><span data-stu-id="6fa61-695">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="6fa61-696">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="6fa61-696">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="6fa61-697">Impede o model binding de acessar os dados do formulário.</span><span class="sxs-lookup"><span data-stu-id="6fa61-697">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="6fa61-698">Usado para uploads de arquivos grandes para impedir que os dados de formulário sejam lidos na memória.</span><span class="sxs-lookup"><span data-stu-id="6fa61-698">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="6fa61-699">Filtros de ação</span><span class="sxs-lookup"><span data-stu-id="6fa61-699">Action filters</span></span>

> [!IMPORTANT]
> <span data-ttu-id="6fa61-700">Os filtros de ação **não** se aplicam ao Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="6fa61-700">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="6fa61-701">O Razor Pages é compatível com <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> e <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter>.</span><span class="sxs-lookup"><span data-stu-id="6fa61-701">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="6fa61-702">Para obter mais informações, confira [Métodos de filtro para Páginas Razor](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="6fa61-702">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="6fa61-703">Filtros de ação:</span><span class="sxs-lookup"><span data-stu-id="6fa61-703">Action filters:</span></span>

* <span data-ttu-id="6fa61-704">Implementam a interface <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.</span><span class="sxs-lookup"><span data-stu-id="6fa61-704">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="6fa61-705">A execução deles envolve a execução de métodos de ação.</span><span class="sxs-lookup"><span data-stu-id="6fa61-705">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="6fa61-706">O código a seguir mostra um exemplo de filtro de ação:</span><span class="sxs-lookup"><span data-stu-id="6fa61-706">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="6fa61-707">A classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> fornece as seguintes propriedades:</span><span class="sxs-lookup"><span data-stu-id="6fa61-707">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="6fa61-708"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - permite a leitura das entradas para um método de ação.</span><span class="sxs-lookup"><span data-stu-id="6fa61-708"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables the inputs to an action method be read.</span></span>
* <span data-ttu-id="6fa61-709"><xref:Microsoft.AspNetCore.Mvc.Controller> – permite a manipulação da instância do controlador.</span><span class="sxs-lookup"><span data-stu-id="6fa61-709"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="6fa61-710"><xref:System.Web.Mvc.ActionExecutingContext.Result> – configuração de `Result` execução de curtos-circuitos do método de ação e dos filtros de ação posteriores.</span><span class="sxs-lookup"><span data-stu-id="6fa61-710"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="6fa61-711">Gerar uma exceção em um método de ação:</span><span class="sxs-lookup"><span data-stu-id="6fa61-711">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="6fa61-712">Impede a execução de filtros subsequentes.</span><span class="sxs-lookup"><span data-stu-id="6fa61-712">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="6fa61-713">Ao contrário da configuração `Result`, é tratada como uma falha e não como um resultado bem-sucedido.</span><span class="sxs-lookup"><span data-stu-id="6fa61-713">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="6fa61-714">A classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> fornece `Controller` e `Result`, além das seguintes propriedades:</span><span class="sxs-lookup"><span data-stu-id="6fa61-714">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="6fa61-715"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> – verdadeiro se a execução da ação tiver sofrido curto-circuito por outro filtro.</span><span class="sxs-lookup"><span data-stu-id="6fa61-715"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="6fa61-716"><xref:System.Web.Mvc.ActionExecutedContext.Exception> – não será nulo se a ação ou um filtro de ação executado antes tiver apresentado uma exceção.</span><span class="sxs-lookup"><span data-stu-id="6fa61-716"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="6fa61-717">Definir essa propriedade como nulo:</span><span class="sxs-lookup"><span data-stu-id="6fa61-717">Setting this property to null:</span></span>

  * <span data-ttu-id="6fa61-718">Trata efetivamente a exceção.</span><span class="sxs-lookup"><span data-stu-id="6fa61-718">Effectively handles the exception.</span></span>
  * <span data-ttu-id="6fa61-719">`Result` é executado como se tivesse retornado do método de ação.</span><span class="sxs-lookup"><span data-stu-id="6fa61-719">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="6fa61-720">Para um `IAsyncActionFilter`, uma chamada para o <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span><span class="sxs-lookup"><span data-stu-id="6fa61-720">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="6fa61-721">Executa todos os próximos filtros de ação e o método de ação.</span><span class="sxs-lookup"><span data-stu-id="6fa61-721">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="6fa61-722">Retorna `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="6fa61-722">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="6fa61-723">Para fazer um curto-circuito, atribua <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> a uma instância de resultado e não chame o `next` (`ActionExecutionDelegate`).</span><span class="sxs-lookup"><span data-stu-id="6fa61-723">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="6fa61-724">A estrutura fornece um <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> abstrato que você pode colocar em uma subclasse.</span><span class="sxs-lookup"><span data-stu-id="6fa61-724">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="6fa61-725">O filtro de ação `OnActionExecuting` pode ser usado para:</span><span class="sxs-lookup"><span data-stu-id="6fa61-725">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="6fa61-726">Validar o estado do modelo.</span><span class="sxs-lookup"><span data-stu-id="6fa61-726">Validate model state.</span></span>
* <span data-ttu-id="6fa61-727">Retornar um erro se o estado for inválido.</span><span class="sxs-lookup"><span data-stu-id="6fa61-727">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="6fa61-728">O método `OnActionExecuted` é executado após o método de ação:</span><span class="sxs-lookup"><span data-stu-id="6fa61-728">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="6fa61-729">E pode ver e manipular os resultados da ação por meio da propriedade <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result>.</span><span class="sxs-lookup"><span data-stu-id="6fa61-729">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="6fa61-730"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> é definido como verdadeiro se a execução da ação tiver sofrido curto-circuito por outro filtro.</span><span class="sxs-lookup"><span data-stu-id="6fa61-730"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="6fa61-731"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> é definido como um valor não nulo se a ação ou um filtro de ação posterior tiver apresentado uma exceção.</span><span class="sxs-lookup"><span data-stu-id="6fa61-731"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="6fa61-732">Definindo `Exception` como nulo:</span><span class="sxs-lookup"><span data-stu-id="6fa61-732">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="6fa61-733">Trata efetivamente uma exceção.</span><span class="sxs-lookup"><span data-stu-id="6fa61-733">Effectively handles an exception.</span></span>
  * <span data-ttu-id="6fa61-734">`ActionExecutedContext.Result` é executado como se fosse retornado normalmente do método de ação.</span><span class="sxs-lookup"><span data-stu-id="6fa61-734">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="6fa61-735">Filtros de exceção</span><span class="sxs-lookup"><span data-stu-id="6fa61-735">Exception filters</span></span>

<span data-ttu-id="6fa61-736">Filtros de exceção:</span><span class="sxs-lookup"><span data-stu-id="6fa61-736">Exception filters:</span></span>

* <span data-ttu-id="6fa61-737">Implementam <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span><span class="sxs-lookup"><span data-stu-id="6fa61-737">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span> 
* <span data-ttu-id="6fa61-738">Podem ser usados para implementar políticas de tratamento de erro comuns.</span><span class="sxs-lookup"><span data-stu-id="6fa61-738">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="6fa61-739">O exemplo de filtro de exceção a seguir usa uma exibição de erro personalizada para mostrar detalhes sobre exceções que ocorrem quando o aplicativo está em desenvolvimento:</span><span class="sxs-lookup"><span data-stu-id="6fa61-739">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="6fa61-740">Filtros de exceção:</span><span class="sxs-lookup"><span data-stu-id="6fa61-740">Exception filters:</span></span>

* <span data-ttu-id="6fa61-741">Não têm eventos anteriores nem posteriores.</span><span class="sxs-lookup"><span data-stu-id="6fa61-741">Don't have before and after events.</span></span>
* <span data-ttu-id="6fa61-742">Implementam <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span><span class="sxs-lookup"><span data-stu-id="6fa61-742">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="6fa61-743">Tratam as exceções sem tratamento que ocorrem no Razor Page ou na criação do controlador, no [model binding](xref:mvc/models/model-binding), nos filtros de ação ou nos métodos de ação.</span><span class="sxs-lookup"><span data-stu-id="6fa61-743">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="6fa61-744">**Não** capturam as exceções que ocorrem em filtros de recurso, em filtros de resultado ou na execução do resultado de MVC.</span><span class="sxs-lookup"><span data-stu-id="6fa61-744">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="6fa61-745">Para tratar uma exceção, defina a propriedade <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> como `true` ou grave uma resposta.</span><span class="sxs-lookup"><span data-stu-id="6fa61-745">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="6fa61-746">Isso interrompe a propagação da exceção.</span><span class="sxs-lookup"><span data-stu-id="6fa61-746">This stops propagation of the exception.</span></span> <span data-ttu-id="6fa61-747">Um filtro de exceção não pode transformar uma exceção em "êxito".</span><span class="sxs-lookup"><span data-stu-id="6fa61-747">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="6fa61-748">Somente um filtro de ação pode fazer isso.</span><span class="sxs-lookup"><span data-stu-id="6fa61-748">Only an action filter can do that.</span></span>

<span data-ttu-id="6fa61-749">Filtros de exceção:</span><span class="sxs-lookup"><span data-stu-id="6fa61-749">Exception filters:</span></span>

* <span data-ttu-id="6fa61-750">São bons para interceptar as exceções que ocorrem nas ações.</span><span class="sxs-lookup"><span data-stu-id="6fa61-750">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="6fa61-751">Não são tão flexíveis quanto o middleware de tratamento de erro.</span><span class="sxs-lookup"><span data-stu-id="6fa61-751">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="6fa61-752">Prefira o middleware para tratamento de exceção.</span><span class="sxs-lookup"><span data-stu-id="6fa61-752">Prefer middleware for exception handling.</span></span> <span data-ttu-id="6fa61-753">Use filtros de exceção apenas quando o tratamento de erros *for diferente* com base no método de ação chamado.</span><span class="sxs-lookup"><span data-stu-id="6fa61-753">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="6fa61-754">Por exemplo, um aplicativo pode ter métodos de ação para os pontos de extremidade da API e para modos de exibição/HTML.</span><span class="sxs-lookup"><span data-stu-id="6fa61-754">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="6fa61-755">Os pontos de extremidade da API podem retornar informações de erro como JSON, enquanto as ações baseadas em modo de exibição podem retornar uma página de erro como HTML.</span><span class="sxs-lookup"><span data-stu-id="6fa61-755">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="6fa61-756">Filtros de resultado</span><span class="sxs-lookup"><span data-stu-id="6fa61-756">Result filters</span></span>

<span data-ttu-id="6fa61-757">Filtros de resultado:</span><span class="sxs-lookup"><span data-stu-id="6fa61-757">Result filters:</span></span>

* <span data-ttu-id="6fa61-758">Implemente uma interface:</span><span class="sxs-lookup"><span data-stu-id="6fa61-758">Implement an interface:</span></span>
  * <span data-ttu-id="6fa61-759"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="6fa61-759"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="6fa61-760"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="6fa61-760"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="6fa61-761">A execução deles envolve a execução de resultados de ação.</span><span class="sxs-lookup"><span data-stu-id="6fa61-761">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="6fa61-762">IResultFilter e IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="6fa61-762">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="6fa61-763">O código a seguir mostra um filtro de resultado que adiciona um cabeçalho HTTP:</span><span class="sxs-lookup"><span data-stu-id="6fa61-763">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="6fa61-764">O tipo de resultado que está sendo executado depende da ação.</span><span class="sxs-lookup"><span data-stu-id="6fa61-764">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="6fa61-765">Uma ação que retorna um modo de exibição incluiria todo o processamento de Razor como parte do <xref:Microsoft.AspNetCore.Mvc.ViewResult> em execução.</span><span class="sxs-lookup"><span data-stu-id="6fa61-765">An action returning a view would include all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="6fa61-766">Um método de API pode executar alguma serialização como parte da execução do resultado.</span><span class="sxs-lookup"><span data-stu-id="6fa61-766">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="6fa61-767">Saiba mais sobre [os resultados da ação](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="6fa61-767">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="6fa61-768">Os filtros de resultado são executados somente quando um filtro de ação ou Action produz um resultado de ação.</span><span class="sxs-lookup"><span data-stu-id="6fa61-768">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="6fa61-769">Os filtros de resultado não são executados quando:</span><span class="sxs-lookup"><span data-stu-id="6fa61-769">Result filters are not executed when:</span></span>

* <span data-ttu-id="6fa61-770">Um filtro de autorização ou filtro de recursos de curto-circuitos do pipeline.</span><span class="sxs-lookup"><span data-stu-id="6fa61-770">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="6fa61-771">Um filtro de exceção trata uma exceção produzindo um resultado de ação.</span><span class="sxs-lookup"><span data-stu-id="6fa61-771">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="6fa61-772">O método <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> pode fazer o curto-circuito da execução do resultado da ação e dos filtros de resultados posteriores definindo <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> como `true`.</span><span class="sxs-lookup"><span data-stu-id="6fa61-772">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="6fa61-773">Grave no objeto de resposta ao fazer um curto-circuito para evitar gerar uma resposta vazia.</span><span class="sxs-lookup"><span data-stu-id="6fa61-773">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="6fa61-774">A geração de uma exceção em `IResultFilter.OnResultExecuting`:</span><span class="sxs-lookup"><span data-stu-id="6fa61-774">Throwing an exception in `IResultFilter.OnResultExecuting` will:</span></span>

* <span data-ttu-id="6fa61-775">Impedir a execução do resultado da ação e dos próximos filtros.</span><span class="sxs-lookup"><span data-stu-id="6fa61-775">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="6fa61-776">Ser tratada como uma falha e não como um resultado com êxito.</span><span class="sxs-lookup"><span data-stu-id="6fa61-776">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="6fa61-777">Quando o método <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> é executado, a resposta provavelmente já foi enviada para o cliente.</span><span class="sxs-lookup"><span data-stu-id="6fa61-777">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has likely already been sent to the client.</span></span> <span data-ttu-id="6fa61-778">Se a resposta já tiver sido enviada ao cliente, ela não poderá ser alterada ainda mais.</span><span class="sxs-lookup"><span data-stu-id="6fa61-778">If the response has already been sent to the client, it cannot be changed further.</span></span>

<span data-ttu-id="6fa61-779">`ResultExecutedContext.Canceled` será definido como `true` se a execução do resultado da ação tiver sofrido curto-circuito por outro filtro.</span><span class="sxs-lookup"><span data-stu-id="6fa61-779">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="6fa61-780">`ResultExecutedContext.Exception` será definido como um valor não nulo se o resultado da ação ou um filtro de resultado posterior tiver gerado uma exceção.</span><span class="sxs-lookup"><span data-stu-id="6fa61-780">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="6fa61-781">Definir `Exception` como nulo trata uma exceção com eficiência e impede que ela seja gerada novamente pelo ASP.NET Core posteriormente no pipeline.</span><span class="sxs-lookup"><span data-stu-id="6fa61-781">Setting `Exception` to null effectively handles an exception and prevents the exception from being rethrown by ASP.NET Core later in the pipeline.</span></span> <span data-ttu-id="6fa61-782">Não há nenhuma maneira confiável para gravar dados em uma resposta ao manipular uma exceção em um filtro de resultado.</span><span class="sxs-lookup"><span data-stu-id="6fa61-782">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="6fa61-783">Se os cabeçalhos tiverem sido liberados para o cliente quando o resultado de uma ação gerar uma exceção, não haverá mecanismo confiável para enviar um código de falha.</span><span class="sxs-lookup"><span data-stu-id="6fa61-783">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="6fa61-784">Para um <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, uma chamada para `await next` no <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executa qualquer filtro de resultado posterior e o resultado da ação.</span><span class="sxs-lookup"><span data-stu-id="6fa61-784">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="6fa61-785">Para causar um curto-circuito, defina [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) como `true` e não chame o `ResultExecutionDelegate`:</span><span class="sxs-lookup"><span data-stu-id="6fa61-785">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="6fa61-786">A estrutura fornece um `ResultFilterAttribute` abstrato que você pode colocar em uma subclasse.</span><span class="sxs-lookup"><span data-stu-id="6fa61-786">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="6fa61-787">A classe [AddHeaderAttribute](#add-header-attribute) mostrada anteriormente é um exemplo de atributo de filtro de resultado.</span><span class="sxs-lookup"><span data-stu-id="6fa61-787">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="6fa61-788">IAlwaysRunResultFilter e IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="6fa61-788">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="6fa61-789">As interfaces <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> e <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> declaram uma implementação <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> que é executada para todos os resultados da ação.</span><span class="sxs-lookup"><span data-stu-id="6fa61-789">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="6fa61-790">Isso inclui resultados de ação produzidos por:</span><span class="sxs-lookup"><span data-stu-id="6fa61-790">This includes action results produced by:</span></span>

* <span data-ttu-id="6fa61-791">Filtros de autorização e filtros de recursos que curto-Circuit.</span><span class="sxs-lookup"><span data-stu-id="6fa61-791">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="6fa61-792">Filtros de exceção.</span><span class="sxs-lookup"><span data-stu-id="6fa61-792">Exception filters.</span></span>

<span data-ttu-id="6fa61-793">Por exemplo, o filtro a seguir sempre é executado e define o resultado de uma ação (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) com um código de status *422 Entidade Não Processável* quando ocorre falha na negociação de conteúdo:</span><span class="sxs-lookup"><span data-stu-id="6fa61-793">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="6fa61-794">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="6fa61-794">IFilterFactory</span></span>

<span data-ttu-id="6fa61-795"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="6fa61-795"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="6fa61-796">Portanto, uma instância `IFilterFactory` pode ser usada como uma instância `IFilterMetadata` em qualquer parte do pipeline de filtro.</span><span class="sxs-lookup"><span data-stu-id="6fa61-796">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="6fa61-797">Quando o runtime se prepara para invocar o filtro, tenta convertê-lo em um `IFilterFactory`.</span><span class="sxs-lookup"><span data-stu-id="6fa61-797">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="6fa61-798">Se essa conversão for bem-sucedida, o método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> será chamado para criar a instância `IFilterMetadata` invocada.</span><span class="sxs-lookup"><span data-stu-id="6fa61-798">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="6fa61-799">Isso fornece um design flexível, porque o pipeline de filtro preciso não precisa ser definido explicitamente quando o aplicativo é iniciado.</span><span class="sxs-lookup"><span data-stu-id="6fa61-799">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="6fa61-800">Implemente `IFilterFactory` usando implementações personalizadas de atributo como outra abordagem à criação de filtros:</span><span class="sxs-lookup"><span data-stu-id="6fa61-800">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="6fa61-801">O código anterior pode ser testado executando o [exemplo para download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span><span class="sxs-lookup"><span data-stu-id="6fa61-801">The preceding code can be tested by running the [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span></span>

* <span data-ttu-id="6fa61-802">Invocar as ferramentas de desenvolvedor F12.</span><span class="sxs-lookup"><span data-stu-id="6fa61-802">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="6fa61-803">Navegue até `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="6fa61-803">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="6fa61-804">As ferramentas de desenvolvedor F12 exibem os seguintes cabeçalhos de resposta adicionados pelo código de exemplo:</span><span class="sxs-lookup"><span data-stu-id="6fa61-804">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="6fa61-805">**autor:** `Joe Smith`</span><span class="sxs-lookup"><span data-stu-id="6fa61-805">**author:** `Joe Smith`</span></span>
* <span data-ttu-id="6fa61-806">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="6fa61-806">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="6fa61-807">**interno:** `My header`</span><span class="sxs-lookup"><span data-stu-id="6fa61-807">**internal:** `My header`</span></span>

<span data-ttu-id="6fa61-808">O código anterior cria o cabeçalho de resposta **interno:** `My header`.</span><span class="sxs-lookup"><span data-stu-id="6fa61-808">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="6fa61-809">IFilterFactory implementado em um atributo</span><span class="sxs-lookup"><span data-stu-id="6fa61-809">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="6fa61-810">Filtros que implementam `IFilterFactory` são úteis para filtros que:</span><span class="sxs-lookup"><span data-stu-id="6fa61-810">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="6fa61-811">Não exigem a passagem de parâmetros.</span><span class="sxs-lookup"><span data-stu-id="6fa61-811">Don't require passing parameters.</span></span>
* <span data-ttu-id="6fa61-812">Tenha dependências de construtor que precisem ser atendidas pela DI.</span><span class="sxs-lookup"><span data-stu-id="6fa61-812">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="6fa61-813"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="6fa61-813"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="6fa61-814">`IFilterFactory` expõe o método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> para criar uma instância de <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="6fa61-814">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="6fa61-815">`CreateInstance` carrega o tipo especificado do contêiner de serviços (DI).</span><span class="sxs-lookup"><span data-stu-id="6fa61-815">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="6fa61-816">O código a seguir mostra três abordagens para aplicar o `[SampleActionFilter]`:</span><span class="sxs-lookup"><span data-stu-id="6fa61-816">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="6fa61-817">No código anterior, decorar o método com `[SampleActionFilter]` é a abordagem preferida para aplicar o `SampleActionFilter`.</span><span class="sxs-lookup"><span data-stu-id="6fa61-817">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="6fa61-818">Usando middleware no pipeline de filtros</span><span class="sxs-lookup"><span data-stu-id="6fa61-818">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="6fa61-819">Filtros de recursos funcionam como [middleware](xref:fundamentals/middleware/index), no sentido em que envolvem a execução de tudo o que vem depois no pipeline.</span><span class="sxs-lookup"><span data-stu-id="6fa61-819">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="6fa61-820">Mas os filtros diferem do middleware porque fazem parte do ASP.NET Core, o que significa que têm acesso ao contexto e a constructos do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6fa61-820">But filters differ from middleware in that they're part of the ASP.NET Core runtime, which means that they have access to ASP.NET Core context and constructs.</span></span>

<span data-ttu-id="6fa61-821">Para usar o middleware como um filtro, crie um tipo com um método `Configure` que especifica o middleware para injeção no pipeline de filtros.</span><span class="sxs-lookup"><span data-stu-id="6fa61-821">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="6fa61-822">O exemplo a seguir usa o middleware de localização para estabelecer a cultura atual para uma solicitação:</span><span class="sxs-lookup"><span data-stu-id="6fa61-822">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="6fa61-823">Use <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> para executar o middleware:</span><span class="sxs-lookup"><span data-stu-id="6fa61-823">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="6fa61-824">Filtros de middleware são executados no mesmo estágio do pipeline de filtros que filtros de recurso, antes do model binding e depois do restante do pipeline.</span><span class="sxs-lookup"><span data-stu-id="6fa61-824">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="6fa61-825">Próximas ações</span><span class="sxs-lookup"><span data-stu-id="6fa61-825">Next actions</span></span>

* <span data-ttu-id="6fa61-826">Consulte [métodos de filtro para Razor Pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="6fa61-826">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="6fa61-827">Para fazer experiências com filtros, [baixe, teste e modifique o exemplo do GitHub](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span><span class="sxs-lookup"><span data-stu-id="6fa61-827">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>

::: moniker-end
