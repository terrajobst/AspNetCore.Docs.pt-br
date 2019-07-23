---
title: Filtros no ASP.NET Core
author: ardalis
description: Saiba como os filtros funcionam e como usá-los no ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 05/08/2019
uid: mvc/controllers/filters
ms.openlocfilehash: 50b199744f32ad19335080da406db69665ec1ae9
ms.sourcegitcommit: 7a40c56bf6a6aaa63a7ee83a2cac9b3a1d77555e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67856155"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="dc263-103">Filtros no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dc263-103">Filters in ASP.NET Core</span></span>

<span data-ttu-id="dc263-104">Por [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="dc263-104">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="dc263-105">Os *Filtros* no ASP.NET Core permitem a execução de código antes ou depois de determinados estágios do pipeline de processamento de solicitações.</span><span class="sxs-lookup"><span data-stu-id="dc263-105">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="dc263-106">O filtros internos lidam com tarefas como:</span><span class="sxs-lookup"><span data-stu-id="dc263-106">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="dc263-107">Autorização (impedir o acesso a recursos aos quais o usuário não está autorizado).</span><span class="sxs-lookup"><span data-stu-id="dc263-107">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="dc263-108">Cache de resposta (causar um curto-circuito do pipeline de solicitação para retornar uma resposta armazenada em cache).</span><span class="sxs-lookup"><span data-stu-id="dc263-108">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="dc263-109">É possível criar filtros personalizados para lidar com interesses paralelos.</span><span class="sxs-lookup"><span data-stu-id="dc263-109">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="dc263-110">Entre os exemplos de interesses paralelos estão o tratamento de erros, cache, configuração, autorização e registro em log.</span><span class="sxs-lookup"><span data-stu-id="dc263-110">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="dc263-111">Filtros evitam a duplicação do código.</span><span class="sxs-lookup"><span data-stu-id="dc263-111">Filters avoid duplicating code.</span></span> <span data-ttu-id="dc263-112">Por exemplo, um filtro de exceção de tratamento de erro poderia consolidar o tratamento de erro.</span><span class="sxs-lookup"><span data-stu-id="dc263-112">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="dc263-113">Este documento se aplica ao Razor Pages, a controladores de API e controladores com exibição.</span><span class="sxs-lookup"><span data-stu-id="dc263-113">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="dc263-114">[Exibir ou baixar exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="dc263-114">[View or download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="dc263-115">Como os filtros funcionam</span><span class="sxs-lookup"><span data-stu-id="dc263-115">How filters work</span></span>

<span data-ttu-id="dc263-116">Os filtros são executados dentro do *pipeline de invocação de ações do ASP.NET Core*, às vezes chamado de *pipeline de filtros*.</span><span class="sxs-lookup"><span data-stu-id="dc263-116">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="dc263-117">O pipeline de filtros é executado após o ASP.NET Core selecionar a ação a ser executada.</span><span class="sxs-lookup"><span data-stu-id="dc263-117">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![A solicitação é processada por meio de Outro Middleware, do Middleware de Roteamento, da Seleção de Ação e do Pipeline de Invocação de Ações do ASP.NET Core.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="dc263-120">Tipos de filtro</span><span class="sxs-lookup"><span data-stu-id="dc263-120">Filter types</span></span>

<span data-ttu-id="dc263-121">Cada tipo de filtro é executado em um estágio diferente no pipeline de filtros:</span><span class="sxs-lookup"><span data-stu-id="dc263-121">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="dc263-122">[Filtros de autorização](#authorization-filters) são executados primeiro e são usados para determinar se o usuário tem autorização para a solicitação.</span><span class="sxs-lookup"><span data-stu-id="dc263-122">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="dc263-123">Os filtros de autorização podem causar um curto-circuito do pipeline se uma solicitação não for autorizada.</span><span class="sxs-lookup"><span data-stu-id="dc263-123">Authorization filters short-circuit the pipeline if the request is unauthorized.</span></span>

* <span data-ttu-id="dc263-124">[Filtros de recurso](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="dc263-124">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="dc263-125">Execute após a autorização.</span><span class="sxs-lookup"><span data-stu-id="dc263-125">Run after authorization.</span></span>  
  * <span data-ttu-id="dc263-126"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> pode executar o código antes do restante do pipeline de filtros.</span><span class="sxs-lookup"><span data-stu-id="dc263-126"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> can run code before the rest of the filter pipeline.</span></span> <span data-ttu-id="dc263-127">Por exemplo, `OnResourceExecuting` pode executar o código antes da associação de modelo.</span><span class="sxs-lookup"><span data-stu-id="dc263-127">For example, `OnResourceExecuting` can run code before model binding.</span></span>
  * <span data-ttu-id="dc263-128"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> pode executar o código após a conclusão do restante do pipeline.</span><span class="sxs-lookup"><span data-stu-id="dc263-128"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> can run code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="dc263-129">[Filtros de ação](#action-filters) podem executar código imediatamente antes e depois de um método de ação individual ser chamado.</span><span class="sxs-lookup"><span data-stu-id="dc263-129">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="dc263-130">Eles podem ser usados para manipular os argumentos passados para uma ação, bem como o resultado da ação.</span><span class="sxs-lookup"><span data-stu-id="dc263-130">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span> <span data-ttu-id="dc263-131">Os filtros de ação **não** são compatíveis com o Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="dc263-131">Action filters are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="dc263-132">[Filtros de exceção](#exception-filters) são usados para aplicar políticas globais para exceções sem tratamento que ocorrem antes que qualquer coisa tenha sido gravada no corpo da resposta.</span><span class="sxs-lookup"><span data-stu-id="dc263-132">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="dc263-133">[Filtros de resposta](#result-filters) podem executar código imediatamente antes e depois da execução de resultados de ações individuais.</span><span class="sxs-lookup"><span data-stu-id="dc263-133">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="dc263-134">Eles são executados somente quando o método de ação é executado com êxito.</span><span class="sxs-lookup"><span data-stu-id="dc263-134">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="dc263-135">Eles são úteis para a lógica que precisa envolver a execução da exibição ou do formatador.</span><span class="sxs-lookup"><span data-stu-id="dc263-135">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="dc263-136">O diagrama a seguir mostra como os tipos de filtro interagem no pipeline de filtros.</span><span class="sxs-lookup"><span data-stu-id="dc263-136">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![A solicitação é processada por meio de Filtros de autorização, Filtros de recurso, Model binding, Filtros de ação, Execução de ação e Conversão do resultado de ação, Filtros de exceção, Filtros de resultado e Execução de resultado.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="dc263-139">Implementação</span><span class="sxs-lookup"><span data-stu-id="dc263-139">Implementation</span></span>

<span data-ttu-id="dc263-140">Os filtros dão suporte a implementações síncronas e assíncronas por meio de diferentes definições de interface.</span><span class="sxs-lookup"><span data-stu-id="dc263-140">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="dc263-141">Os filtros síncronos podem executar o código antes (`On-Stage-Executing`) e depois (`On-Stage-Executed`) do estágio de pipeline.</span><span class="sxs-lookup"><span data-stu-id="dc263-141">Synchronous filters can run code before (`On-Stage-Executing`) and after (`On-Stage-Executed`) their pipeline stage.</span></span> <span data-ttu-id="dc263-142">Por exemplo, `OnActionExecuting` é chamado antes que o método de ação seja chamado.</span><span class="sxs-lookup"><span data-stu-id="dc263-142">For example, `OnActionExecuting` is called before the action method is called.</span></span> <span data-ttu-id="dc263-143">`OnActionExecuted` é chamado após o método de ação retornar.</span><span class="sxs-lookup"><span data-stu-id="dc263-143">`OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="dc263-144">Os filtros assíncronos definem um método `On-Stage-ExecutionAsync`:</span><span class="sxs-lookup"><span data-stu-id="dc263-144">Asynchronous filters define an `On-Stage-ExecutionAsync` method:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="dc263-145">No código anterior, o `SampleAsyncActionFilter` tem um <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) que executa o método de ação.</span><span class="sxs-lookup"><span data-stu-id="dc263-145">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)  that executes the action method.</span></span>  <span data-ttu-id="dc263-146">Cada um dos métodos `On-Stage-ExecutionAsync` usa um `FilterType-ExecutionDelegate` que executa o estágio de pipeline do filtro.</span><span class="sxs-lookup"><span data-stu-id="dc263-146">Each of the `On-Stage-ExecutionAsync` methods take a `FilterType-ExecutionDelegate` that executes the filter's pipeline stage.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="dc263-147">Vários estágios do filtro</span><span class="sxs-lookup"><span data-stu-id="dc263-147">Multiple filter stages</span></span>

<span data-ttu-id="dc263-148">É possível implementar interfaces para vários estágios do filtro em uma única classe.</span><span class="sxs-lookup"><span data-stu-id="dc263-148">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="dc263-149">Por exemplo, a classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> implementa `IActionFilter`, `IResultFilter` e os respectivos equivalentes assíncronos.</span><span class="sxs-lookup"><span data-stu-id="dc263-149">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

<span data-ttu-id="dc263-150">Implemente **ou** a versão assíncrona ou a versão síncrona de uma interface de filtro, **não** ambas.</span><span class="sxs-lookup"><span data-stu-id="dc263-150">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="dc263-151">Primeiro, o tempo de execução verifica se o filtro implementa a interface assíncrona e, se for esse o caso, a chama.</span><span class="sxs-lookup"><span data-stu-id="dc263-151">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="dc263-152">Caso contrário, ela chama os métodos da interface síncrona.</span><span class="sxs-lookup"><span data-stu-id="dc263-152">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="dc263-153">Se as interfaces síncrona e assíncrona forem implementadas em uma classe, somente o método assíncrono será chamado.</span><span class="sxs-lookup"><span data-stu-id="dc263-153">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="dc263-154">Ao usar classes abstratas como <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, substitua apenas os métodos síncronos ou o método assíncrono para cada tipo de filtro.</span><span class="sxs-lookup"><span data-stu-id="dc263-154">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="dc263-155">Atributos de filtro internos</span><span class="sxs-lookup"><span data-stu-id="dc263-155">Built-in filter attributes</span></span>

<span data-ttu-id="dc263-156">O ASP.NET Core inclui filtros internos baseados em atributos que podem ser organizados em subclasses e personalizados.</span><span class="sxs-lookup"><span data-stu-id="dc263-156">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="dc263-157">Por exemplo, o filtro de resultado a seguir adiciona um cabeçalho à resposta:</span><span class="sxs-lookup"><span data-stu-id="dc263-157">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="dc263-158">Os atributos permitem que os filtros aceitem argumentos, conforme mostrado no exemplo acima.</span><span class="sxs-lookup"><span data-stu-id="dc263-158">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="dc263-159">Aplique o `AddHeaderAttribute` a um controlador ou método de ação e especifique o nome e o valor do cabeçalho HTTP:</span><span class="sxs-lookup"><span data-stu-id="dc263-159">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

<span data-ttu-id="dc263-160">Várias interfaces de filtro têm atributos correspondentes que podem ser usados como classes base para implementações personalizadas.</span><span class="sxs-lookup"><span data-stu-id="dc263-160">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="dc263-161">Atributos de filtro:</span><span class="sxs-lookup"><span data-stu-id="dc263-161">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="dc263-162">Escopos e ordem de execução dos filtros</span><span class="sxs-lookup"><span data-stu-id="dc263-162">Filter scopes and order of execution</span></span>

<span data-ttu-id="dc263-163">Um filtro pode ser adicionado ao pipeline com um de três *escopos*:</span><span class="sxs-lookup"><span data-stu-id="dc263-163">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="dc263-164">Usando um atributo em uma ação.</span><span class="sxs-lookup"><span data-stu-id="dc263-164">Using an attribute on an action.</span></span>
* <span data-ttu-id="dc263-165">Usando um atributo em um controlador.</span><span class="sxs-lookup"><span data-stu-id="dc263-165">Using an attribute on a controller.</span></span>
* <span data-ttu-id="dc263-166">Globalmente para todos os controladores e ações, como mostra o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="dc263-166">Globally for all controllers and actions as shown in the following code:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="dc263-167">O código anterior adiciona três filtros globalmente usando a coleção [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters).</span><span class="sxs-lookup"><span data-stu-id="dc263-167">The preceding code adds three filters globally using the [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) collection.</span></span>

### <a name="default-order-of-execution"></a><span data-ttu-id="dc263-168">Ordem padrão de execução</span><span class="sxs-lookup"><span data-stu-id="dc263-168">Default order of execution</span></span>

<span data-ttu-id="dc263-169">Quando há vários filtros para um determinado estágio do pipeline, o escopo determina a ordem padrão de execução dos filtros.</span><span class="sxs-lookup"><span data-stu-id="dc263-169">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="dc263-170">Filtros globais circundam filtros de classe, que, por sua vez, circundam filtros de método.</span><span class="sxs-lookup"><span data-stu-id="dc263-170">Global filters surround class filters, which in turn surround method filters.</span></span>

<span data-ttu-id="dc263-171">Como resultado do aninhamento de filtro, o código *posterior* dos filtros é executado na ordem inversa do código *anterior*.</span><span class="sxs-lookup"><span data-stu-id="dc263-171">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="dc263-172">A sequência de filtro:</span><span class="sxs-lookup"><span data-stu-id="dc263-172">The filter sequence:</span></span>

* <span data-ttu-id="dc263-173">O código *anterior* dos filtros globais.</span><span class="sxs-lookup"><span data-stu-id="dc263-173">The *before* code of global filters.</span></span>
  * <span data-ttu-id="dc263-174">O código *anterior* dos filtros de controlador.</span><span class="sxs-lookup"><span data-stu-id="dc263-174">The *before* code of controller filters.</span></span>
    * <span data-ttu-id="dc263-175">O código *anterior* dos filtros de método de ação.</span><span class="sxs-lookup"><span data-stu-id="dc263-175">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="dc263-176">O código *posterior* dos filtros de método de ação.</span><span class="sxs-lookup"><span data-stu-id="dc263-176">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="dc263-177">O código *posterior* dos filtros de controlador.</span><span class="sxs-lookup"><span data-stu-id="dc263-177">The *after* code of controller filters.</span></span>
* <span data-ttu-id="dc263-178">O código *posterior* dos filtros globais.</span><span class="sxs-lookup"><span data-stu-id="dc263-178">The *after* code of global filters.</span></span>
  
<span data-ttu-id="dc263-179">O exemplo a seguir ilustra a ordem na qual os métodos de filtro são chamados para filtros de ação síncrona.</span><span class="sxs-lookup"><span data-stu-id="dc263-179">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="dc263-180">Sequência</span><span class="sxs-lookup"><span data-stu-id="dc263-180">Sequence</span></span> | <span data-ttu-id="dc263-181">Escopo do filtro</span><span class="sxs-lookup"><span data-stu-id="dc263-181">Filter scope</span></span> | <span data-ttu-id="dc263-182">Método do filtro</span><span class="sxs-lookup"><span data-stu-id="dc263-182">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="dc263-183">1</span><span class="sxs-lookup"><span data-stu-id="dc263-183">1</span></span> | <span data-ttu-id="dc263-184">Global</span><span class="sxs-lookup"><span data-stu-id="dc263-184">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="dc263-185">2</span><span class="sxs-lookup"><span data-stu-id="dc263-185">2</span></span> | <span data-ttu-id="dc263-186">Controlador</span><span class="sxs-lookup"><span data-stu-id="dc263-186">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="dc263-187">3</span><span class="sxs-lookup"><span data-stu-id="dc263-187">3</span></span> | <span data-ttu-id="dc263-188">Método</span><span class="sxs-lookup"><span data-stu-id="dc263-188">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="dc263-189">4</span><span class="sxs-lookup"><span data-stu-id="dc263-189">4</span></span> | <span data-ttu-id="dc263-190">Método</span><span class="sxs-lookup"><span data-stu-id="dc263-190">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="dc263-191">5</span><span class="sxs-lookup"><span data-stu-id="dc263-191">5</span></span> | <span data-ttu-id="dc263-192">Controlador</span><span class="sxs-lookup"><span data-stu-id="dc263-192">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="dc263-193">6</span><span class="sxs-lookup"><span data-stu-id="dc263-193">6</span></span> | <span data-ttu-id="dc263-194">Global</span><span class="sxs-lookup"><span data-stu-id="dc263-194">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="dc263-195">Esta sequência mostra:</span><span class="sxs-lookup"><span data-stu-id="dc263-195">This sequence shows:</span></span>

* <span data-ttu-id="dc263-196">O filtro de método está aninhado no filtro de controlador.</span><span class="sxs-lookup"><span data-stu-id="dc263-196">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="dc263-197">O filtro de controlador está aninhado no filtro global.</span><span class="sxs-lookup"><span data-stu-id="dc263-197">The controller filter is nested within the global filter.</span></span>

### <a name="controller-and-razor-page-level-filters"></a><span data-ttu-id="dc263-198">Filtros no nível do controlador e do Razor Page</span><span class="sxs-lookup"><span data-stu-id="dc263-198">Controller and Razor Page level filters</span></span>

<span data-ttu-id="dc263-199">Cada controlador que herda da classe base <xref:Microsoft.AspNetCore.Mvc.Controller> inclui os métodos [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) e [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="dc263-199">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="dc263-200">Estes métodos:</span><span class="sxs-lookup"><span data-stu-id="dc263-200">These methods:</span></span>

* <span data-ttu-id="dc263-201">Encapsulam os filtros executados para uma determinada ação.</span><span class="sxs-lookup"><span data-stu-id="dc263-201">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="dc263-202">`OnActionExecuting` é chamado antes de qualquer um dos filtros da ação.</span><span class="sxs-lookup"><span data-stu-id="dc263-202">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="dc263-203">`OnActionExecuted` é chamado após todos os filtros da ação.</span><span class="sxs-lookup"><span data-stu-id="dc263-203">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="dc263-204">`OnActionExecutionAsync` é chamado antes de qualquer um dos filtros da ação.</span><span class="sxs-lookup"><span data-stu-id="dc263-204">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="dc263-205">O código no filtro após `next` é executado após o método de ação.</span><span class="sxs-lookup"><span data-stu-id="dc263-205">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="dc263-206">Por exemplo, no download de exemplo, `MySampleActionFilter` é aplicado globalmente na inicialização.</span><span class="sxs-lookup"><span data-stu-id="dc263-206">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="dc263-207">O `TestController`:</span><span class="sxs-lookup"><span data-stu-id="dc263-207">The `TestController`:</span></span>

* <span data-ttu-id="dc263-208">Aplica `SampleActionFilterAttribute` (`[SampleActionFilter]`) à ação `FilterTest2`:</span><span class="sxs-lookup"><span data-stu-id="dc263-208">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action:</span></span>
* <span data-ttu-id="dc263-209">Substitui `OnActionExecuting` e `OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="dc263-209">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="dc263-210">Navegar até `https://localhost:5001/Test/FilterTest2` executa o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="dc263-210">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="dc263-211">Para Razor Pages, confira [Implementar filtros do Razor Page substituindo os métodos de filtro](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="dc263-211">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="dc263-212">Substituindo a ordem padrão</span><span class="sxs-lookup"><span data-stu-id="dc263-212">Overriding the default order</span></span>

<span data-ttu-id="dc263-213">É possível substituir a sequência padrão de execução implementando <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span><span class="sxs-lookup"><span data-stu-id="dc263-213">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="dc263-214">`IOrderedFilter` expõe a propriedade <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> que tem precedência sobre o escopo para determinar a ordem da execução.</span><span class="sxs-lookup"><span data-stu-id="dc263-214">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="dc263-215">Um filtro com um valor de `Order` menor:</span><span class="sxs-lookup"><span data-stu-id="dc263-215">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="dc263-216">Executa o código *anterior* antes de um filtro com um valor mais alto de `Order`.</span><span class="sxs-lookup"><span data-stu-id="dc263-216">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="dc263-217">Executa o código *posterior* após um filtro com um valor mais alto de `Order`.</span><span class="sxs-lookup"><span data-stu-id="dc263-217">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="dc263-218">A propriedade `Order` pode ser definida com um parâmetro de construtor:</span><span class="sxs-lookup"><span data-stu-id="dc263-218">The `Order` property can be set with a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="dc263-219">Considere os mesmos filtros de 3 ações mostrados no exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="dc263-219">Consider the same 3 action filters shown in the preceding example.</span></span> <span data-ttu-id="dc263-220">Se a propriedade `Order` do controlador e os filtros globais estiverem definidos como 1 e 2, respectivamente, a ordem de execução será invertida.</span><span class="sxs-lookup"><span data-stu-id="dc263-220">If the `Order` property of the controller and global filters is set to 1 and 2 respectively, the order of execution is reversed.</span></span>

| <span data-ttu-id="dc263-221">Sequência</span><span class="sxs-lookup"><span data-stu-id="dc263-221">Sequence</span></span> | <span data-ttu-id="dc263-222">Escopo do filtro</span><span class="sxs-lookup"><span data-stu-id="dc263-222">Filter scope</span></span> | <span data-ttu-id="dc263-223">Propriedade `Order`</span><span class="sxs-lookup"><span data-stu-id="dc263-223">`Order` property</span></span> | <span data-ttu-id="dc263-224">Método do filtro</span><span class="sxs-lookup"><span data-stu-id="dc263-224">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="dc263-225">1</span><span class="sxs-lookup"><span data-stu-id="dc263-225">1</span></span> | <span data-ttu-id="dc263-226">Método</span><span class="sxs-lookup"><span data-stu-id="dc263-226">Method</span></span> | <span data-ttu-id="dc263-227">0</span><span class="sxs-lookup"><span data-stu-id="dc263-227">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="dc263-228">2</span><span class="sxs-lookup"><span data-stu-id="dc263-228">2</span></span> | <span data-ttu-id="dc263-229">Controlador</span><span class="sxs-lookup"><span data-stu-id="dc263-229">Controller</span></span> | <span data-ttu-id="dc263-230">1</span><span class="sxs-lookup"><span data-stu-id="dc263-230">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="dc263-231">3</span><span class="sxs-lookup"><span data-stu-id="dc263-231">3</span></span> | <span data-ttu-id="dc263-232">Global</span><span class="sxs-lookup"><span data-stu-id="dc263-232">Global</span></span> | <span data-ttu-id="dc263-233">2</span><span class="sxs-lookup"><span data-stu-id="dc263-233">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="dc263-234">4</span><span class="sxs-lookup"><span data-stu-id="dc263-234">4</span></span> | <span data-ttu-id="dc263-235">Global</span><span class="sxs-lookup"><span data-stu-id="dc263-235">Global</span></span> | <span data-ttu-id="dc263-236">2</span><span class="sxs-lookup"><span data-stu-id="dc263-236">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="dc263-237">5</span><span class="sxs-lookup"><span data-stu-id="dc263-237">5</span></span> | <span data-ttu-id="dc263-238">Controlador</span><span class="sxs-lookup"><span data-stu-id="dc263-238">Controller</span></span> | <span data-ttu-id="dc263-239">1</span><span class="sxs-lookup"><span data-stu-id="dc263-239">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="dc263-240">6</span><span class="sxs-lookup"><span data-stu-id="dc263-240">6</span></span> | <span data-ttu-id="dc263-241">Método</span><span class="sxs-lookup"><span data-stu-id="dc263-241">Method</span></span> | <span data-ttu-id="dc263-242">0</span><span class="sxs-lookup"><span data-stu-id="dc263-242">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="dc263-243">A propriedade `Order` substitui o escopo ao determinar a ordem na qual os filtros serão executados.</span><span class="sxs-lookup"><span data-stu-id="dc263-243">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="dc263-244">Os filtros são classificados primeiro pela ordem e o escopo é usado para desempatar.</span><span class="sxs-lookup"><span data-stu-id="dc263-244">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="dc263-245">Todos os filtros internos implementam `IOrderedFilter` e definem o valor de `Order` padrão como 0.</span><span class="sxs-lookup"><span data-stu-id="dc263-245">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="dc263-246">Para os filtros internos, o escopo determina a ordem, a menos que você defina `Order` com um valor diferente de zero.</span><span class="sxs-lookup"><span data-stu-id="dc263-246">For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="dc263-247">Cancelamento e curto-circuito</span><span class="sxs-lookup"><span data-stu-id="dc263-247">Cancellation and short-circuiting</span></span>

<span data-ttu-id="dc263-248">O pipeline de filtro pode sofrer um curto-circuito por meio da configuração da propriedade <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> no parâmetro <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> fornecido ao método do filtro.</span><span class="sxs-lookup"><span data-stu-id="dc263-248">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="dc263-249">Por exemplo, o filtro de Recurso a seguir impede que o resto do pipeline seja executado:</span><span class="sxs-lookup"><span data-stu-id="dc263-249">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="dc263-250">No código a seguir, os filtros `ShortCircuitingResourceFilter` e `AddHeader` têm como destino o método de ação `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="dc263-250">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="dc263-251">O `ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="dc263-251">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="dc263-252">É executado primeiro, porque ele é um filtro de recurso e `AddHeader` é um filtro de ação.</span><span class="sxs-lookup"><span data-stu-id="dc263-252">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="dc263-253">Causa um curto-circuito no restante do pipeline.</span><span class="sxs-lookup"><span data-stu-id="dc263-253">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="dc263-254">Portanto, o filtro `AddHeader` nunca é executado para a ação `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="dc263-254">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="dc263-255">Esse comportamento seria o mesmo se os dois filtros fossem aplicados no nível do método de ação, desde que `ShortCircuitingResourceFilter` fosse executado primeiro.</span><span class="sxs-lookup"><span data-stu-id="dc263-255">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="dc263-256">O `ShortCircuitingResourceFilter` é executado primeiro, devido ao seu tipo de filtro ou pelo uso explícito da propriedade `Order`.</span><span class="sxs-lookup"><span data-stu-id="dc263-256">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="dc263-257">Injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="dc263-257">Dependency injection</span></span>

<span data-ttu-id="dc263-258">Filtros podem ser adicionados por tipo ou por instância.</span><span class="sxs-lookup"><span data-stu-id="dc263-258">Filters can be added by type or by instance.</span></span> <span data-ttu-id="dc263-259">Se você adicionar uma instância, ela será usada para cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="dc263-259">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="dc263-260">Se um tipo for adicionado, ele será ativado pelo tipo.</span><span class="sxs-lookup"><span data-stu-id="dc263-260">If a type is added, it's type-activated.</span></span> <span data-ttu-id="dc263-261">Um filtro ativado por tipo significa:</span><span class="sxs-lookup"><span data-stu-id="dc263-261">A type-activated filter means:</span></span>

* <span data-ttu-id="dc263-262">Uma instância é criada para cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="dc263-262">An instance is created for each request.</span></span>
* <span data-ttu-id="dc263-263">Qualquer dependência de construtor é preenchida pela [injeção de dependência](xref:fundamentals/dependency-injection) (DI).</span><span class="sxs-lookup"><span data-stu-id="dc263-263">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="dc263-264">Filtros que são implementados como atributos e adicionados diretamente a classes de controlador ou métodos de ação não podem ter dependências de construtor fornecidas pela DI ([injeção de dependência](xref:fundamentals/dependency-injection)).</span><span class="sxs-lookup"><span data-stu-id="dc263-264">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="dc263-265">Dependências de construtor não podem ser fornecidas pela DI porque:</span><span class="sxs-lookup"><span data-stu-id="dc263-265">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="dc263-266">Os atributos precisam ter os parâmetros de construtor fornecidos quando são aplicados.</span><span class="sxs-lookup"><span data-stu-id="dc263-266">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="dc263-267">Essa é uma limitação do funcionamento dos atributos.</span><span class="sxs-lookup"><span data-stu-id="dc263-267">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="dc263-268">Os filtros a seguir são compatíveis com dependências de construtor fornecidas pela injeção de dependência:</span><span class="sxs-lookup"><span data-stu-id="dc263-268">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="dc263-269"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementado no atributo.</span><span class="sxs-lookup"><span data-stu-id="dc263-269"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="dc263-270">Os filtros anteriores podem ser aplicados a um método de ação ou controlador:</span><span class="sxs-lookup"><span data-stu-id="dc263-270">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="dc263-271">Os agentes estão disponíveis na DI.</span><span class="sxs-lookup"><span data-stu-id="dc263-271">Loggers are available from DI.</span></span> <span data-ttu-id="dc263-272">No entanto, evite criar e usar filtros apenas para fins de registro em log.</span><span class="sxs-lookup"><span data-stu-id="dc263-272">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="dc263-273">O [registro em log da estrutura interna](xref:fundamentals/logging/index) normalmente fornece o que é necessário para o registro em log.</span><span class="sxs-lookup"><span data-stu-id="dc263-273">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="dc263-274">Registro em log adicionado aos filtros:</span><span class="sxs-lookup"><span data-stu-id="dc263-274">Logging added to filters:</span></span>

* <span data-ttu-id="dc263-275">Deve se concentrar em questões de domínio de negócios ou comportamento específico ao filtro.</span><span class="sxs-lookup"><span data-stu-id="dc263-275">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="dc263-276">**Não** deve registrar ações ou outros eventos da estrutura.</span><span class="sxs-lookup"><span data-stu-id="dc263-276">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="dc263-277">Os filtros internos registram ações e eventos da estrutura.</span><span class="sxs-lookup"><span data-stu-id="dc263-277">The built in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="dc263-278">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="dc263-278">ServiceFilterAttribute</span></span>

<span data-ttu-id="dc263-279">Os tipos de implementação do filtro de serviço são registrados em `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="dc263-279">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="dc263-280">Um <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> recupera uma instância do filtro da DI.</span><span class="sxs-lookup"><span data-stu-id="dc263-280">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="dc263-281">O código a seguir mostra `AddHeaderResultServiceFilter`:</span><span class="sxs-lookup"><span data-stu-id="dc263-281">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="dc263-282">No código a seguir, `AddHeaderResultServiceFilter` é adicionado ao contêiner de DI:</span><span class="sxs-lookup"><span data-stu-id="dc263-282">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="dc263-283">No código a seguir, o atributo `ServiceFilter` recupera uma instância do filtro `AddHeaderResultServiceFilter` da DI:</span><span class="sxs-lookup"><span data-stu-id="dc263-283">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="dc263-284">Ao usar `ServiceFilterAttribute`, configurar [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="dc263-284">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="dc263-285">Fornece uma dica de que a instância de filtro *pode* ser reutilizada fora do escopo de solicitação em que foi criada.</span><span class="sxs-lookup"><span data-stu-id="dc263-285">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="dc263-286">O tempo de execução do ASP.NET Core não garante:</span><span class="sxs-lookup"><span data-stu-id="dc263-286">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="dc263-287">Que uma única instância do filtro será criada.</span><span class="sxs-lookup"><span data-stu-id="dc263-287">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="dc263-288">Que o filtro não será solicitado novamente no contêiner de DI em algum momento posterior.</span><span class="sxs-lookup"><span data-stu-id="dc263-288">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="dc263-289">Não deve ser usado com um filtro que dependa dos serviços com um tempo de vida diferente de singleton.</span><span class="sxs-lookup"><span data-stu-id="dc263-289">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="dc263-290"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="dc263-290"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="dc263-291">`IFilterFactory` expõe o método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> para criar uma instância de <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="dc263-291">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="dc263-292">`CreateInstance` carrega o tipo especificado na DI.</span><span class="sxs-lookup"><span data-stu-id="dc263-292">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="dc263-293">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="dc263-293">TypeFilterAttribute</span></span>

<span data-ttu-id="dc263-294">O <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> é semelhante ao <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, mas seu tipo não é resolvido diretamente por meio do contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="dc263-294"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="dc263-295">Ele cria uma instância do tipo usando <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="dc263-295">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="dc263-296">Como os tipos `TypeFilterAttribute` não são resolvidos diretamente do contêiner de DI:</span><span class="sxs-lookup"><span data-stu-id="dc263-296">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="dc263-297">Os tipos referenciados usando o `TypeFilterAttribute` não precisam ser registrados no contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="dc263-297">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="dc263-298">Eles têm suas dependências atendidas pelo contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="dc263-298">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="dc263-299">Opcionalmente, o `TypeFilterAttribute` pode aceitar argumentos de construtor para o tipo.</span><span class="sxs-lookup"><span data-stu-id="dc263-299">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="dc263-300">Ao usar `TypeFilterAttribute`, configurar [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="dc263-300">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="dc263-301">Fornece uma dica de que a instância de filtro *pode* ser reutilizada fora do escopo de solicitação em que foi criada.</span><span class="sxs-lookup"><span data-stu-id="dc263-301">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="dc263-302">O tempo de execução do ASP.NET Core não fornece garantias de que uma única instância do filtro será criada.</span><span class="sxs-lookup"><span data-stu-id="dc263-302">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="dc263-303">Não deve ser usado com um filtro que dependa dos serviços com um tempo de vida diferente de singleton.</span><span class="sxs-lookup"><span data-stu-id="dc263-303">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="dc263-304">O exemplo a seguir mostra como passar argumentos para um tipo usando `TypeFilterAttribute`:</span><span class="sxs-lookup"><span data-stu-id="dc263-304">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="dc263-305">Filtros de autorização</span><span class="sxs-lookup"><span data-stu-id="dc263-305">Authorization filters</span></span>

<span data-ttu-id="dc263-306">Filtros de autorização:</span><span class="sxs-lookup"><span data-stu-id="dc263-306">Authorization filters:</span></span>

* <span data-ttu-id="dc263-307">São os primeiros filtros executados no pipeline de filtro.</span><span class="sxs-lookup"><span data-stu-id="dc263-307">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="dc263-308">Controlam o acesso aos métodos de ação.</span><span class="sxs-lookup"><span data-stu-id="dc263-308">Control access to action methods.</span></span>
* <span data-ttu-id="dc263-309">Têm um método anterior, mas não têm um método posterior.</span><span class="sxs-lookup"><span data-stu-id="dc263-309">Have a before method, but no after method.</span></span>

<span data-ttu-id="dc263-310">Filtros de autorização personalizados exigem uma estrutura de autorização personalizada.</span><span class="sxs-lookup"><span data-stu-id="dc263-310">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="dc263-311">Prefira configurar as políticas de autorização ou escrever uma política de autorização personalizada em vez de escrever um filtro personalizado.</span><span class="sxs-lookup"><span data-stu-id="dc263-311">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="dc263-312">O filtro de autorização interno:</span><span class="sxs-lookup"><span data-stu-id="dc263-312">The built-in authorization filter:</span></span>

* <span data-ttu-id="dc263-313">Chama o sistema de autorização.</span><span class="sxs-lookup"><span data-stu-id="dc263-313">Calls the authorization system.</span></span>
* <span data-ttu-id="dc263-314">Não autoriza solicitações.</span><span class="sxs-lookup"><span data-stu-id="dc263-314">Does not authorize requests.</span></span>

<span data-ttu-id="dc263-315">**Não** gera exceções dentro de filtros de autorização:</span><span class="sxs-lookup"><span data-stu-id="dc263-315">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="dc263-316">A exceção não será tratada.</span><span class="sxs-lookup"><span data-stu-id="dc263-316">The exception will not be handled.</span></span>
* <span data-ttu-id="dc263-317">Os filtros de exceção não tratarão a exceção.</span><span class="sxs-lookup"><span data-stu-id="dc263-317">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="dc263-318">Considere a possibilidade de emitir um desafio quando ocorrer uma exceção em um filtro de autorização.</span><span class="sxs-lookup"><span data-stu-id="dc263-318">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="dc263-319">Saiba mais sobre [Autorização](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="dc263-319">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="dc263-320">Filtros de recurso</span><span class="sxs-lookup"><span data-stu-id="dc263-320">Resource filters</span></span>

<span data-ttu-id="dc263-321">Filtros de recurso:</span><span class="sxs-lookup"><span data-stu-id="dc263-321">Resource filters:</span></span>

* <span data-ttu-id="dc263-322">Implementam a interface <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.</span><span class="sxs-lookup"><span data-stu-id="dc263-322">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="dc263-323">A execução encapsula grande parte do pipeline de filtro.</span><span class="sxs-lookup"><span data-stu-id="dc263-323">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="dc263-324">Somente os [Filtros de autorização](#authorization-filters) são executados antes dos filtros de recurso.</span><span class="sxs-lookup"><span data-stu-id="dc263-324">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="dc263-325">Os filtros de recursos são úteis para causar um curto-circuito na maior parte do pipeline.</span><span class="sxs-lookup"><span data-stu-id="dc263-325">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="dc263-326">Por exemplo, um filtro de cache pode evitar o restante do pipeline em uma ocorrência no cache.</span><span class="sxs-lookup"><span data-stu-id="dc263-326">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="dc263-327">Exemplos de filtros de recurso:</span><span class="sxs-lookup"><span data-stu-id="dc263-327">Resource filter examples:</span></span>

* <span data-ttu-id="dc263-328">[O filtro de recurso em curto-circuito](#short-circuiting-resource-filter) mostrado anteriormente.</span><span class="sxs-lookup"><span data-stu-id="dc263-328">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="dc263-329">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="dc263-329">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="dc263-330">Impede o model binding de acessar os dados do formulário.</span><span class="sxs-lookup"><span data-stu-id="dc263-330">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="dc263-331">Usado para uploads de arquivos grandes para impedir que os dados de formulário sejam lidos na memória.</span><span class="sxs-lookup"><span data-stu-id="dc263-331">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="dc263-332">Filtros de ação</span><span class="sxs-lookup"><span data-stu-id="dc263-332">Action filters</span></span>

> [!IMPORTANT]
> <span data-ttu-id="dc263-333">Os filtros de ação **não** se aplicam ao Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="dc263-333">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="dc263-334">O Razor Pages é compatível com <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> e <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter>.</span><span class="sxs-lookup"><span data-stu-id="dc263-334">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="dc263-335">Para obter mais informações, confira [Métodos de filtro para Páginas Razor](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="dc263-335">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="dc263-336">Filtros de ação:</span><span class="sxs-lookup"><span data-stu-id="dc263-336">Action filters:</span></span>

* <span data-ttu-id="dc263-337">Implementam a interface <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.</span><span class="sxs-lookup"><span data-stu-id="dc263-337">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="dc263-338">A execução deles envolve a execução de métodos de ação.</span><span class="sxs-lookup"><span data-stu-id="dc263-338">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="dc263-339">O código a seguir mostra um exemplo de filtro de ação:</span><span class="sxs-lookup"><span data-stu-id="dc263-339">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="dc263-340">A classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> fornece as seguintes propriedades:</span><span class="sxs-lookup"><span data-stu-id="dc263-340">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="dc263-341"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - permite a leitura das entradas para um método de ação.</span><span class="sxs-lookup"><span data-stu-id="dc263-341"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables the inputs to an action method be read.</span></span>
* <span data-ttu-id="dc263-342"><xref:Microsoft.AspNetCore.Mvc.Controller> – permite a manipulação da instância do controlador.</span><span class="sxs-lookup"><span data-stu-id="dc263-342"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="dc263-343"><xref:System.Web.Mvc.ActionExecutingContext.Result> – configuração de `Result` execução de curtos-circuitos do método de ação e dos filtros de ação posteriores.</span><span class="sxs-lookup"><span data-stu-id="dc263-343"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="dc263-344">Gerar uma exceção em um método de ação:</span><span class="sxs-lookup"><span data-stu-id="dc263-344">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="dc263-345">Impede a execução de filtros subsequentes.</span><span class="sxs-lookup"><span data-stu-id="dc263-345">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="dc263-346">Ao contrário da configuração `Result`, é tratada como uma falha e não como um resultado bem-sucedido.</span><span class="sxs-lookup"><span data-stu-id="dc263-346">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="dc263-347">A classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> fornece `Controller` e `Result`, além das seguintes propriedades:</span><span class="sxs-lookup"><span data-stu-id="dc263-347">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="dc263-348"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> – verdadeiro se a execução da ação tiver sofrido curto-circuito por outro filtro.</span><span class="sxs-lookup"><span data-stu-id="dc263-348"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="dc263-349"><xref:System.Web.Mvc.ActionExecutedContext.Exception> – não será nulo se a ação ou um filtro de ação executado antes tiver apresentado uma exceção.</span><span class="sxs-lookup"><span data-stu-id="dc263-349"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="dc263-350">Definir essa propriedade como nulo:</span><span class="sxs-lookup"><span data-stu-id="dc263-350">Setting this property to null:</span></span>

  * <span data-ttu-id="dc263-351">Trata efetivamente a exceção.</span><span class="sxs-lookup"><span data-stu-id="dc263-351">Effectively handles the exception.</span></span>
  * <span data-ttu-id="dc263-352">`Result` é executado como se tivesse retornado do método de ação.</span><span class="sxs-lookup"><span data-stu-id="dc263-352">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="dc263-353">Para um `IAsyncActionFilter`, uma chamada para o <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span><span class="sxs-lookup"><span data-stu-id="dc263-353">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="dc263-354">Executa todos os próximos filtros de ação e o método de ação.</span><span class="sxs-lookup"><span data-stu-id="dc263-354">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="dc263-355">Retorna `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="dc263-355">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="dc263-356">Para fazer um curto-circuito, atribua <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> a uma instância de resultado e não chame o `next` (`ActionExecutionDelegate`).</span><span class="sxs-lookup"><span data-stu-id="dc263-356">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="dc263-357">A estrutura fornece um <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> abstrato que você pode colocar em uma subclasse.</span><span class="sxs-lookup"><span data-stu-id="dc263-357">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="dc263-358">O filtro de ação `OnActionExecuting` pode ser usado para:</span><span class="sxs-lookup"><span data-stu-id="dc263-358">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="dc263-359">Validar o estado do modelo.</span><span class="sxs-lookup"><span data-stu-id="dc263-359">Validate model state.</span></span>
* <span data-ttu-id="dc263-360">Retornar um erro se o estado for inválido.</span><span class="sxs-lookup"><span data-stu-id="dc263-360">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="dc263-361">O método `OnActionExecuted` é executado após o método de ação:</span><span class="sxs-lookup"><span data-stu-id="dc263-361">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="dc263-362">E pode ver e manipular os resultados da ação por meio da propriedade <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result>.</span><span class="sxs-lookup"><span data-stu-id="dc263-362">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="dc263-363"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> é definido como verdadeiro se a execução da ação tiver sofrido curto-circuito por outro filtro.</span><span class="sxs-lookup"><span data-stu-id="dc263-363"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="dc263-364"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> é definido como um valor não nulo se a ação ou um filtro de ação posterior tiver apresentado uma exceção.</span><span class="sxs-lookup"><span data-stu-id="dc263-364"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="dc263-365">Definindo `Exception` como nulo:</span><span class="sxs-lookup"><span data-stu-id="dc263-365">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="dc263-366">Trata efetivamente uma exceção.</span><span class="sxs-lookup"><span data-stu-id="dc263-366">Effectively handles an exception.</span></span>
  * <span data-ttu-id="dc263-367">`ActionExecutedContext.Result` é executado como se fosse retornado normalmente do método de ação.</span><span class="sxs-lookup"><span data-stu-id="dc263-367">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="dc263-368">Filtros de exceção</span><span class="sxs-lookup"><span data-stu-id="dc263-368">Exception filters</span></span>

<span data-ttu-id="dc263-369">Filtros de exceção:</span><span class="sxs-lookup"><span data-stu-id="dc263-369">Exception filters:</span></span>

* <span data-ttu-id="dc263-370">Implementam <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span><span class="sxs-lookup"><span data-stu-id="dc263-370">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span> 
* <span data-ttu-id="dc263-371">Podem ser usados para implementar políticas de tratamento de erro comuns.</span><span class="sxs-lookup"><span data-stu-id="dc263-371">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="dc263-372">O exemplo de filtro de exceção a seguir usa uma exibição de erro personalizada para mostrar detalhes sobre exceções que ocorrem quando o aplicativo está em desenvolvimento:</span><span class="sxs-lookup"><span data-stu-id="dc263-372">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilterAttribute.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="dc263-373">Filtros de exceção:</span><span class="sxs-lookup"><span data-stu-id="dc263-373">Exception filters:</span></span>

* <span data-ttu-id="dc263-374">Não têm eventos anteriores nem posteriores.</span><span class="sxs-lookup"><span data-stu-id="dc263-374">Don't have before and after events.</span></span>
* <span data-ttu-id="dc263-375">Implementam <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span><span class="sxs-lookup"><span data-stu-id="dc263-375">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="dc263-376">Tratam as exceções sem tratamento que ocorrem no Razor Page ou na criação do controlador, no [model binding](xref:mvc/models/model-binding), nos filtros de ação ou nos métodos de ação.</span><span class="sxs-lookup"><span data-stu-id="dc263-376">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="dc263-377">**Não** capturam as exceções que ocorrem em filtros de recurso, em filtros de resultado ou na execução do resultado de MVC.</span><span class="sxs-lookup"><span data-stu-id="dc263-377">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="dc263-378">Para tratar uma exceção, defina a propriedade <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> como `true` ou grave uma resposta.</span><span class="sxs-lookup"><span data-stu-id="dc263-378">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="dc263-379">Isso interrompe a propagação da exceção.</span><span class="sxs-lookup"><span data-stu-id="dc263-379">This stops propagation of the exception.</span></span> <span data-ttu-id="dc263-380">Um filtro de exceção não pode transformar uma exceção em "êxito".</span><span class="sxs-lookup"><span data-stu-id="dc263-380">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="dc263-381">Somente um filtro de ação pode fazer isso.</span><span class="sxs-lookup"><span data-stu-id="dc263-381">Only an action filter can do that.</span></span>

<span data-ttu-id="dc263-382">Filtros de exceção:</span><span class="sxs-lookup"><span data-stu-id="dc263-382">Exception filters:</span></span>

* <span data-ttu-id="dc263-383">São bons para interceptar as exceções que ocorrem nas ações.</span><span class="sxs-lookup"><span data-stu-id="dc263-383">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="dc263-384">Não são tão flexíveis quanto o middleware de tratamento de erro.</span><span class="sxs-lookup"><span data-stu-id="dc263-384">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="dc263-385">Prefira o middleware para tratamento de exceção.</span><span class="sxs-lookup"><span data-stu-id="dc263-385">Prefer middleware for exception handling.</span></span> <span data-ttu-id="dc263-386">Use filtros de exceção apenas quando o tratamento de erros *for diferente* com base no método de ação chamado.</span><span class="sxs-lookup"><span data-stu-id="dc263-386">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="dc263-387">Por exemplo, um aplicativo pode ter métodos de ação para os pontos de extremidade da API e para modos de exibição/HTML.</span><span class="sxs-lookup"><span data-stu-id="dc263-387">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="dc263-388">Os pontos de extremidade da API podem retornar informações de erro como JSON, enquanto as ações baseadas em modo de exibição podem retornar uma página de erro como HTML.</span><span class="sxs-lookup"><span data-stu-id="dc263-388">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="dc263-389">Filtros de resultado</span><span class="sxs-lookup"><span data-stu-id="dc263-389">Result filters</span></span>

<span data-ttu-id="dc263-390">Filtros de resultado:</span><span class="sxs-lookup"><span data-stu-id="dc263-390">Result filters:</span></span>

* <span data-ttu-id="dc263-391">Implemente uma interface:</span><span class="sxs-lookup"><span data-stu-id="dc263-391">Implement an interface:</span></span>
  * <span data-ttu-id="dc263-392"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="dc263-392"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="dc263-393"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="dc263-393"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="dc263-394">A execução deles envolve a execução de resultados de ação.</span><span class="sxs-lookup"><span data-stu-id="dc263-394">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="dc263-395">IResultFilter e IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="dc263-395">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="dc263-396">O código a seguir mostra um filtro de resultado que adiciona um cabeçalho HTTP:</span><span class="sxs-lookup"><span data-stu-id="dc263-396">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="dc263-397">O tipo de resultado que está sendo executado depende da ação.</span><span class="sxs-lookup"><span data-stu-id="dc263-397">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="dc263-398">Uma ação que retorna um modo de exibição incluiria todo o processamento de Razor como parte do <xref:Microsoft.AspNetCore.Mvc.ViewResult> em execução.</span><span class="sxs-lookup"><span data-stu-id="dc263-398">An action returning a view would include all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="dc263-399">Um método de API pode executar alguma serialização como parte da execução do resultado.</span><span class="sxs-lookup"><span data-stu-id="dc263-399">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="dc263-400">Saiba mais sobre [resultados de ação](xref:mvc/controllers/actions)</span><span class="sxs-lookup"><span data-stu-id="dc263-400">Learn more about [action results](xref:mvc/controllers/actions)</span></span>

<span data-ttu-id="dc263-401">Filtros de resultado são executados somente para resultados bem-sucedidos – quando a ação ou filtros da ação produzem um resultado de ação.</span><span class="sxs-lookup"><span data-stu-id="dc263-401">Result filters are only executed for successful results - when the action or action filters produce an action result.</span></span> <span data-ttu-id="dc263-402">Filtros de resultado não são executados quando filtros de exceção tratam uma exceção.</span><span class="sxs-lookup"><span data-stu-id="dc263-402">Result filters are not executed when exception filters handle an exception.</span></span>

<span data-ttu-id="dc263-403">O método <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> pode fazer o curto-circuito da execução do resultado da ação e dos filtros de resultados posteriores definindo <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> como `true`.</span><span class="sxs-lookup"><span data-stu-id="dc263-403">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="dc263-404">Grave no objeto de resposta ao fazer um curto-circuito para evitar gerar uma resposta vazia.</span><span class="sxs-lookup"><span data-stu-id="dc263-404">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="dc263-405">A geração de uma exceção em `IResultFilter.OnResultExecuting`:</span><span class="sxs-lookup"><span data-stu-id="dc263-405">Throwing an exception in `IResultFilter.OnResultExecuting` will:</span></span>

* <span data-ttu-id="dc263-406">Impedir a execução do resultado da ação e dos próximos filtros.</span><span class="sxs-lookup"><span data-stu-id="dc263-406">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="dc263-407">Ser tratada como uma falha e não como um resultado com êxito.</span><span class="sxs-lookup"><span data-stu-id="dc263-407">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="dc263-408">Após a execução do método <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName>:</span><span class="sxs-lookup"><span data-stu-id="dc263-408">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs:</span></span>

* <span data-ttu-id="dc263-409">Provavelmente, a resposta foi enviada ao cliente e não pode ser alterada.</span><span class="sxs-lookup"><span data-stu-id="dc263-409">The response has likely been sent to the client and cannot be changed.</span></span>
* <span data-ttu-id="dc263-410">Se uma exceção foi lançada, o corpo da resposta não será enviado.</span><span class="sxs-lookup"><span data-stu-id="dc263-410">If an exception was thrown, the response body is not sent.</span></span>

<!-- Review preceding "If an exception was thrown: Original 
When the OnResultExecuted method runs, the response has likely been sent to the client and cannot be changed further (unless an exception was thrown).

SHould that be , 
If an exception was thrown **IN THE RESULT FILTER**, the response body is not sent.

 -->

<span data-ttu-id="dc263-411">`ResultExecutedContext.Canceled` será definido como `true` se a execução do resultado da ação tiver sofrido curto-circuito por outro filtro.</span><span class="sxs-lookup"><span data-stu-id="dc263-411">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="dc263-412">`ResultExecutedContext.Exception` será definido como um valor não nulo se o resultado da ação ou um filtro de resultado posterior tiver gerado uma exceção.</span><span class="sxs-lookup"><span data-stu-id="dc263-412">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="dc263-413">Definir `Exception` como nulo trata uma exceção com eficiência e impede que ela seja gerada novamente pelo ASP.NET Core posteriormente no pipeline.</span><span class="sxs-lookup"><span data-stu-id="dc263-413">Setting `Exception` to null effectively handles an exception and prevents the exception from being rethrown by ASP.NET Core later in the pipeline.</span></span> <span data-ttu-id="dc263-414">Não há nenhuma maneira confiável para gravar dados em uma resposta ao manipular uma exceção em um filtro de resultado.</span><span class="sxs-lookup"><span data-stu-id="dc263-414">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="dc263-415">Se os cabeçalhos tiverem sido liberados para o cliente quando o resultado de uma ação gerar uma exceção, não haverá mecanismo confiável para enviar um código de falha.</span><span class="sxs-lookup"><span data-stu-id="dc263-415">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="dc263-416">Para um <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, uma chamada para `await next` no <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executa qualquer filtro de resultado posterior e o resultado da ação.</span><span class="sxs-lookup"><span data-stu-id="dc263-416">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="dc263-417">Para causar um curto-circuito, defina [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) como `true` e não chame o `ResultExecutionDelegate`:</span><span class="sxs-lookup"><span data-stu-id="dc263-417">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="dc263-418">A estrutura fornece um `ResultFilterAttribute` abstrato que você pode colocar em uma subclasse.</span><span class="sxs-lookup"><span data-stu-id="dc263-418">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="dc263-419">A classe [AddHeaderAttribute](#add-header-attribute) mostrada anteriormente é um exemplo de atributo de filtro de resultado.</span><span class="sxs-lookup"><span data-stu-id="dc263-419">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="dc263-420">IAlwaysRunResultFilter e IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="dc263-420">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="dc263-421">As interfaces <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> e <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> declaram uma implementação <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> que é executada para todos os resultados da ação.</span><span class="sxs-lookup"><span data-stu-id="dc263-421">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="dc263-422">O filtro é aplicado a todos os resultados da ação, a menos que:</span><span class="sxs-lookup"><span data-stu-id="dc263-422">The filter is applied to all action results unless:</span></span>

* <span data-ttu-id="dc263-423">Uma <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAuthorizationFilter> aplica e causa curto-circuito à resposta.</span><span class="sxs-lookup"><span data-stu-id="dc263-423">An <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAuthorizationFilter> applies and short-circuits the response.</span></span>
* <span data-ttu-id="dc263-424">Um filtro de exceção trata uma exceção produzindo um resultado de ação.</span><span class="sxs-lookup"><span data-stu-id="dc263-424">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="dc263-425">Filtra o que não é `IExceptionFilter`, e `IAuthorizationFilter` não causa curto-circuito em `IAlwaysRunResultFilter` e `IAsyncAlwaysRunResultFilter`.</span><span class="sxs-lookup"><span data-stu-id="dc263-425">Filters other than `IExceptionFilter` and `IAuthorizationFilter` don't short-circuit `IAlwaysRunResultFilter` and `IAsyncAlwaysRunResultFilter`.</span></span>

<span data-ttu-id="dc263-426">Por exemplo, o filtro a seguir sempre é executado e define o resultado de uma ação (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) com um código de status *422 Entidade Não Processável* quando ocorre falha na negociação de conteúdo:</span><span class="sxs-lookup"><span data-stu-id="dc263-426">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="dc263-427">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="dc263-427">IFilterFactory</span></span>

<span data-ttu-id="dc263-428"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="dc263-428"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="dc263-429">Portanto, uma instância `IFilterFactory` pode ser usada como uma instância `IFilterMetadata` em qualquer parte do pipeline de filtro.</span><span class="sxs-lookup"><span data-stu-id="dc263-429">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="dc263-430">Quando o tempo de execução se prepara para invocar o filtro, tenta convertê-lo em um `IFilterFactory`.</span><span class="sxs-lookup"><span data-stu-id="dc263-430">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="dc263-431">Se essa conversão for bem-sucedida, o método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> será chamado para criar a instância `IFilterMetadata` invocada.</span><span class="sxs-lookup"><span data-stu-id="dc263-431">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="dc263-432">Isso fornece um design flexível, porque o pipeline de filtro preciso não precisa ser definido explicitamente quando o aplicativo é iniciado.</span><span class="sxs-lookup"><span data-stu-id="dc263-432">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="dc263-433">Implemente `IFilterFactory` usando implementações personalizadas de atributo como outra abordagem à criação de filtros:</span><span class="sxs-lookup"><span data-stu-id="dc263-433">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="dc263-434">O código anterior pode ser testado executando o [exemplo para download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span><span class="sxs-lookup"><span data-stu-id="dc263-434">The preceding code can be tested by running the [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span></span>

* <span data-ttu-id="dc263-435">Invocar as ferramentas de desenvolvedor F12.</span><span class="sxs-lookup"><span data-stu-id="dc263-435">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="dc263-436">Navegue até `https://localhost:5001/Sample/HeaderWithFactory`</span><span class="sxs-lookup"><span data-stu-id="dc263-436">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`</span></span>

<span data-ttu-id="dc263-437">As ferramentas de desenvolvedor F12 exibem os seguintes cabeçalhos de resposta adicionados pelo código de exemplo:</span><span class="sxs-lookup"><span data-stu-id="dc263-437">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="dc263-438">**author:** `Joe Smith`</span><span class="sxs-lookup"><span data-stu-id="dc263-438">**author:** `Joe Smith`</span></span>
* <span data-ttu-id="dc263-439">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="dc263-439">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="dc263-440">**internal:** `My header`</span><span class="sxs-lookup"><span data-stu-id="dc263-440">**internal:** `My header`</span></span>

<span data-ttu-id="dc263-441">O código anterior cria o cabeçalho de resposta **interno:** `My header`.</span><span class="sxs-lookup"><span data-stu-id="dc263-441">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="dc263-442">IFilterFactory implementado em um atributo</span><span class="sxs-lookup"><span data-stu-id="dc263-442">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="dc263-443">Filtros que implementam `IFilterFactory` são úteis para filtros que:</span><span class="sxs-lookup"><span data-stu-id="dc263-443">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="dc263-444">Não exigem a passagem de parâmetros.</span><span class="sxs-lookup"><span data-stu-id="dc263-444">Don't require passing parameters.</span></span>
* <span data-ttu-id="dc263-445">Tenha dependências de construtor que precisem ser atendidas pela DI.</span><span class="sxs-lookup"><span data-stu-id="dc263-445">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="dc263-446"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="dc263-446"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="dc263-447">`IFilterFactory` expõe o método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> para criar uma instância de <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="dc263-447">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="dc263-448">`CreateInstance` carrega o tipo especificado do contêiner de serviços (DI).</span><span class="sxs-lookup"><span data-stu-id="dc263-448">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="dc263-449">O código a seguir mostra três abordagens para aplicar o `[SampleActionFilter]`:</span><span class="sxs-lookup"><span data-stu-id="dc263-449">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="dc263-450">No código anterior, decorar o método com `[SampleActionFilter]` é a abordagem preferida para aplicar o `SampleActionFilter`.</span><span class="sxs-lookup"><span data-stu-id="dc263-450">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="dc263-451">Usando middleware no pipeline de filtros</span><span class="sxs-lookup"><span data-stu-id="dc263-451">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="dc263-452">Filtros de recursos funcionam como [middleware](xref:fundamentals/middleware/index), no sentido em que envolvem a execução de tudo o que vem depois no pipeline.</span><span class="sxs-lookup"><span data-stu-id="dc263-452">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="dc263-453">Mas os filtros diferem do middleware porque fazem parte do ASP.NET Core, o que significa que têm acesso ao contexto e a constructos do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dc263-453">But filters differ from middleware in that they're part of the ASP.NET Core runtime, which means that they have access to ASP.NET Core context and constructs.</span></span>

<span data-ttu-id="dc263-454">Para usar o middleware como um filtro, crie um tipo com um método `Configure` que especifica o middleware para injeção no pipeline de filtros.</span><span class="sxs-lookup"><span data-stu-id="dc263-454">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="dc263-455">O exemplo a seguir usa o middleware de localização para estabelecer a cultura atual para uma solicitação:</span><span class="sxs-lookup"><span data-stu-id="dc263-455">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,21)]

<span data-ttu-id="dc263-456">Use <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> para executar o middleware:</span><span class="sxs-lookup"><span data-stu-id="dc263-456">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="dc263-457">Filtros de middleware são executados no mesmo estágio do pipeline de filtros que filtros de recurso, antes do model binding e depois do restante do pipeline.</span><span class="sxs-lookup"><span data-stu-id="dc263-457">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="dc263-458">Próximas ações</span><span class="sxs-lookup"><span data-stu-id="dc263-458">Next actions</span></span>

* <span data-ttu-id="dc263-459">Confira [Métodos de filtro do Razor Pages](xref:razor-pages/filter)</span><span class="sxs-lookup"><span data-stu-id="dc263-459">See [Filter methods for Razor Pages](xref:razor-pages/filter)</span></span>
* <span data-ttu-id="dc263-460">Para fazer experiências com filtros, [baixe, teste e modifique o exemplo do GitHub](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span><span class="sxs-lookup"><span data-stu-id="dc263-460">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>
