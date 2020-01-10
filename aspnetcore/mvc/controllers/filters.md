---
title: Filtros no ASP.NET Core
author: Rick-Anderson
description: Saiba como os filtros funcionam e como usá-los no ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 1/1/2020
uid: mvc/controllers/filters
ms.openlocfilehash: 759c150e7f35f3f6a52947edc5ef41448dc227fe
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75828965"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="bf121-103">Filtros no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bf121-103">Filters in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="bf121-104">Por [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="bf121-104">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="bf121-105">Os *Filtros* no ASP.NET Core permitem a execução de código antes ou depois de determinados estágios do pipeline de processamento de solicitações.</span><span class="sxs-lookup"><span data-stu-id="bf121-105">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="bf121-106">O filtros internos lidam com tarefas como:</span><span class="sxs-lookup"><span data-stu-id="bf121-106">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="bf121-107">Autorização (impedir o acesso a recursos aos quais o usuário não está autorizado).</span><span class="sxs-lookup"><span data-stu-id="bf121-107">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="bf121-108">Cache de resposta (causar um curto-circuito do pipeline de solicitação para retornar uma resposta armazenada em cache).</span><span class="sxs-lookup"><span data-stu-id="bf121-108">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="bf121-109">É possível criar filtros personalizados para lidar com interesses paralelos.</span><span class="sxs-lookup"><span data-stu-id="bf121-109">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="bf121-110">Entre os exemplos de interesses paralelos estão o tratamento de erros, cache, configuração, autorização e registro em log.</span><span class="sxs-lookup"><span data-stu-id="bf121-110">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="bf121-111">Filtros evitam a duplicação do código.</span><span class="sxs-lookup"><span data-stu-id="bf121-111">Filters avoid duplicating code.</span></span> <span data-ttu-id="bf121-112">Por exemplo, um filtro de exceção de tratamento de erro poderia consolidar o tratamento de erro.</span><span class="sxs-lookup"><span data-stu-id="bf121-112">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="bf121-113">Este documento se aplica ao Razor Pages, a controladores de API e controladores com exibição.</span><span class="sxs-lookup"><span data-stu-id="bf121-113">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="bf121-114">[Exibir ou baixar exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="bf121-114">[View or download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="bf121-115">Como os filtros funcionam</span><span class="sxs-lookup"><span data-stu-id="bf121-115">How filters work</span></span>

<span data-ttu-id="bf121-116">Os filtros são executados dentro do *pipeline de invocação de ações do ASP.NET Core*, às vezes chamado de *pipeline de filtros*.</span><span class="sxs-lookup"><span data-stu-id="bf121-116">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="bf121-117">O pipeline de filtros é executado após o ASP.NET Core selecionar a ação a ser executada.</span><span class="sxs-lookup"><span data-stu-id="bf121-117">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![A solicitação é processada por meio de outro middleware, middleware de roteamento, seleção de ação e o pipeline de invocação de ação.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="bf121-120">Tipos de filtro</span><span class="sxs-lookup"><span data-stu-id="bf121-120">Filter types</span></span>

<span data-ttu-id="bf121-121">Cada tipo de filtro é executado em um estágio diferente no pipeline de filtros:</span><span class="sxs-lookup"><span data-stu-id="bf121-121">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="bf121-122">[Filtros de autorização](#authorization-filters) são executados primeiro e são usados para determinar se o usuário tem autorização para a solicitação.</span><span class="sxs-lookup"><span data-stu-id="bf121-122">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="bf121-123">Os filtros de autorização de circuito curto do pipeline se a solicitação não for autorizada.</span><span class="sxs-lookup"><span data-stu-id="bf121-123">Authorization filters short-circuit the pipeline if the request is not authorized.</span></span>

* <span data-ttu-id="bf121-124">[Filtros de recurso](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="bf121-124">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="bf121-125">Execute após a autorização.</span><span class="sxs-lookup"><span data-stu-id="bf121-125">Run after authorization.</span></span>  
  * <span data-ttu-id="bf121-126"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> executa o código antes do resto do pipeline de filtro.</span><span class="sxs-lookup"><span data-stu-id="bf121-126"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> runs code before the rest of the filter pipeline.</span></span> <span data-ttu-id="bf121-127">Por exemplo, `OnResourceExecuting` executa o código antes da Associação de modelo.</span><span class="sxs-lookup"><span data-stu-id="bf121-127">For example, `OnResourceExecuting` runs code before model binding.</span></span>
  * <span data-ttu-id="bf121-128"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> executa o código após a conclusão do restante do pipeline.</span><span class="sxs-lookup"><span data-stu-id="bf121-128"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> runs code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="bf121-129">[Filtros de ação](#action-filters):</span><span class="sxs-lookup"><span data-stu-id="bf121-129">[Action filters](#action-filters):</span></span>

  * <span data-ttu-id="bf121-130">Execute o código imediatamente antes e depois que um método de ação for chamado.</span><span class="sxs-lookup"><span data-stu-id="bf121-130">Run code immediately before and after an action method is called.</span></span>
  * <span data-ttu-id="bf121-131">Pode alterar os argumentos passados para uma ação.</span><span class="sxs-lookup"><span data-stu-id="bf121-131">Can change the arguments passed into an action.</span></span>
  * <span data-ttu-id="bf121-132">Pode alterar o resultado retornado da ação.</span><span class="sxs-lookup"><span data-stu-id="bf121-132">Can change the result returned from the action.</span></span>
  * <span data-ttu-id="bf121-133">**Não** têm suporte no Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="bf121-133">Are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="bf121-134">Os [filtros de exceção](#exception-filters) aplicam políticas globais a exceções sem tratamento que ocorrem antes da gravação do corpo da resposta.</span><span class="sxs-lookup"><span data-stu-id="bf121-134">[Exception filters](#exception-filters) apply global policies to unhandled exceptions that occur before the response body has been written to.</span></span>

* <span data-ttu-id="bf121-135">Os [filtros de resultado](#result-filters) executam o código imediatamente antes e após a execução dos resultados da ação.</span><span class="sxs-lookup"><span data-stu-id="bf121-135">[Result filters](#result-filters) run code immediately before and after the execution of action results.</span></span> <span data-ttu-id="bf121-136">Eles são executados somente quando o método de ação é executado com êxito.</span><span class="sxs-lookup"><span data-stu-id="bf121-136">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="bf121-137">Eles são úteis para a lógica que precisa envolver a execução da exibição ou do formatador.</span><span class="sxs-lookup"><span data-stu-id="bf121-137">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="bf121-138">O diagrama a seguir mostra como os tipos de filtro interagem no pipeline de filtros.</span><span class="sxs-lookup"><span data-stu-id="bf121-138">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![A solicitação é processada por meio de Filtros de autorização, Filtros de recurso, Model binding, Filtros de ação, Execução de ação e Conversão do resultado de ação, Filtros de exceção, Filtros de resultado e Execução de resultado.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="bf121-141">Implementação</span><span class="sxs-lookup"><span data-stu-id="bf121-141">Implementation</span></span>

<span data-ttu-id="bf121-142">Os filtros dão suporte a implementações síncronas e assíncronas por meio de diferentes definições de interface.</span><span class="sxs-lookup"><span data-stu-id="bf121-142">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="bf121-143">Os filtros síncronos executam o código antes e depois do estágio do pipeline.</span><span class="sxs-lookup"><span data-stu-id="bf121-143">Synchronous filters run code before and after their pipeline stage.</span></span> <span data-ttu-id="bf121-144">Por exemplo, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> é chamado antes que o método de ação seja chamado.</span><span class="sxs-lookup"><span data-stu-id="bf121-144">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> is called before the action method is called.</span></span> <span data-ttu-id="bf121-145"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> é chamado após o método de ação retornar.</span><span class="sxs-lookup"><span data-stu-id="bf121-145"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> is called after the action method returns.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="bf121-146">Os filtros assíncronos definem um método `On-Stage-ExecutionAsync`.</span><span class="sxs-lookup"><span data-stu-id="bf121-146">Asynchronous filters define an `On-Stage-ExecutionAsync` method.</span></span> <span data-ttu-id="bf121-147">Por exemplo <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span><span class="sxs-lookup"><span data-stu-id="bf121-147">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="bf121-148">No código anterior, o `SampleAsyncActionFilter` tem um <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) que executa o método de ação.</span><span class="sxs-lookup"><span data-stu-id="bf121-148">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) that executes the action method.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="bf121-149">Vários estágios do filtro</span><span class="sxs-lookup"><span data-stu-id="bf121-149">Multiple filter stages</span></span>

<span data-ttu-id="bf121-150">É possível implementar interfaces para vários estágios do filtro em uma única classe.</span><span class="sxs-lookup"><span data-stu-id="bf121-150">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="bf121-151">Por exemplo, a classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> implementa:</span><span class="sxs-lookup"><span data-stu-id="bf121-151">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements:</span></span>

* <span data-ttu-id="bf121-152">Síncrono: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> e <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span><span class="sxs-lookup"><span data-stu-id="bf121-152">Synchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> and  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span></span>
* <span data-ttu-id="bf121-153">Assíncrono: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> e <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="bf121-153">Asynchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

<span data-ttu-id="bf121-154">Implemente **ou** a versão assíncrona ou a versão síncrona de uma interface de filtro, **não** ambas.</span><span class="sxs-lookup"><span data-stu-id="bf121-154">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="bf121-155">Primeiro, o runtime verifica se o filtro implementa a interface assíncrona e, se for esse o caso, a chama.</span><span class="sxs-lookup"><span data-stu-id="bf121-155">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="bf121-156">Caso contrário, ela chama os métodos da interface síncrona.</span><span class="sxs-lookup"><span data-stu-id="bf121-156">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="bf121-157">Se as interfaces síncrona e assíncrona forem implementadas em uma classe, somente o método assíncrono será chamado.</span><span class="sxs-lookup"><span data-stu-id="bf121-157">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="bf121-158">Ao usar classes abstratas como <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, substitua apenas os métodos síncronos ou o método assíncrono para cada tipo de filtro.</span><span class="sxs-lookup"><span data-stu-id="bf121-158">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, override only the synchronous methods or the asynchronous method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="bf121-159">Atributos de filtro internos</span><span class="sxs-lookup"><span data-stu-id="bf121-159">Built-in filter attributes</span></span>

<span data-ttu-id="bf121-160">O ASP.NET Core inclui filtros internos baseados em atributos que podem ser organizados em subclasses e personalizados.</span><span class="sxs-lookup"><span data-stu-id="bf121-160">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="bf121-161">Por exemplo, o filtro de resultado a seguir adiciona um cabeçalho à resposta:</span><span class="sxs-lookup"><span data-stu-id="bf121-161">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="bf121-162">Os atributos permitem que os filtros aceitem argumentos, conforme mostrado no exemplo acima.</span><span class="sxs-lookup"><span data-stu-id="bf121-162">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="bf121-163">Aplique o `AddHeaderAttribute` a um controlador ou método de ação e especifique o nome e o valor do cabeçalho HTTP:</span><span class="sxs-lookup"><span data-stu-id="bf121-163">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<span data-ttu-id="bf121-164">Use uma ferramenta como as [ferramentas de desenvolvedor do navegador](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) para examinar os cabeçalhos.</span><span class="sxs-lookup"><span data-stu-id="bf121-164">Use a tool such as the [browser developer tools](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) to examine the headers.</span></span> <span data-ttu-id="bf121-165">Em **cabeçalhos de resposta**, `author: Rick Anderson` é exibido.</span><span class="sxs-lookup"><span data-stu-id="bf121-165">Under **Response Headers**, `author: Rick Anderson` is displayed.</span></span>

<span data-ttu-id="bf121-166">O código a seguir implementa um `ActionFilterAttribute` que:</span><span class="sxs-lookup"><span data-stu-id="bf121-166">The following code implements an `ActionFilterAttribute` that:</span></span>

* <span data-ttu-id="bf121-167">Lê o título e o nome do sistema de configuração.</span><span class="sxs-lookup"><span data-stu-id="bf121-167">Reads the title and name from the configuration system.</span></span> <span data-ttu-id="bf121-168">Ao contrário do exemplo anterior, o código a seguir não exige que os parâmetros de filtro sejam adicionados ao código.</span><span class="sxs-lookup"><span data-stu-id="bf121-168">Unlike the previous sample, the following code doesn't require filter parameters to be added to the code.</span></span>
* <span data-ttu-id="bf121-169">Adiciona o título e o nome ao cabeçalho de resposta.</span><span class="sxs-lookup"><span data-stu-id="bf121-169">Adds the title and name to the response header.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

<span data-ttu-id="bf121-170">As opções de configuração são fornecidas no [sistema de configuração](xref:fundamentals/configuration/index) usando o [padrão de opções](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="bf121-170">The configuration options are provided from the [configuration system](xref:fundamentals/configuration/index) using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="bf121-171">Por exemplo, no arquivo *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="bf121-171">For example, from the *appsettings.json* file:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/appsettings.json)]

<span data-ttu-id="bf121-172">No `StartUp.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="bf121-172">In the `StartUp.ConfigureServices`:</span></span>

* <span data-ttu-id="bf121-173">A classe `PositionOptions` é adicionada ao contêiner de serviço com a área de configuração `"Position"`.</span><span class="sxs-lookup"><span data-stu-id="bf121-173">The `PositionOptions` class is added to the service container with the `"Position"` configuration area.</span></span>
* <span data-ttu-id="bf121-174">O `MyActionFilterAttribute` é adicionado ao contêiner de serviço.</span><span class="sxs-lookup"><span data-stu-id="bf121-174">The `MyActionFilterAttribute` is added to the service container.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

<span data-ttu-id="bf121-175">O código a seguir mostra a classe `PositionOptions`:</span><span class="sxs-lookup"><span data-stu-id="bf121-175">The following code shows the `PositionOptions` class:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

<span data-ttu-id="bf121-176">O código a seguir aplica o `MyActionFilterAttribute` ao método `Index2`:</span><span class="sxs-lookup"><span data-stu-id="bf121-176">The following code applies the `MyActionFilterAttribute` to the `Index2` method:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

<span data-ttu-id="bf121-177">Em **cabeçalhos de resposta**, `author: Rick Anderson`e `Editor: Joe Smith` são exibidos quando o ponto de extremidade `Sample/Index2` é chamado.</span><span class="sxs-lookup"><span data-stu-id="bf121-177">Under **Response Headers**, `author: Rick Anderson`, and `Editor: Joe Smith` is displayed when the `Sample/Index2` endpoint is called.</span></span>

<span data-ttu-id="bf121-178">O código a seguir aplica o `MyActionFilterAttribute` e o `AddHeaderAttribute` à página Razor:</span><span class="sxs-lookup"><span data-stu-id="bf121-178">The following code applies the `MyActionFilterAttribute` and the `AddHeaderAttribute` to the Razor Page:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="bf121-179">Os filtros não podem ser aplicados aos métodos do manipulador de páginas do Razor.</span><span class="sxs-lookup"><span data-stu-id="bf121-179">Filters cannot be applied to Razor Page handler methods.</span></span> <span data-ttu-id="bf121-180">Eles podem ser aplicados ao modelo de página do Razor ou globalmente.</span><span class="sxs-lookup"><span data-stu-id="bf121-180">They can be applied either to the Razor Page model or globally.</span></span>

<span data-ttu-id="bf121-181">Várias interfaces de filtro têm atributos correspondentes que podem ser usados como classes base para implementações personalizadas.</span><span class="sxs-lookup"><span data-stu-id="bf121-181">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="bf121-182">Atributos de filtro:</span><span class="sxs-lookup"><span data-stu-id="bf121-182">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="bf121-183">Escopos e ordem de execução dos filtros</span><span class="sxs-lookup"><span data-stu-id="bf121-183">Filter scopes and order of execution</span></span>

<span data-ttu-id="bf121-184">Um filtro pode ser adicionado ao pipeline com um de três *escopos*:</span><span class="sxs-lookup"><span data-stu-id="bf121-184">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="bf121-185">Usando um atributo em uma ação do controlador.</span><span class="sxs-lookup"><span data-stu-id="bf121-185">Using an attribute on a controller action.</span></span> <span data-ttu-id="bf121-186">Atributos de filtro não podem ser aplicados a métodos de manipuladores de Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="bf121-186">Filter attributes cannot be applied to Razor Pages handler methods.</span></span>
* <span data-ttu-id="bf121-187">Usando um atributo em um controlador ou em uma página Razor.</span><span class="sxs-lookup"><span data-stu-id="bf121-187">Using an attribute on a controller or Razor Page.</span></span>
* <span data-ttu-id="bf121-188">Globalmente para todos os controladores, ações e Razor Pages, conforme mostrado no código a seguir:</span><span class="sxs-lookup"><span data-stu-id="bf121-188">Globally for all controllers, actions, and Razor Pages as shown in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a><span data-ttu-id="bf121-189">Ordem padrão de execução</span><span class="sxs-lookup"><span data-stu-id="bf121-189">Default order of execution</span></span>

<span data-ttu-id="bf121-190">Quando há vários filtros para um determinado estágio do pipeline, o escopo determina a ordem padrão de execução dos filtros.</span><span class="sxs-lookup"><span data-stu-id="bf121-190">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="bf121-191">Filtros globais circundam filtros de classe, que, por sua vez, circundam filtros de método.</span><span class="sxs-lookup"><span data-stu-id="bf121-191">Global filters surround class filters, which in turn surround method filters.</span></span>

<span data-ttu-id="bf121-192">Como resultado do aninhamento de filtro, o código *posterior* dos filtros é executado na ordem inversa do código *anterior*.</span><span class="sxs-lookup"><span data-stu-id="bf121-192">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="bf121-193">A sequência de filtro:</span><span class="sxs-lookup"><span data-stu-id="bf121-193">The filter sequence:</span></span>

* <span data-ttu-id="bf121-194">O código *anterior* dos filtros globais.</span><span class="sxs-lookup"><span data-stu-id="bf121-194">The *before* code of global filters.</span></span>
  * <span data-ttu-id="bf121-195">O código *anterior* dos filtros de página do controlador e do Razor.</span><span class="sxs-lookup"><span data-stu-id="bf121-195">The *before* code of controller and Razor Page filters.</span></span>
    * <span data-ttu-id="bf121-196">O código *anterior* dos filtros de método de ação.</span><span class="sxs-lookup"><span data-stu-id="bf121-196">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="bf121-197">O código *posterior* dos filtros de método de ação.</span><span class="sxs-lookup"><span data-stu-id="bf121-197">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="bf121-198">O código *After* dos filtros de página do controlador e do Razor.</span><span class="sxs-lookup"><span data-stu-id="bf121-198">The *after* code of controller and Razor Page filters.</span></span>
* <span data-ttu-id="bf121-199">O código *posterior* dos filtros globais.</span><span class="sxs-lookup"><span data-stu-id="bf121-199">The *after* code of global filters.</span></span>
  
<span data-ttu-id="bf121-200">O exemplo a seguir ilustra a ordem na qual os métodos de filtro são chamados para filtros de ação síncrona.</span><span class="sxs-lookup"><span data-stu-id="bf121-200">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="bf121-201">Sequence</span><span class="sxs-lookup"><span data-stu-id="bf121-201">Sequence</span></span> | <span data-ttu-id="bf121-202">Escopo do filtro</span><span class="sxs-lookup"><span data-stu-id="bf121-202">Filter scope</span></span> | <span data-ttu-id="bf121-203">Método Filter</span><span class="sxs-lookup"><span data-stu-id="bf121-203">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="bf121-204">1</span><span class="sxs-lookup"><span data-stu-id="bf121-204">1</span></span> | <span data-ttu-id="bf121-205">Global</span><span class="sxs-lookup"><span data-stu-id="bf121-205">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="bf121-206">2</span><span class="sxs-lookup"><span data-stu-id="bf121-206">2</span></span> | <span data-ttu-id="bf121-207">Controlador ou página Razor</span><span class="sxs-lookup"><span data-stu-id="bf121-207">Controller or Razor Page</span></span>| `OnActionExecuting` |
| <span data-ttu-id="bf121-208">3</span><span class="sxs-lookup"><span data-stu-id="bf121-208">3</span></span> | <span data-ttu-id="bf121-209">Método</span><span class="sxs-lookup"><span data-stu-id="bf121-209">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="bf121-210">4</span><span class="sxs-lookup"><span data-stu-id="bf121-210">4</span></span> | <span data-ttu-id="bf121-211">Método</span><span class="sxs-lookup"><span data-stu-id="bf121-211">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="bf121-212">5</span><span class="sxs-lookup"><span data-stu-id="bf121-212">5</span></span> | <span data-ttu-id="bf121-213">Controlador ou página Razor</span><span class="sxs-lookup"><span data-stu-id="bf121-213">Controller or Razor Page</span></span> | `OnActionExecuted` |
| <span data-ttu-id="bf121-214">6</span><span class="sxs-lookup"><span data-stu-id="bf121-214">6</span></span> | <span data-ttu-id="bf121-215">Global</span><span class="sxs-lookup"><span data-stu-id="bf121-215">Global</span></span> | `OnActionExecuted` |

### <a name="controller-level-filters"></a><span data-ttu-id="bf121-216">Filtros de nível de controlador</span><span class="sxs-lookup"><span data-stu-id="bf121-216">Controller level filters</span></span>

<span data-ttu-id="bf121-217">Cada controlador que herda da classe base <xref:Microsoft.AspNetCore.Mvc.Controller> inclui os métodos [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) e [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="bf121-217">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="bf121-218">Estes métodos:</span><span class="sxs-lookup"><span data-stu-id="bf121-218">These methods:</span></span>

* <span data-ttu-id="bf121-219">Encapsulam os filtros executados para uma determinada ação.</span><span class="sxs-lookup"><span data-stu-id="bf121-219">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="bf121-220">`OnActionExecuting` é chamado antes de qualquer um dos filtros da ação.</span><span class="sxs-lookup"><span data-stu-id="bf121-220">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="bf121-221">`OnActionExecuted` é chamado após todos os filtros da ação.</span><span class="sxs-lookup"><span data-stu-id="bf121-221">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="bf121-222">`OnActionExecutionAsync` é chamado antes de qualquer um dos filtros da ação.</span><span class="sxs-lookup"><span data-stu-id="bf121-222">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="bf121-223">O código no filtro após `next` é executado após o método de ação.</span><span class="sxs-lookup"><span data-stu-id="bf121-223">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="bf121-224">Por exemplo, no download de exemplo, `MySampleActionFilter` é aplicado globalmente na inicialização.</span><span class="sxs-lookup"><span data-stu-id="bf121-224">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="bf121-225">O `TestController`:</span><span class="sxs-lookup"><span data-stu-id="bf121-225">The `TestController`:</span></span>

* <span data-ttu-id="bf121-226">Aplica o `SampleActionFilterAttribute` (`[SampleActionFilter]`) à ação `FilterTest2`.</span><span class="sxs-lookup"><span data-stu-id="bf121-226">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="bf121-227">Substitui `OnActionExecuting` e `OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="bf121-227">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

<span data-ttu-id="bf121-228">Navegar até `https://localhost:5001/Test2/FilterTest2` executa o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="bf121-228">Navigating to `https://localhost:5001/Test2/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="bf121-229">Filtros de nível de controlador definem a propriedade [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) como `int.MinValue`.</span><span class="sxs-lookup"><span data-stu-id="bf121-229">Controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue`.</span></span> <span data-ttu-id="bf121-230">Os filtros de nível de controlador **não** podem ser definidos para execução após os filtros aplicados aos métodos.</span><span class="sxs-lookup"><span data-stu-id="bf121-230">Controller level filters can **not** be set to run after filters applied to methods.</span></span> <span data-ttu-id="bf121-231">A ordem é explicada na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="bf121-231">Order is explained in the next section.</span></span>

<span data-ttu-id="bf121-232">Para Razor Pages, confira [Implementar filtros do Razor Page substituindo os métodos de filtro](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="bf121-232">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="bf121-233">Substituindo a ordem padrão</span><span class="sxs-lookup"><span data-stu-id="bf121-233">Overriding the default order</span></span>

<span data-ttu-id="bf121-234">É possível substituir a sequência padrão de execução implementando <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span><span class="sxs-lookup"><span data-stu-id="bf121-234">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="bf121-235">`IOrderedFilter` expõe a propriedade <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> que tem precedência sobre o escopo para determinar a ordem da execução.</span><span class="sxs-lookup"><span data-stu-id="bf121-235">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="bf121-236">Um filtro com um valor de `Order` menor:</span><span class="sxs-lookup"><span data-stu-id="bf121-236">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="bf121-237">Executa o código *anterior* antes de um filtro com um valor mais alto de `Order`.</span><span class="sxs-lookup"><span data-stu-id="bf121-237">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="bf121-238">Executa o código *posterior* após um filtro com um valor mais alto de `Order`.</span><span class="sxs-lookup"><span data-stu-id="bf121-238">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="bf121-239">A propriedade `Order` é definida com um parâmetro de construtor:</span><span class="sxs-lookup"><span data-stu-id="bf121-239">The `Order` property is set with a constructor parameter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

<span data-ttu-id="bf121-240">Considere os dois filtros de ação no controlador a seguir:</span><span class="sxs-lookup"><span data-stu-id="bf121-240">Consider the two action filters in the following controller:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="bf121-241">Um filtro global é adicionado no `StartUp.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="bf121-241">A global filter is added in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

<span data-ttu-id="bf121-242">Os 3 filtros são executados na seguinte ordem:</span><span class="sxs-lookup"><span data-stu-id="bf121-242">The 3 filters run in the following order:</span></span>

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MySampleActionFilter.OnActionExecuted`
  * `MyAction2FilterAttribute.OnResultExecuting`
* `Test2Controller.OnActionExecuted`

<span data-ttu-id="bf121-243">A propriedade `Order` substitui o escopo ao determinar a ordem na qual os filtros serão executados.</span><span class="sxs-lookup"><span data-stu-id="bf121-243">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="bf121-244">Os filtros são classificados primeiro pela ordem e o escopo é usado para desempatar.</span><span class="sxs-lookup"><span data-stu-id="bf121-244">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="bf121-245">Todos os filtros internos implementam `IOrderedFilter` e definem o valor de `Order` padrão como 0.</span><span class="sxs-lookup"><span data-stu-id="bf121-245">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="bf121-246">Como mencionado anteriormente, os filtros no nível do controlador definem a propriedade [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) como `int.MinValue` para filtros internos, o escopo determina a ordem, a menos que `Order` esteja definido como um valor diferente de zero.</span><span class="sxs-lookup"><span data-stu-id="bf121-246">As mentioned previously, controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue` For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

<span data-ttu-id="bf121-247">No código anterior, `MySampleActionFilter` tem escopo global para que ele seja executado antes `MyAction2FilterAttribute`, que tem o escopo do controlador.</span><span class="sxs-lookup"><span data-stu-id="bf121-247">In the preceding code, `MySampleActionFilter` has global scope so it runs before `MyAction2FilterAttribute`, which has controller scope.</span></span> <span data-ttu-id="bf121-248">Para fazer `MyAction2FilterAttribute` executar primeiro, defina o pedido como `int.MinValue`:</span><span class="sxs-lookup"><span data-stu-id="bf121-248">To make `MyAction2FilterAttribute` run first, set the order to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

<span data-ttu-id="bf121-249">Para fazer com que o filtro global `MySampleActionFilter` executado primeiro, defina `Order` como `int.MinValue`:</span><span class="sxs-lookup"><span data-stu-id="bf121-249">To make the global filter `MySampleActionFilter` run first, set `Order` to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="bf121-250">Cancelamento e curto-circuito</span><span class="sxs-lookup"><span data-stu-id="bf121-250">Cancellation and short-circuiting</span></span>

<span data-ttu-id="bf121-251">O pipeline de filtro pode sofrer um curto-circuito por meio da configuração da propriedade <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> no parâmetro <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> fornecido ao método do filtro.</span><span class="sxs-lookup"><span data-stu-id="bf121-251">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="bf121-252">Por exemplo, o filtro de Recurso a seguir impede que o resto do pipeline seja executado:</span><span class="sxs-lookup"><span data-stu-id="bf121-252">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="bf121-253">No código a seguir, os filtros `ShortCircuitingResourceFilter` e `AddHeader` têm como destino o método de ação `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="bf121-253">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="bf121-254">O `ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="bf121-254">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="bf121-255">É executado primeiro, porque ele é um filtro de recurso e `AddHeader` é um filtro de ação.</span><span class="sxs-lookup"><span data-stu-id="bf121-255">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="bf121-256">Causa um curto-circuito no restante do pipeline.</span><span class="sxs-lookup"><span data-stu-id="bf121-256">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="bf121-257">Portanto, o filtro `AddHeader` nunca é executado para a ação `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="bf121-257">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="bf121-258">Esse comportamento seria o mesmo se os dois filtros fossem aplicados no nível do método de ação, desde que `ShortCircuitingResourceFilter` fosse executado primeiro.</span><span class="sxs-lookup"><span data-stu-id="bf121-258">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="bf121-259">O `ShortCircuitingResourceFilter` é executado primeiro, devido ao seu tipo de filtro ou pelo uso explícito da propriedade `Order`.</span><span class="sxs-lookup"><span data-stu-id="bf121-259">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

## <a name="dependency-injection"></a><span data-ttu-id="bf121-260">Injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="bf121-260">Dependency injection</span></span>

<span data-ttu-id="bf121-261">Filtros podem ser adicionados por tipo ou por instância.</span><span class="sxs-lookup"><span data-stu-id="bf121-261">Filters can be added by type or by instance.</span></span> <span data-ttu-id="bf121-262">Se você adicionar uma instância, ela será usada para cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="bf121-262">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="bf121-263">Se um tipo for adicionado, ele será ativado pelo tipo.</span><span class="sxs-lookup"><span data-stu-id="bf121-263">If a type is added, it's type-activated.</span></span> <span data-ttu-id="bf121-264">Um filtro ativado por tipo significa:</span><span class="sxs-lookup"><span data-stu-id="bf121-264">A type-activated filter means:</span></span>

* <span data-ttu-id="bf121-265">Uma instância é criada para cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="bf121-265">An instance is created for each request.</span></span>
* <span data-ttu-id="bf121-266">Qualquer dependência de construtor é preenchida pela [injeção de dependência](xref:fundamentals/dependency-injection) (DI).</span><span class="sxs-lookup"><span data-stu-id="bf121-266">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="bf121-267">Filtros que são implementados como atributos e adicionados diretamente a classes de controlador ou métodos de ação não podem ter dependências de construtor fornecidas pela DI ([injeção de dependência](xref:fundamentals/dependency-injection)).</span><span class="sxs-lookup"><span data-stu-id="bf121-267">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="bf121-268">Dependências de construtor não podem ser fornecidas pela DI porque:</span><span class="sxs-lookup"><span data-stu-id="bf121-268">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="bf121-269">Os atributos precisam ter os parâmetros de construtor fornecidos quando são aplicados.</span><span class="sxs-lookup"><span data-stu-id="bf121-269">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="bf121-270">Essa é uma limitação do funcionamento dos atributos.</span><span class="sxs-lookup"><span data-stu-id="bf121-270">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="bf121-271">Os filtros a seguir são compatíveis com dependências de construtor fornecidas pela injeção de dependência:</span><span class="sxs-lookup"><span data-stu-id="bf121-271">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="bf121-272"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementado no atributo.</span><span class="sxs-lookup"><span data-stu-id="bf121-272"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="bf121-273">Os filtros anteriores podem ser aplicados a um método de ação ou controlador:</span><span class="sxs-lookup"><span data-stu-id="bf121-273">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="bf121-274">Os agentes estão disponíveis na DI.</span><span class="sxs-lookup"><span data-stu-id="bf121-274">Loggers are available from DI.</span></span> <span data-ttu-id="bf121-275">No entanto, evite criar e usar filtros apenas para fins de registro em log.</span><span class="sxs-lookup"><span data-stu-id="bf121-275">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="bf121-276">O [registro em log da estrutura interna](xref:fundamentals/logging/index) normalmente fornece o que é necessário para o registro em log.</span><span class="sxs-lookup"><span data-stu-id="bf121-276">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="bf121-277">Registro em log adicionado aos filtros:</span><span class="sxs-lookup"><span data-stu-id="bf121-277">Logging added to filters:</span></span>

* <span data-ttu-id="bf121-278">Deve se concentrar em questões de domínio de negócios ou comportamento específico ao filtro.</span><span class="sxs-lookup"><span data-stu-id="bf121-278">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="bf121-279">**Não** deve registrar ações ou outros eventos da estrutura.</span><span class="sxs-lookup"><span data-stu-id="bf121-279">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="bf121-280">Os filtros internos registram ações de log e eventos de estrutura.</span><span class="sxs-lookup"><span data-stu-id="bf121-280">The built-in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="bf121-281">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="bf121-281">ServiceFilterAttribute</span></span>

<span data-ttu-id="bf121-282">Os tipos de implementação do filtro de serviço são registrados em `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="bf121-282">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="bf121-283">Um <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> recupera uma instância do filtro da DI.</span><span class="sxs-lookup"><span data-stu-id="bf121-283">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="bf121-284">O código a seguir mostra `AddHeaderResultServiceFilter`:</span><span class="sxs-lookup"><span data-stu-id="bf121-284">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="bf121-285">No código a seguir, `AddHeaderResultServiceFilter` é adicionado ao contêiner de DI:</span><span class="sxs-lookup"><span data-stu-id="bf121-285">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

<span data-ttu-id="bf121-286">No código a seguir, o atributo `ServiceFilter` recupera uma instância do filtro `AddHeaderResultServiceFilter` da DI:</span><span class="sxs-lookup"><span data-stu-id="bf121-286">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="bf121-287">Ao usar `ServiceFilterAttribute`, configurar [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="bf121-287">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="bf121-288">Fornece uma dica de que a instância de filtro *pode* ser reutilizada fora do escopo de solicitação em que foi criada.</span><span class="sxs-lookup"><span data-stu-id="bf121-288">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="bf121-289">O runtime do ASP.NET Core não garante:</span><span class="sxs-lookup"><span data-stu-id="bf121-289">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="bf121-290">Que uma única instância do filtro será criada.</span><span class="sxs-lookup"><span data-stu-id="bf121-290">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="bf121-291">Que o filtro não será solicitado novamente no contêiner de DI em algum momento posterior.</span><span class="sxs-lookup"><span data-stu-id="bf121-291">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="bf121-292">Não deve ser usado com um filtro que dependa dos serviços com um tempo de vida diferente de singleton.</span><span class="sxs-lookup"><span data-stu-id="bf121-292">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="bf121-293"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="bf121-293"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="bf121-294">`IFilterFactory` expõe o método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> para criar uma instância de <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="bf121-294">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="bf121-295">`CreateInstance` carrega o tipo especificado na DI.</span><span class="sxs-lookup"><span data-stu-id="bf121-295">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="bf121-296">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="bf121-296">TypeFilterAttribute</span></span>

<span data-ttu-id="bf121-297">O <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> é semelhante ao <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, mas seu tipo não é resolvido diretamente por meio do contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="bf121-297"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="bf121-298">Ele cria uma instância do tipo usando <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="bf121-298">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="bf121-299">Como os tipos `TypeFilterAttribute` não são resolvidos diretamente do contêiner de DI:</span><span class="sxs-lookup"><span data-stu-id="bf121-299">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="bf121-300">Os tipos referenciados usando o `TypeFilterAttribute` não precisam ser registrados no contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="bf121-300">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="bf121-301">Eles têm suas dependências atendidas pelo contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="bf121-301">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="bf121-302">Opcionalmente, o `TypeFilterAttribute` pode aceitar argumentos de construtor para o tipo.</span><span class="sxs-lookup"><span data-stu-id="bf121-302">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="bf121-303">Ao usar `TypeFilterAttribute`, configurar [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="bf121-303">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="bf121-304">Fornece uma dica de que a instância de filtro *pode* ser reutilizada fora do escopo de solicitação em que foi criada.</span><span class="sxs-lookup"><span data-stu-id="bf121-304">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="bf121-305">O runtime do ASP.NET Core não fornece garantias de que uma única instância do filtro será criada.</span><span class="sxs-lookup"><span data-stu-id="bf121-305">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="bf121-306">Não deve ser usado com um filtro que dependa dos serviços com um tempo de vida diferente de singleton.</span><span class="sxs-lookup"><span data-stu-id="bf121-306">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="bf121-307">O exemplo a seguir mostra como passar argumentos para um tipo usando `TypeFilterAttribute`:</span><span class="sxs-lookup"><span data-stu-id="bf121-307">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="bf121-308">Filtros de autorização</span><span class="sxs-lookup"><span data-stu-id="bf121-308">Authorization filters</span></span>

<span data-ttu-id="bf121-309">Filtros de autorização:</span><span class="sxs-lookup"><span data-stu-id="bf121-309">Authorization filters:</span></span>

* <span data-ttu-id="bf121-310">São os primeiros filtros executados no pipeline de filtro.</span><span class="sxs-lookup"><span data-stu-id="bf121-310">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="bf121-311">Controlam o acesso aos métodos de ação.</span><span class="sxs-lookup"><span data-stu-id="bf121-311">Control access to action methods.</span></span>
* <span data-ttu-id="bf121-312">Têm um método anterior, mas não têm um método posterior.</span><span class="sxs-lookup"><span data-stu-id="bf121-312">Have a before method, but no after method.</span></span>

<span data-ttu-id="bf121-313">Filtros de autorização personalizados exigem uma estrutura de autorização personalizada.</span><span class="sxs-lookup"><span data-stu-id="bf121-313">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="bf121-314">Prefira configurar as políticas de autorização ou escrever uma política de autorização personalizada em vez de escrever um filtro personalizado.</span><span class="sxs-lookup"><span data-stu-id="bf121-314">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="bf121-315">O filtro de autorização interno:</span><span class="sxs-lookup"><span data-stu-id="bf121-315">The built-in authorization filter:</span></span>

* <span data-ttu-id="bf121-316">Chama o sistema de autorização.</span><span class="sxs-lookup"><span data-stu-id="bf121-316">Calls the authorization system.</span></span>
* <span data-ttu-id="bf121-317">Não autoriza solicitações.</span><span class="sxs-lookup"><span data-stu-id="bf121-317">Does not authorize requests.</span></span>

<span data-ttu-id="bf121-318">**Não** gera exceções dentro de filtros de autorização:</span><span class="sxs-lookup"><span data-stu-id="bf121-318">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="bf121-319">A exceção não será tratada.</span><span class="sxs-lookup"><span data-stu-id="bf121-319">The exception will not be handled.</span></span>
* <span data-ttu-id="bf121-320">Os filtros de exceção não tratarão a exceção.</span><span class="sxs-lookup"><span data-stu-id="bf121-320">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="bf121-321">Considere a possibilidade de emitir um desafio quando ocorrer uma exceção em um filtro de autorização.</span><span class="sxs-lookup"><span data-stu-id="bf121-321">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="bf121-322">Saiba mais sobre [Autorização](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="bf121-322">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="bf121-323">Filtros de recurso</span><span class="sxs-lookup"><span data-stu-id="bf121-323">Resource filters</span></span>

<span data-ttu-id="bf121-324">Filtros de recurso:</span><span class="sxs-lookup"><span data-stu-id="bf121-324">Resource filters:</span></span>

* <span data-ttu-id="bf121-325">Implementam a interface <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.</span><span class="sxs-lookup"><span data-stu-id="bf121-325">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="bf121-326">A execução encapsula grande parte do pipeline de filtro.</span><span class="sxs-lookup"><span data-stu-id="bf121-326">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="bf121-327">Somente os [Filtros de autorização](#authorization-filters) são executados antes dos filtros de recurso.</span><span class="sxs-lookup"><span data-stu-id="bf121-327">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="bf121-328">Os filtros de recursos são úteis para causar um curto-circuito na maior parte do pipeline.</span><span class="sxs-lookup"><span data-stu-id="bf121-328">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="bf121-329">Por exemplo, um filtro de cache pode evitar o restante do pipeline em uma ocorrência no cache.</span><span class="sxs-lookup"><span data-stu-id="bf121-329">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="bf121-330">Exemplos de filtros de recurso:</span><span class="sxs-lookup"><span data-stu-id="bf121-330">Resource filter examples:</span></span>

* <span data-ttu-id="bf121-331">[O filtro de recurso em curto-circuito](#short-circuiting-resource-filter) mostrado anteriormente.</span><span class="sxs-lookup"><span data-stu-id="bf121-331">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="bf121-332">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="bf121-332">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="bf121-333">Impede o model binding de acessar os dados do formulário.</span><span class="sxs-lookup"><span data-stu-id="bf121-333">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="bf121-334">Usado para uploads de arquivos grandes para impedir que os dados de formulário sejam lidos na memória.</span><span class="sxs-lookup"><span data-stu-id="bf121-334">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="bf121-335">Filtros de ação</span><span class="sxs-lookup"><span data-stu-id="bf121-335">Action filters</span></span>

<span data-ttu-id="bf121-336">Os filtros de ação **não** se aplicam ao Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="bf121-336">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="bf121-337">O Razor Pages é compatível com <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> e <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter>.</span><span class="sxs-lookup"><span data-stu-id="bf121-337">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="bf121-338">Para obter mais informações, confira [Métodos de filtro para Páginas Razor](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="bf121-338">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="bf121-339">Filtros de ação:</span><span class="sxs-lookup"><span data-stu-id="bf121-339">Action filters:</span></span>

* <span data-ttu-id="bf121-340">Implementam a interface <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.</span><span class="sxs-lookup"><span data-stu-id="bf121-340">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="bf121-341">A execução deles envolve a execução de métodos de ação.</span><span class="sxs-lookup"><span data-stu-id="bf121-341">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="bf121-342">O código a seguir mostra um exemplo de filtro de ação:</span><span class="sxs-lookup"><span data-stu-id="bf121-342">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="bf121-343">A classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> fornece as seguintes propriedades:</span><span class="sxs-lookup"><span data-stu-id="bf121-343">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="bf121-344"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments>-permite ler as entradas para um método de ação.</span><span class="sxs-lookup"><span data-stu-id="bf121-344"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables reading the inputs to an action method.</span></span>
* <span data-ttu-id="bf121-345"><xref:Microsoft.AspNetCore.Mvc.Controller> – permite a manipulação da instância do controlador.</span><span class="sxs-lookup"><span data-stu-id="bf121-345"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="bf121-346"><xref:System.Web.Mvc.ActionExecutingContext.Result> – configuração de `Result` execução de curtos-circuitos do método de ação e dos filtros de ação posteriores.</span><span class="sxs-lookup"><span data-stu-id="bf121-346"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="bf121-347">Gerar uma exceção em um método de ação:</span><span class="sxs-lookup"><span data-stu-id="bf121-347">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="bf121-348">Impede a execução de filtros subsequentes.</span><span class="sxs-lookup"><span data-stu-id="bf121-348">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="bf121-349">Ao contrário da configuração `Result`, é tratada como uma falha e não como um resultado bem-sucedido.</span><span class="sxs-lookup"><span data-stu-id="bf121-349">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="bf121-350">A classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> fornece `Controller` e `Result`, além das seguintes propriedades:</span><span class="sxs-lookup"><span data-stu-id="bf121-350">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="bf121-351"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> – verdadeiro se a execução da ação tiver sofrido curto-circuito por outro filtro.</span><span class="sxs-lookup"><span data-stu-id="bf121-351"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="bf121-352"><xref:System.Web.Mvc.ActionExecutedContext.Exception> – não será nulo se a ação ou um filtro de ação executado antes tiver apresentado uma exceção.</span><span class="sxs-lookup"><span data-stu-id="bf121-352"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="bf121-353">Definir essa propriedade como nulo:</span><span class="sxs-lookup"><span data-stu-id="bf121-353">Setting this property to null:</span></span>

  * <span data-ttu-id="bf121-354">Trata efetivamente a exceção.</span><span class="sxs-lookup"><span data-stu-id="bf121-354">Effectively handles the exception.</span></span>
  * <span data-ttu-id="bf121-355">`Result` é executado como se tivesse retornado do método de ação.</span><span class="sxs-lookup"><span data-stu-id="bf121-355">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="bf121-356">Para um `IAsyncActionFilter`, uma chamada para o <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span><span class="sxs-lookup"><span data-stu-id="bf121-356">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="bf121-357">Executa todos os próximos filtros de ação e o método de ação.</span><span class="sxs-lookup"><span data-stu-id="bf121-357">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="bf121-358">Retorna `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="bf121-358">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="bf121-359">Para fazer um curto-circuito, atribua <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> a uma instância de resultado e não chame o `next` (`ActionExecutionDelegate`).</span><span class="sxs-lookup"><span data-stu-id="bf121-359">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="bf121-360">A estrutura fornece um <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> abstrato que você pode colocar em uma subclasse.</span><span class="sxs-lookup"><span data-stu-id="bf121-360">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="bf121-361">O filtro de ação `OnActionExecuting` pode ser usado para:</span><span class="sxs-lookup"><span data-stu-id="bf121-361">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="bf121-362">Validar o estado do modelo.</span><span class="sxs-lookup"><span data-stu-id="bf121-362">Validate model state.</span></span>
* <span data-ttu-id="bf121-363">Retornar um erro se o estado for inválido.</span><span class="sxs-lookup"><span data-stu-id="bf121-363">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="bf121-364">O método `OnActionExecuted` é executado após o método de ação:</span><span class="sxs-lookup"><span data-stu-id="bf121-364">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="bf121-365">E pode ver e manipular os resultados da ação por meio da propriedade <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result>.</span><span class="sxs-lookup"><span data-stu-id="bf121-365">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="bf121-366"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> é definido como verdadeiro se a execução da ação tiver sofrido curto-circuito por outro filtro.</span><span class="sxs-lookup"><span data-stu-id="bf121-366"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="bf121-367"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> é definido como um valor não nulo se a ação ou um filtro de ação posterior tiver apresentado uma exceção.</span><span class="sxs-lookup"><span data-stu-id="bf121-367"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="bf121-368">Definindo `Exception` como nulo:</span><span class="sxs-lookup"><span data-stu-id="bf121-368">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="bf121-369">Trata efetivamente uma exceção.</span><span class="sxs-lookup"><span data-stu-id="bf121-369">Effectively handles an exception.</span></span>
  * <span data-ttu-id="bf121-370">`ActionExecutedContext.Result` é executado como se fosse retornado normalmente do método de ação.</span><span class="sxs-lookup"><span data-stu-id="bf121-370">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="bf121-371">Filtros de exceção</span><span class="sxs-lookup"><span data-stu-id="bf121-371">Exception filters</span></span>

<span data-ttu-id="bf121-372">Filtros de exceção:</span><span class="sxs-lookup"><span data-stu-id="bf121-372">Exception filters:</span></span>

* <span data-ttu-id="bf121-373">Implementam <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span><span class="sxs-lookup"><span data-stu-id="bf121-373">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span>
* <span data-ttu-id="bf121-374">Podem ser usados para implementar políticas de tratamento de erro comuns.</span><span class="sxs-lookup"><span data-stu-id="bf121-374">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="bf121-375">O exemplo de filtro de exceção a seguir usa uma exibição de erro personalizada para mostrar detalhes sobre exceções que ocorrem quando o aplicativo está em desenvolvimento:</span><span class="sxs-lookup"><span data-stu-id="bf121-375">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="bf121-376">O código a seguir testa o filtro de exceção:</span><span class="sxs-lookup"><span data-stu-id="bf121-376">The following code tests the exception filter:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

<span data-ttu-id="bf121-377">Filtros de exceção:</span><span class="sxs-lookup"><span data-stu-id="bf121-377">Exception filters:</span></span>

* <span data-ttu-id="bf121-378">Não têm eventos anteriores nem posteriores.</span><span class="sxs-lookup"><span data-stu-id="bf121-378">Don't have before and after events.</span></span>
* <span data-ttu-id="bf121-379">Implementam <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span><span class="sxs-lookup"><span data-stu-id="bf121-379">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="bf121-380">Tratam as exceções sem tratamento que ocorrem no Razor Page ou na criação do controlador, no [model binding](xref:mvc/models/model-binding), nos filtros de ação ou nos métodos de ação.</span><span class="sxs-lookup"><span data-stu-id="bf121-380">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="bf121-381">**Não** capturam as exceções que ocorrem em filtros de recurso, em filtros de resultado ou na execução do resultado de MVC.</span><span class="sxs-lookup"><span data-stu-id="bf121-381">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="bf121-382">Para tratar uma exceção, defina a propriedade <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> como `true` ou grave uma resposta.</span><span class="sxs-lookup"><span data-stu-id="bf121-382">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="bf121-383">Isso interrompe a propagação da exceção.</span><span class="sxs-lookup"><span data-stu-id="bf121-383">This stops propagation of the exception.</span></span> <span data-ttu-id="bf121-384">Um filtro de exceção não pode transformar uma exceção em "êxito".</span><span class="sxs-lookup"><span data-stu-id="bf121-384">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="bf121-385">Somente um filtro de ação pode fazer isso.</span><span class="sxs-lookup"><span data-stu-id="bf121-385">Only an action filter can do that.</span></span>

<span data-ttu-id="bf121-386">Filtros de exceção:</span><span class="sxs-lookup"><span data-stu-id="bf121-386">Exception filters:</span></span>

* <span data-ttu-id="bf121-387">São bons para interceptar as exceções que ocorrem nas ações.</span><span class="sxs-lookup"><span data-stu-id="bf121-387">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="bf121-388">Não são tão flexíveis quanto o middleware de tratamento de erro.</span><span class="sxs-lookup"><span data-stu-id="bf121-388">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="bf121-389">Prefira o middleware para tratamento de exceção.</span><span class="sxs-lookup"><span data-stu-id="bf121-389">Prefer middleware for exception handling.</span></span> <span data-ttu-id="bf121-390">Use filtros de exceção apenas quando o tratamento de erros *for diferente* com base no método de ação chamado.</span><span class="sxs-lookup"><span data-stu-id="bf121-390">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="bf121-391">Por exemplo, um aplicativo pode ter métodos de ação para os pontos de extremidade da API e para modos de exibição/HTML.</span><span class="sxs-lookup"><span data-stu-id="bf121-391">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="bf121-392">Os pontos de extremidade da API podem retornar informações de erro como JSON, enquanto as ações baseadas em modo de exibição podem retornar uma página de erro como HTML.</span><span class="sxs-lookup"><span data-stu-id="bf121-392">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="bf121-393">Filtros de resultado</span><span class="sxs-lookup"><span data-stu-id="bf121-393">Result filters</span></span>

<span data-ttu-id="bf121-394">Filtros de resultado:</span><span class="sxs-lookup"><span data-stu-id="bf121-394">Result filters:</span></span>

* <span data-ttu-id="bf121-395">Implemente uma interface:</span><span class="sxs-lookup"><span data-stu-id="bf121-395">Implement an interface:</span></span>
  * <span data-ttu-id="bf121-396"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="bf121-396"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="bf121-397"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="bf121-397"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="bf121-398">A execução deles envolve a execução de resultados de ação.</span><span class="sxs-lookup"><span data-stu-id="bf121-398">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="bf121-399">IResultFilter e IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="bf121-399">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="bf121-400">O código a seguir mostra um filtro de resultado que adiciona um cabeçalho HTTP:</span><span class="sxs-lookup"><span data-stu-id="bf121-400">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="bf121-401">O tipo de resultado que está sendo executado depende da ação.</span><span class="sxs-lookup"><span data-stu-id="bf121-401">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="bf121-402">Uma ação que retorna uma exibição inclui todo o processamento do Razor como parte do <xref:Microsoft.AspNetCore.Mvc.ViewResult> que está sendo executado.</span><span class="sxs-lookup"><span data-stu-id="bf121-402">An action returning a view includes all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="bf121-403">Um método de API pode executar alguma serialização como parte da execução do resultado.</span><span class="sxs-lookup"><span data-stu-id="bf121-403">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="bf121-404">Saiba mais sobre [os resultados da ação](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="bf121-404">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="bf121-405">Os filtros de resultado são executados somente quando um filtro de ação ou Action produz um resultado de ação.</span><span class="sxs-lookup"><span data-stu-id="bf121-405">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="bf121-406">Os filtros de resultado não são executados quando:</span><span class="sxs-lookup"><span data-stu-id="bf121-406">Result filters are not executed when:</span></span>

* <span data-ttu-id="bf121-407">Um filtro de autorização ou filtro de recursos de curto-circuitos do pipeline.</span><span class="sxs-lookup"><span data-stu-id="bf121-407">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="bf121-408">Um filtro de exceção trata uma exceção produzindo um resultado de ação.</span><span class="sxs-lookup"><span data-stu-id="bf121-408">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="bf121-409">O método <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> pode fazer o curto-circuito da execução do resultado da ação e dos filtros de resultados posteriores definindo <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> como `true`.</span><span class="sxs-lookup"><span data-stu-id="bf121-409">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="bf121-410">Grave no objeto de resposta ao fazer um curto-circuito para evitar gerar uma resposta vazia.</span><span class="sxs-lookup"><span data-stu-id="bf121-410">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="bf121-411">Gerando uma exceção no `IResultFilter.OnResultExecuting`:</span><span class="sxs-lookup"><span data-stu-id="bf121-411">Throwing an exception in `IResultFilter.OnResultExecuting`:</span></span>

* <span data-ttu-id="bf121-412">Impede a execução do resultado da ação e dos filtros subsequentes.</span><span class="sxs-lookup"><span data-stu-id="bf121-412">Prevents execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="bf121-413">É tratado como uma falha em vez de um resultado bem-sucedido.</span><span class="sxs-lookup"><span data-stu-id="bf121-413">Is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="bf121-414">Quando o método <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> é executado, a resposta provavelmente já foi enviada ao cliente.</span><span class="sxs-lookup"><span data-stu-id="bf121-414">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has probably already been sent to the client.</span></span> <span data-ttu-id="bf121-415">Se a resposta já tiver sido enviada ao cliente, ela não poderá ser alterada.</span><span class="sxs-lookup"><span data-stu-id="bf121-415">If the response has already been sent to the client, it cannot be changed.</span></span>

<span data-ttu-id="bf121-416">`ResultExecutedContext.Canceled` será definido como `true` se a execução do resultado da ação tiver sofrido curto-circuito por outro filtro.</span><span class="sxs-lookup"><span data-stu-id="bf121-416">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="bf121-417">`ResultExecutedContext.Exception` será definido como um valor não nulo se o resultado da ação ou um filtro de resultado posterior tiver gerado uma exceção.</span><span class="sxs-lookup"><span data-stu-id="bf121-417">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="bf121-418">A definição de `Exception` como NULL trata efetivamente de uma exceção e impede que a exceção seja gerada novamente mais tarde no pipeline.</span><span class="sxs-lookup"><span data-stu-id="bf121-418">Setting `Exception` to null effectively handles an exception and prevents the exception from being thrown again later in the pipeline.</span></span> <span data-ttu-id="bf121-419">Não há nenhuma maneira confiável para gravar dados em uma resposta ao manipular uma exceção em um filtro de resultado.</span><span class="sxs-lookup"><span data-stu-id="bf121-419">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="bf121-420">Se os cabeçalhos tiverem sido liberados para o cliente quando o resultado de uma ação gerar uma exceção, não haverá mecanismo confiável para enviar um código de falha.</span><span class="sxs-lookup"><span data-stu-id="bf121-420">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="bf121-421">Para um <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, uma chamada para `await next` no <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executa qualquer filtro de resultado posterior e o resultado da ação.</span><span class="sxs-lookup"><span data-stu-id="bf121-421">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="bf121-422">Para causar um curto-circuito, defina [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) como `true` e não chame o `ResultExecutionDelegate`:</span><span class="sxs-lookup"><span data-stu-id="bf121-422">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="bf121-423">A estrutura fornece um `ResultFilterAttribute` abstrato que você pode colocar em uma subclasse.</span><span class="sxs-lookup"><span data-stu-id="bf121-423">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="bf121-424">A classe [AddHeaderAttribute](#add-header-attribute) mostrada anteriormente é um exemplo de atributo de filtro de resultado.</span><span class="sxs-lookup"><span data-stu-id="bf121-424">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="bf121-425">IAlwaysRunResultFilter e IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="bf121-425">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="bf121-426">As interfaces <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> e <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> declaram uma implementação <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> que é executada para todos os resultados da ação.</span><span class="sxs-lookup"><span data-stu-id="bf121-426">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="bf121-427">Isso inclui resultados de ação produzidos por:</span><span class="sxs-lookup"><span data-stu-id="bf121-427">This includes action results produced by:</span></span>

* <span data-ttu-id="bf121-428">Filtros de autorização e filtros de recursos que curto-Circuit.</span><span class="sxs-lookup"><span data-stu-id="bf121-428">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="bf121-429">Filtros de exceção.</span><span class="sxs-lookup"><span data-stu-id="bf121-429">Exception filters.</span></span>

<span data-ttu-id="bf121-430">Por exemplo, o filtro a seguir sempre é executado e define o resultado de uma ação (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) com um código de status *422 Entidade Não Processável* quando ocorre falha na negociação de conteúdo:</span><span class="sxs-lookup"><span data-stu-id="bf121-430">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="bf121-431">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="bf121-431">IFilterFactory</span></span>

<span data-ttu-id="bf121-432"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="bf121-432"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="bf121-433">Portanto, uma instância `IFilterFactory` pode ser usada como uma instância `IFilterMetadata` em qualquer parte do pipeline de filtro.</span><span class="sxs-lookup"><span data-stu-id="bf121-433">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="bf121-434">Quando o runtime se prepara para invocar o filtro, tenta convertê-lo em um `IFilterFactory`.</span><span class="sxs-lookup"><span data-stu-id="bf121-434">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="bf121-435">Se essa conversão for bem-sucedida, o método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> será chamado para criar a instância `IFilterMetadata` invocada.</span><span class="sxs-lookup"><span data-stu-id="bf121-435">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="bf121-436">Isso fornece um design flexível, porque o pipeline de filtro preciso não precisa ser definido explicitamente quando o aplicativo é iniciado.</span><span class="sxs-lookup"><span data-stu-id="bf121-436">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="bf121-437">Implemente `IFilterFactory` usando implementações personalizadas de atributo como outra abordagem à criação de filtros:</span><span class="sxs-lookup"><span data-stu-id="bf121-437">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="bf121-438">O filtro é aplicado no código a seguir:</span><span class="sxs-lookup"><span data-stu-id="bf121-438">The filter is applied in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

<span data-ttu-id="bf121-439">Teste o código anterior executando o [exemplo de download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span><span class="sxs-lookup"><span data-stu-id="bf121-439">Test the preceding code by running the [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span></span>

* <span data-ttu-id="bf121-440">Invocar as ferramentas de desenvolvedor F12.</span><span class="sxs-lookup"><span data-stu-id="bf121-440">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="bf121-441">Navegue para `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="bf121-441">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="bf121-442">As ferramentas de desenvolvedor F12 exibem os seguintes cabeçalhos de resposta adicionados pelo código de exemplo:</span><span class="sxs-lookup"><span data-stu-id="bf121-442">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="bf121-443">**autor:** `Rick Anderson`</span><span class="sxs-lookup"><span data-stu-id="bf121-443">**author:** `Rick Anderson`</span></span>
* <span data-ttu-id="bf121-444">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="bf121-444">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="bf121-445">**interno:** `My header`</span><span class="sxs-lookup"><span data-stu-id="bf121-445">**internal:** `My header`</span></span>

<span data-ttu-id="bf121-446">O código anterior cria o cabeçalho de resposta **interno:** `My header`.</span><span class="sxs-lookup"><span data-stu-id="bf121-446">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="bf121-447">IFilterFactory implementado em um atributo</span><span class="sxs-lookup"><span data-stu-id="bf121-447">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="bf121-448">Filtros que implementam `IFilterFactory` são úteis para filtros que:</span><span class="sxs-lookup"><span data-stu-id="bf121-448">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="bf121-449">Não exigem a passagem de parâmetros.</span><span class="sxs-lookup"><span data-stu-id="bf121-449">Don't require passing parameters.</span></span>
* <span data-ttu-id="bf121-450">Tenha dependências de construtor que precisem ser atendidas pela DI.</span><span class="sxs-lookup"><span data-stu-id="bf121-450">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="bf121-451"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="bf121-451"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="bf121-452">`IFilterFactory` expõe o método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> para criar uma instância de <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="bf121-452">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="bf121-453">`CreateInstance` carrega o tipo especificado do contêiner de serviços (DI).</span><span class="sxs-lookup"><span data-stu-id="bf121-453">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="bf121-454">O código a seguir mostra três abordagens para aplicar o `[SampleActionFilter]`:</span><span class="sxs-lookup"><span data-stu-id="bf121-454">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="bf121-455">No código anterior, decorar o método com `[SampleActionFilter]` é a abordagem preferida para aplicar o `SampleActionFilter`.</span><span class="sxs-lookup"><span data-stu-id="bf121-455">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="bf121-456">Usando middleware no pipeline de filtros</span><span class="sxs-lookup"><span data-stu-id="bf121-456">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="bf121-457">Filtros de recursos funcionam como [middleware](xref:fundamentals/middleware/index), no sentido em que envolvem a execução de tudo o que vem depois no pipeline.</span><span class="sxs-lookup"><span data-stu-id="bf121-457">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="bf121-458">Mas os filtros diferem do middleware, pois fazem parte do tempo de execução, o que significa que eles têm acesso ao contexto e às construções.</span><span class="sxs-lookup"><span data-stu-id="bf121-458">But filters differ from middleware in that they're part of the runtime, which means that they have access to context and constructs.</span></span>

<span data-ttu-id="bf121-459">Para usar o middleware como um filtro, crie um tipo com um método `Configure` que especifica o middleware para injeção no pipeline de filtros.</span><span class="sxs-lookup"><span data-stu-id="bf121-459">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="bf121-460">O exemplo a seguir usa o middleware de localização para estabelecer a cultura atual para uma solicitação:</span><span class="sxs-lookup"><span data-stu-id="bf121-460">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="bf121-461">Use <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> para executar o middleware:</span><span class="sxs-lookup"><span data-stu-id="bf121-461">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="bf121-462">Filtros de middleware são executados no mesmo estágio do pipeline de filtros que filtros de recurso, antes do model binding e depois do restante do pipeline.</span><span class="sxs-lookup"><span data-stu-id="bf121-462">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="bf121-463">Próximas ações</span><span class="sxs-lookup"><span data-stu-id="bf121-463">Next actions</span></span>

* <span data-ttu-id="bf121-464">Consulte [métodos de filtro para Razor Pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="bf121-464">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="bf121-465">Para fazer experiências com filtros, [baixe, teste e modifique o exemplo do GitHub](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span><span class="sxs-lookup"><span data-stu-id="bf121-465">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="bf121-466">Por [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) e [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="bf121-466">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="bf121-467">Os *Filtros* no ASP.NET Core permitem a execução de código antes ou depois de determinados estágios do pipeline de processamento de solicitações.</span><span class="sxs-lookup"><span data-stu-id="bf121-467">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="bf121-468">O filtros internos lidam com tarefas como:</span><span class="sxs-lookup"><span data-stu-id="bf121-468">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="bf121-469">Autorização (impedir o acesso a recursos aos quais o usuário não está autorizado).</span><span class="sxs-lookup"><span data-stu-id="bf121-469">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="bf121-470">Cache de resposta (causar um curto-circuito do pipeline de solicitação para retornar uma resposta armazenada em cache).</span><span class="sxs-lookup"><span data-stu-id="bf121-470">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="bf121-471">É possível criar filtros personalizados para lidar com interesses paralelos.</span><span class="sxs-lookup"><span data-stu-id="bf121-471">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="bf121-472">Entre os exemplos de interesses paralelos estão o tratamento de erros, cache, configuração, autorização e registro em log.</span><span class="sxs-lookup"><span data-stu-id="bf121-472">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="bf121-473">Filtros evitam a duplicação do código.</span><span class="sxs-lookup"><span data-stu-id="bf121-473">Filters avoid duplicating code.</span></span> <span data-ttu-id="bf121-474">Por exemplo, um filtro de exceção de tratamento de erro poderia consolidar o tratamento de erro.</span><span class="sxs-lookup"><span data-stu-id="bf121-474">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="bf121-475">Este documento se aplica ao Razor Pages, a controladores de API e controladores com exibição.</span><span class="sxs-lookup"><span data-stu-id="bf121-475">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="bf121-476">[Exibir ou baixar exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="bf121-476">[View or download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="bf121-477">Como os filtros funcionam</span><span class="sxs-lookup"><span data-stu-id="bf121-477">How filters work</span></span>

<span data-ttu-id="bf121-478">Os filtros são executados dentro do *pipeline de invocação de ações do ASP.NET Core*, às vezes chamado de *pipeline de filtros*.</span><span class="sxs-lookup"><span data-stu-id="bf121-478">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="bf121-479">O pipeline de filtros é executado após o ASP.NET Core selecionar a ação a ser executada.</span><span class="sxs-lookup"><span data-stu-id="bf121-479">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![A solicitação é processada por meio de Outro Middleware, do Middleware de Roteamento, da Seleção de Ação e do Pipeline de Invocação de Ações do ASP.NET Core.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="bf121-482">Tipos de filtro</span><span class="sxs-lookup"><span data-stu-id="bf121-482">Filter types</span></span>

<span data-ttu-id="bf121-483">Cada tipo de filtro é executado em um estágio diferente no pipeline de filtros:</span><span class="sxs-lookup"><span data-stu-id="bf121-483">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="bf121-484">[Filtros de autorização](#authorization-filters) são executados primeiro e são usados para determinar se o usuário tem autorização para a solicitação.</span><span class="sxs-lookup"><span data-stu-id="bf121-484">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="bf121-485">Os filtros de autorização podem causar um curto-circuito do pipeline se uma solicitação não for autorizada.</span><span class="sxs-lookup"><span data-stu-id="bf121-485">Authorization filters short-circuit the pipeline if the request is unauthorized.</span></span>

* <span data-ttu-id="bf121-486">[Filtros de recurso](#resource-filters):</span><span class="sxs-lookup"><span data-stu-id="bf121-486">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="bf121-487">Execute após a autorização.</span><span class="sxs-lookup"><span data-stu-id="bf121-487">Run after authorization.</span></span>  
  * <span data-ttu-id="bf121-488"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> pode executar o código antes do restante do pipeline de filtros.</span><span class="sxs-lookup"><span data-stu-id="bf121-488"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> can run code before the rest of the filter pipeline.</span></span> <span data-ttu-id="bf121-489">Por exemplo, `OnResourceExecuting` pode executar o código antes do model binding.</span><span class="sxs-lookup"><span data-stu-id="bf121-489">For example, `OnResourceExecuting` can run code before model binding.</span></span>
  * <span data-ttu-id="bf121-490"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> pode executar o código após a conclusão do restante do pipeline.</span><span class="sxs-lookup"><span data-stu-id="bf121-490"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> can run code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="bf121-491">[Filtros de ação](#action-filters) podem executar código imediatamente antes e depois de um método de ação individual ser chamado.</span><span class="sxs-lookup"><span data-stu-id="bf121-491">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="bf121-492">Eles podem ser usados para manipular os argumentos passados para uma ação, bem como o resultado da ação.</span><span class="sxs-lookup"><span data-stu-id="bf121-492">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span> <span data-ttu-id="bf121-493">Os filtros de ação **não** são compatíveis com o Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="bf121-493">Action filters are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="bf121-494">[Filtros de exceção](#exception-filters) são usados para aplicar políticas globais para exceções sem tratamento que ocorrem antes que qualquer coisa tenha sido gravada no corpo da resposta.</span><span class="sxs-lookup"><span data-stu-id="bf121-494">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="bf121-495">[Filtros de resposta](#result-filters) podem executar código imediatamente antes e depois da execução de resultados de ações individuais.</span><span class="sxs-lookup"><span data-stu-id="bf121-495">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="bf121-496">Eles são executados somente quando o método de ação é executado com êxito.</span><span class="sxs-lookup"><span data-stu-id="bf121-496">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="bf121-497">Eles são úteis para a lógica que precisa envolver a execução da exibição ou do formatador.</span><span class="sxs-lookup"><span data-stu-id="bf121-497">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="bf121-498">O diagrama a seguir mostra como os tipos de filtro interagem no pipeline de filtros.</span><span class="sxs-lookup"><span data-stu-id="bf121-498">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![A solicitação é processada por meio de Filtros de autorização, Filtros de recurso, Model binding, Filtros de ação, Execução de ação e Conversão do resultado de ação, Filtros de exceção, Filtros de resultado e Execução de resultado.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="bf121-501">Implementação</span><span class="sxs-lookup"><span data-stu-id="bf121-501">Implementation</span></span>

<span data-ttu-id="bf121-502">Os filtros dão suporte a implementações síncronas e assíncronas por meio de diferentes definições de interface.</span><span class="sxs-lookup"><span data-stu-id="bf121-502">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="bf121-503">Os filtros síncronos podem executar o código antes (`On-Stage-Executing`) e depois (`On-Stage-Executed`) do estágio de pipeline.</span><span class="sxs-lookup"><span data-stu-id="bf121-503">Synchronous filters can run code before (`On-Stage-Executing`) and after (`On-Stage-Executed`) their pipeline stage.</span></span> <span data-ttu-id="bf121-504">Por exemplo, `OnActionExecuting` é chamado antes que o método de ação seja chamado.</span><span class="sxs-lookup"><span data-stu-id="bf121-504">For example, `OnActionExecuting` is called before the action method is called.</span></span> <span data-ttu-id="bf121-505">`OnActionExecuted` é chamado após o método de ação retornar.</span><span class="sxs-lookup"><span data-stu-id="bf121-505">`OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="bf121-506">Os filtros assíncronos definem um método `On-Stage-ExecutionAsync`:</span><span class="sxs-lookup"><span data-stu-id="bf121-506">Asynchronous filters define an `On-Stage-ExecutionAsync` method:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="bf121-507">No código anterior, o `SampleAsyncActionFilter` tem um <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) que executa o método de ação.</span><span class="sxs-lookup"><span data-stu-id="bf121-507">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)  that executes the action method.</span></span>  <span data-ttu-id="bf121-508">Cada um dos métodos `On-Stage-ExecutionAsync` usa um `FilterType-ExecutionDelegate` que executa o estágio de pipeline do filtro.</span><span class="sxs-lookup"><span data-stu-id="bf121-508">Each of the `On-Stage-ExecutionAsync` methods take a `FilterType-ExecutionDelegate` that executes the filter's pipeline stage.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="bf121-509">Vários estágios do filtro</span><span class="sxs-lookup"><span data-stu-id="bf121-509">Multiple filter stages</span></span>

<span data-ttu-id="bf121-510">É possível implementar interfaces para vários estágios do filtro em uma única classe.</span><span class="sxs-lookup"><span data-stu-id="bf121-510">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="bf121-511">Por exemplo, a classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> implementa `IActionFilter`, `IResultFilter` e os respectivos equivalentes assíncronos.</span><span class="sxs-lookup"><span data-stu-id="bf121-511">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

<span data-ttu-id="bf121-512">Implemente **ou** a versão assíncrona ou a versão síncrona de uma interface de filtro, **não** ambas.</span><span class="sxs-lookup"><span data-stu-id="bf121-512">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="bf121-513">Primeiro, o runtime verifica se o filtro implementa a interface assíncrona e, se for esse o caso, a chama.</span><span class="sxs-lookup"><span data-stu-id="bf121-513">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="bf121-514">Caso contrário, ela chama os métodos da interface síncrona.</span><span class="sxs-lookup"><span data-stu-id="bf121-514">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="bf121-515">Se as interfaces síncrona e assíncrona forem implementadas em uma classe, somente o método assíncrono será chamado.</span><span class="sxs-lookup"><span data-stu-id="bf121-515">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="bf121-516">Ao usar classes abstratas como <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, substitua apenas os métodos síncronos ou o método assíncrono para cada tipo de filtro.</span><span class="sxs-lookup"><span data-stu-id="bf121-516">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="bf121-517">Atributos de filtro internos</span><span class="sxs-lookup"><span data-stu-id="bf121-517">Built-in filter attributes</span></span>

<span data-ttu-id="bf121-518">O ASP.NET Core inclui filtros internos baseados em atributos que podem ser organizados em subclasses e personalizados.</span><span class="sxs-lookup"><span data-stu-id="bf121-518">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="bf121-519">Por exemplo, o filtro de resultado a seguir adiciona um cabeçalho à resposta:</span><span class="sxs-lookup"><span data-stu-id="bf121-519">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="bf121-520">Os atributos permitem que os filtros aceitem argumentos, conforme mostrado no exemplo acima.</span><span class="sxs-lookup"><span data-stu-id="bf121-520">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="bf121-521">Aplique o `AddHeaderAttribute` a um controlador ou método de ação e especifique o nome e o valor do cabeçalho HTTP:</span><span class="sxs-lookup"><span data-stu-id="bf121-521">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

<span data-ttu-id="bf121-522">Várias interfaces de filtro têm atributos correspondentes que podem ser usados como classes base para implementações personalizadas.</span><span class="sxs-lookup"><span data-stu-id="bf121-522">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="bf121-523">Atributos de filtro:</span><span class="sxs-lookup"><span data-stu-id="bf121-523">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="bf121-524">Escopos e ordem de execução dos filtros</span><span class="sxs-lookup"><span data-stu-id="bf121-524">Filter scopes and order of execution</span></span>

<span data-ttu-id="bf121-525">Um filtro pode ser adicionado ao pipeline com um de três *escopos*:</span><span class="sxs-lookup"><span data-stu-id="bf121-525">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="bf121-526">Usando um atributo em uma ação.</span><span class="sxs-lookup"><span data-stu-id="bf121-526">Using an attribute on an action.</span></span>
* <span data-ttu-id="bf121-527">Usando um atributo em um controlador.</span><span class="sxs-lookup"><span data-stu-id="bf121-527">Using an attribute on a controller.</span></span>
* <span data-ttu-id="bf121-528">Globalmente para todos os controladores e ações, como mostra o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="bf121-528">Globally for all controllers and actions as shown in the following code:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="bf121-529">O código anterior adiciona três filtros globalmente usando a coleção [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters).</span><span class="sxs-lookup"><span data-stu-id="bf121-529">The preceding code adds three filters globally using the [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) collection.</span></span>

### <a name="default-order-of-execution"></a><span data-ttu-id="bf121-530">Ordem padrão de execução</span><span class="sxs-lookup"><span data-stu-id="bf121-530">Default order of execution</span></span>

<span data-ttu-id="bf121-531">Quando há vários filtros *do mesmo tipo*, o escopo determina a ordem padrão de execução do filtro.</span><span class="sxs-lookup"><span data-stu-id="bf121-531">When there are multiple filters *of the same type*, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="bf121-532">Filtros globais envolvem filtros de classe.</span><span class="sxs-lookup"><span data-stu-id="bf121-532">Global filters surround class filters.</span></span> <span data-ttu-id="bf121-533">Filtros de classe envolvem filtros de método.</span><span class="sxs-lookup"><span data-stu-id="bf121-533">Class filters surround method filters.</span></span>

<span data-ttu-id="bf121-534">Como resultado do aninhamento de filtro, o código *posterior* dos filtros é executado na ordem inversa do código *anterior*.</span><span class="sxs-lookup"><span data-stu-id="bf121-534">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="bf121-535">A sequência de filtro:</span><span class="sxs-lookup"><span data-stu-id="bf121-535">The filter sequence:</span></span>

* <span data-ttu-id="bf121-536">O código *anterior* dos filtros globais.</span><span class="sxs-lookup"><span data-stu-id="bf121-536">The *before* code of global filters.</span></span>
  * <span data-ttu-id="bf121-537">O código *anterior* dos filtros de controlador.</span><span class="sxs-lookup"><span data-stu-id="bf121-537">The *before* code of controller filters.</span></span>
    * <span data-ttu-id="bf121-538">O código *anterior* dos filtros de método de ação.</span><span class="sxs-lookup"><span data-stu-id="bf121-538">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="bf121-539">O código *posterior* dos filtros de método de ação.</span><span class="sxs-lookup"><span data-stu-id="bf121-539">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="bf121-540">O código *posterior* dos filtros de controlador.</span><span class="sxs-lookup"><span data-stu-id="bf121-540">The *after* code of controller filters.</span></span>
* <span data-ttu-id="bf121-541">O código *posterior* dos filtros globais.</span><span class="sxs-lookup"><span data-stu-id="bf121-541">The *after* code of global filters.</span></span>
  
<span data-ttu-id="bf121-542">O exemplo a seguir ilustra a ordem na qual os métodos de filtro são chamados para filtros de ação síncrona.</span><span class="sxs-lookup"><span data-stu-id="bf121-542">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="bf121-543">Sequence</span><span class="sxs-lookup"><span data-stu-id="bf121-543">Sequence</span></span> | <span data-ttu-id="bf121-544">Escopo do filtro</span><span class="sxs-lookup"><span data-stu-id="bf121-544">Filter scope</span></span> | <span data-ttu-id="bf121-545">Método Filter</span><span class="sxs-lookup"><span data-stu-id="bf121-545">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="bf121-546">1</span><span class="sxs-lookup"><span data-stu-id="bf121-546">1</span></span> | <span data-ttu-id="bf121-547">Global</span><span class="sxs-lookup"><span data-stu-id="bf121-547">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="bf121-548">2</span><span class="sxs-lookup"><span data-stu-id="bf121-548">2</span></span> | <span data-ttu-id="bf121-549">Controlador</span><span class="sxs-lookup"><span data-stu-id="bf121-549">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="bf121-550">3</span><span class="sxs-lookup"><span data-stu-id="bf121-550">3</span></span> | <span data-ttu-id="bf121-551">Método</span><span class="sxs-lookup"><span data-stu-id="bf121-551">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="bf121-552">4</span><span class="sxs-lookup"><span data-stu-id="bf121-552">4</span></span> | <span data-ttu-id="bf121-553">Método</span><span class="sxs-lookup"><span data-stu-id="bf121-553">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="bf121-554">5</span><span class="sxs-lookup"><span data-stu-id="bf121-554">5</span></span> | <span data-ttu-id="bf121-555">Controlador</span><span class="sxs-lookup"><span data-stu-id="bf121-555">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="bf121-556">6</span><span class="sxs-lookup"><span data-stu-id="bf121-556">6</span></span> | <span data-ttu-id="bf121-557">Global</span><span class="sxs-lookup"><span data-stu-id="bf121-557">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="bf121-558">Esta sequência mostra:</span><span class="sxs-lookup"><span data-stu-id="bf121-558">This sequence shows:</span></span>

* <span data-ttu-id="bf121-559">O filtro de método está aninhado no filtro de controlador.</span><span class="sxs-lookup"><span data-stu-id="bf121-559">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="bf121-560">O filtro de controlador está aninhado no filtro global.</span><span class="sxs-lookup"><span data-stu-id="bf121-560">The controller filter is nested within the global filter.</span></span>

### <a name="controller-and-razor-page-level-filters"></a><span data-ttu-id="bf121-561">Filtros no nível do controlador e do Razor Page</span><span class="sxs-lookup"><span data-stu-id="bf121-561">Controller and Razor Page level filters</span></span>

<span data-ttu-id="bf121-562">Cada controlador que herda da classe base <xref:Microsoft.AspNetCore.Mvc.Controller> inclui os métodos [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) e [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="bf121-562">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="bf121-563">Estes métodos:</span><span class="sxs-lookup"><span data-stu-id="bf121-563">These methods:</span></span>

* <span data-ttu-id="bf121-564">Encapsulam os filtros executados para uma determinada ação.</span><span class="sxs-lookup"><span data-stu-id="bf121-564">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="bf121-565">`OnActionExecuting` é chamado antes de qualquer um dos filtros da ação.</span><span class="sxs-lookup"><span data-stu-id="bf121-565">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="bf121-566">`OnActionExecuted` é chamado após todos os filtros da ação.</span><span class="sxs-lookup"><span data-stu-id="bf121-566">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="bf121-567">`OnActionExecutionAsync` é chamado antes de qualquer um dos filtros da ação.</span><span class="sxs-lookup"><span data-stu-id="bf121-567">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="bf121-568">O código no filtro após `next` é executado após o método de ação.</span><span class="sxs-lookup"><span data-stu-id="bf121-568">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="bf121-569">Por exemplo, no download de exemplo, `MySampleActionFilter` é aplicado globalmente na inicialização.</span><span class="sxs-lookup"><span data-stu-id="bf121-569">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="bf121-570">O `TestController`:</span><span class="sxs-lookup"><span data-stu-id="bf121-570">The `TestController`:</span></span>

* <span data-ttu-id="bf121-571">Aplica o `SampleActionFilterAttribute` (`[SampleActionFilter]`) à ação `FilterTest2`.</span><span class="sxs-lookup"><span data-stu-id="bf121-571">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="bf121-572">Substitui `OnActionExecuting` e `OnActionExecuted`.</span><span class="sxs-lookup"><span data-stu-id="bf121-572">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="bf121-573">Navegar até `https://localhost:5001/Test/FilterTest2` executa o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="bf121-573">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="bf121-574">Para Razor Pages, confira [Implementar filtros do Razor Page substituindo os métodos de filtro](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span><span class="sxs-lookup"><span data-stu-id="bf121-574">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="bf121-575">Substituindo a ordem padrão</span><span class="sxs-lookup"><span data-stu-id="bf121-575">Overriding the default order</span></span>

<span data-ttu-id="bf121-576">É possível substituir a sequência padrão de execução implementando <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span><span class="sxs-lookup"><span data-stu-id="bf121-576">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="bf121-577">`IOrderedFilter` expõe a propriedade <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> que tem precedência sobre o escopo para determinar a ordem da execução.</span><span class="sxs-lookup"><span data-stu-id="bf121-577">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="bf121-578">Um filtro com um valor de `Order` menor:</span><span class="sxs-lookup"><span data-stu-id="bf121-578">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="bf121-579">Executa o código *anterior* antes de um filtro com um valor mais alto de `Order`.</span><span class="sxs-lookup"><span data-stu-id="bf121-579">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="bf121-580">Executa o código *posterior* após um filtro com um valor mais alto de `Order`.</span><span class="sxs-lookup"><span data-stu-id="bf121-580">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="bf121-581">A propriedade `Order` pode ser definida com um parâmetro de construtor:</span><span class="sxs-lookup"><span data-stu-id="bf121-581">The `Order` property can be set with a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="bf121-582">Considere os mesmos filtros de 3 ações mostrados no exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="bf121-582">Consider the same 3 action filters shown in the preceding example.</span></span> <span data-ttu-id="bf121-583">Se a propriedade `Order` do controlador e os filtros globais estiverem definidos como 1 e 2, respectivamente, a ordem de execução será invertida.</span><span class="sxs-lookup"><span data-stu-id="bf121-583">If the `Order` property of the controller and global filters is set to 1 and 2 respectively, the order of execution is reversed.</span></span>

| <span data-ttu-id="bf121-584">Sequence</span><span class="sxs-lookup"><span data-stu-id="bf121-584">Sequence</span></span> | <span data-ttu-id="bf121-585">Escopo do filtro</span><span class="sxs-lookup"><span data-stu-id="bf121-585">Filter scope</span></span> | <span data-ttu-id="bf121-586">Propriedade `Order`</span><span class="sxs-lookup"><span data-stu-id="bf121-586">`Order` property</span></span> | <span data-ttu-id="bf121-587">Método Filter</span><span class="sxs-lookup"><span data-stu-id="bf121-587">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="bf121-588">1</span><span class="sxs-lookup"><span data-stu-id="bf121-588">1</span></span> | <span data-ttu-id="bf121-589">Método</span><span class="sxs-lookup"><span data-stu-id="bf121-589">Method</span></span> | <span data-ttu-id="bf121-590">0</span><span class="sxs-lookup"><span data-stu-id="bf121-590">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="bf121-591">2</span><span class="sxs-lookup"><span data-stu-id="bf121-591">2</span></span> | <span data-ttu-id="bf121-592">Controlador</span><span class="sxs-lookup"><span data-stu-id="bf121-592">Controller</span></span> | <span data-ttu-id="bf121-593">1</span><span class="sxs-lookup"><span data-stu-id="bf121-593">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="bf121-594">3</span><span class="sxs-lookup"><span data-stu-id="bf121-594">3</span></span> | <span data-ttu-id="bf121-595">Global</span><span class="sxs-lookup"><span data-stu-id="bf121-595">Global</span></span> | <span data-ttu-id="bf121-596">2</span><span class="sxs-lookup"><span data-stu-id="bf121-596">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="bf121-597">4</span><span class="sxs-lookup"><span data-stu-id="bf121-597">4</span></span> | <span data-ttu-id="bf121-598">Global</span><span class="sxs-lookup"><span data-stu-id="bf121-598">Global</span></span> | <span data-ttu-id="bf121-599">2</span><span class="sxs-lookup"><span data-stu-id="bf121-599">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="bf121-600">5</span><span class="sxs-lookup"><span data-stu-id="bf121-600">5</span></span> | <span data-ttu-id="bf121-601">Controlador</span><span class="sxs-lookup"><span data-stu-id="bf121-601">Controller</span></span> | <span data-ttu-id="bf121-602">1</span><span class="sxs-lookup"><span data-stu-id="bf121-602">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="bf121-603">6</span><span class="sxs-lookup"><span data-stu-id="bf121-603">6</span></span> | <span data-ttu-id="bf121-604">Método</span><span class="sxs-lookup"><span data-stu-id="bf121-604">Method</span></span> | <span data-ttu-id="bf121-605">0</span><span class="sxs-lookup"><span data-stu-id="bf121-605">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="bf121-606">A propriedade `Order` substitui o escopo ao determinar a ordem na qual os filtros serão executados.</span><span class="sxs-lookup"><span data-stu-id="bf121-606">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="bf121-607">Os filtros são classificados primeiro pela ordem e o escopo é usado para desempatar.</span><span class="sxs-lookup"><span data-stu-id="bf121-607">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="bf121-608">Todos os filtros internos implementam `IOrderedFilter` e definem o valor de `Order` padrão como 0.</span><span class="sxs-lookup"><span data-stu-id="bf121-608">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="bf121-609">Para os filtros internos, o escopo determina a ordem, a menos que você defina `Order` com um valor diferente de zero.</span><span class="sxs-lookup"><span data-stu-id="bf121-609">For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="bf121-610">Cancelamento e curto-circuito</span><span class="sxs-lookup"><span data-stu-id="bf121-610">Cancellation and short-circuiting</span></span>

<span data-ttu-id="bf121-611">O pipeline de filtro pode sofrer um curto-circuito por meio da configuração da propriedade <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> no parâmetro <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> fornecido ao método do filtro.</span><span class="sxs-lookup"><span data-stu-id="bf121-611">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="bf121-612">Por exemplo, o filtro de Recurso a seguir impede que o resto do pipeline seja executado:</span><span class="sxs-lookup"><span data-stu-id="bf121-612">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="bf121-613">No código a seguir, os filtros `ShortCircuitingResourceFilter` e `AddHeader` têm como destino o método de ação `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="bf121-613">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="bf121-614">O `ShortCircuitingResourceFilter`:</span><span class="sxs-lookup"><span data-stu-id="bf121-614">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="bf121-615">É executado primeiro, porque ele é um filtro de recurso e `AddHeader` é um filtro de ação.</span><span class="sxs-lookup"><span data-stu-id="bf121-615">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="bf121-616">Causa um curto-circuito no restante do pipeline.</span><span class="sxs-lookup"><span data-stu-id="bf121-616">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="bf121-617">Portanto, o filtro `AddHeader` nunca é executado para a ação `SomeResource`.</span><span class="sxs-lookup"><span data-stu-id="bf121-617">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="bf121-618">Esse comportamento seria o mesmo se os dois filtros fossem aplicados no nível do método de ação, desde que `ShortCircuitingResourceFilter` fosse executado primeiro.</span><span class="sxs-lookup"><span data-stu-id="bf121-618">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="bf121-619">O `ShortCircuitingResourceFilter` é executado primeiro, devido ao seu tipo de filtro ou pelo uso explícito da propriedade `Order`.</span><span class="sxs-lookup"><span data-stu-id="bf121-619">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="bf121-620">Injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="bf121-620">Dependency injection</span></span>

<span data-ttu-id="bf121-621">Filtros podem ser adicionados por tipo ou por instância.</span><span class="sxs-lookup"><span data-stu-id="bf121-621">Filters can be added by type or by instance.</span></span> <span data-ttu-id="bf121-622">Se você adicionar uma instância, ela será usada para cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="bf121-622">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="bf121-623">Se um tipo for adicionado, ele será ativado pelo tipo.</span><span class="sxs-lookup"><span data-stu-id="bf121-623">If a type is added, it's type-activated.</span></span> <span data-ttu-id="bf121-624">Um filtro ativado por tipo significa:</span><span class="sxs-lookup"><span data-stu-id="bf121-624">A type-activated filter means:</span></span>

* <span data-ttu-id="bf121-625">Uma instância é criada para cada solicitação.</span><span class="sxs-lookup"><span data-stu-id="bf121-625">An instance is created for each request.</span></span>
* <span data-ttu-id="bf121-626">Qualquer dependência de construtor é preenchida pela [injeção de dependência](xref:fundamentals/dependency-injection) (DI).</span><span class="sxs-lookup"><span data-stu-id="bf121-626">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="bf121-627">Filtros que são implementados como atributos e adicionados diretamente a classes de controlador ou métodos de ação não podem ter dependências de construtor fornecidas pela DI ([injeção de dependência](xref:fundamentals/dependency-injection)).</span><span class="sxs-lookup"><span data-stu-id="bf121-627">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="bf121-628">Dependências de construtor não podem ser fornecidas pela DI porque:</span><span class="sxs-lookup"><span data-stu-id="bf121-628">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="bf121-629">Os atributos precisam ter os parâmetros de construtor fornecidos quando são aplicados.</span><span class="sxs-lookup"><span data-stu-id="bf121-629">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="bf121-630">Essa é uma limitação do funcionamento dos atributos.</span><span class="sxs-lookup"><span data-stu-id="bf121-630">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="bf121-631">Os filtros a seguir são compatíveis com dependências de construtor fornecidas pela injeção de dependência:</span><span class="sxs-lookup"><span data-stu-id="bf121-631">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="bf121-632"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementado no atributo.</span><span class="sxs-lookup"><span data-stu-id="bf121-632"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="bf121-633">Os filtros anteriores podem ser aplicados a um método de ação ou controlador:</span><span class="sxs-lookup"><span data-stu-id="bf121-633">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="bf121-634">Os agentes estão disponíveis na DI.</span><span class="sxs-lookup"><span data-stu-id="bf121-634">Loggers are available from DI.</span></span> <span data-ttu-id="bf121-635">No entanto, evite criar e usar filtros apenas para fins de registro em log.</span><span class="sxs-lookup"><span data-stu-id="bf121-635">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="bf121-636">O [registro em log da estrutura interna](xref:fundamentals/logging/index) normalmente fornece o que é necessário para o registro em log.</span><span class="sxs-lookup"><span data-stu-id="bf121-636">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="bf121-637">Registro em log adicionado aos filtros:</span><span class="sxs-lookup"><span data-stu-id="bf121-637">Logging added to filters:</span></span>

* <span data-ttu-id="bf121-638">Deve se concentrar em questões de domínio de negócios ou comportamento específico ao filtro.</span><span class="sxs-lookup"><span data-stu-id="bf121-638">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="bf121-639">**Não** deve registrar ações ou outros eventos da estrutura.</span><span class="sxs-lookup"><span data-stu-id="bf121-639">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="bf121-640">Os filtros internos registram ações e eventos da estrutura.</span><span class="sxs-lookup"><span data-stu-id="bf121-640">The built in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="bf121-641">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="bf121-641">ServiceFilterAttribute</span></span>

<span data-ttu-id="bf121-642">Os tipos de implementação do filtro de serviço são registrados em `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="bf121-642">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="bf121-643">Um <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> recupera uma instância do filtro da DI.</span><span class="sxs-lookup"><span data-stu-id="bf121-643">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="bf121-644">O código a seguir mostra `AddHeaderResultServiceFilter`:</span><span class="sxs-lookup"><span data-stu-id="bf121-644">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="bf121-645">No código a seguir, `AddHeaderResultServiceFilter` é adicionado ao contêiner de DI:</span><span class="sxs-lookup"><span data-stu-id="bf121-645">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="bf121-646">No código a seguir, o atributo `ServiceFilter` recupera uma instância do filtro `AddHeaderResultServiceFilter` da DI:</span><span class="sxs-lookup"><span data-stu-id="bf121-646">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="bf121-647">Ao usar `ServiceFilterAttribute`, configurar [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="bf121-647">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="bf121-648">Fornece uma dica de que a instância de filtro *pode* ser reutilizada fora do escopo de solicitação em que foi criada.</span><span class="sxs-lookup"><span data-stu-id="bf121-648">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="bf121-649">O runtime do ASP.NET Core não garante:</span><span class="sxs-lookup"><span data-stu-id="bf121-649">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="bf121-650">Que uma única instância do filtro será criada.</span><span class="sxs-lookup"><span data-stu-id="bf121-650">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="bf121-651">Que o filtro não será solicitado novamente no contêiner de DI em algum momento posterior.</span><span class="sxs-lookup"><span data-stu-id="bf121-651">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="bf121-652">Não deve ser usado com um filtro que dependa dos serviços com um tempo de vida diferente de singleton.</span><span class="sxs-lookup"><span data-stu-id="bf121-652">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="bf121-653"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="bf121-653"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="bf121-654">`IFilterFactory` expõe o método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> para criar uma instância de <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="bf121-654">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="bf121-655">`CreateInstance` carrega o tipo especificado na DI.</span><span class="sxs-lookup"><span data-stu-id="bf121-655">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="bf121-656">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="bf121-656">TypeFilterAttribute</span></span>

<span data-ttu-id="bf121-657">O <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> é semelhante ao <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, mas seu tipo não é resolvido diretamente por meio do contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="bf121-657"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="bf121-658">Ele cria uma instância do tipo usando <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="bf121-658">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="bf121-659">Como os tipos `TypeFilterAttribute` não são resolvidos diretamente do contêiner de DI:</span><span class="sxs-lookup"><span data-stu-id="bf121-659">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="bf121-660">Os tipos referenciados usando o `TypeFilterAttribute` não precisam ser registrados no contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="bf121-660">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="bf121-661">Eles têm suas dependências atendidas pelo contêiner de DI.</span><span class="sxs-lookup"><span data-stu-id="bf121-661">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="bf121-662">Opcionalmente, o `TypeFilterAttribute` pode aceitar argumentos de construtor para o tipo.</span><span class="sxs-lookup"><span data-stu-id="bf121-662">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="bf121-663">Ao usar `TypeFilterAttribute`, configurar [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span><span class="sxs-lookup"><span data-stu-id="bf121-663">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="bf121-664">Fornece uma dica de que a instância de filtro *pode* ser reutilizada fora do escopo de solicitação em que foi criada.</span><span class="sxs-lookup"><span data-stu-id="bf121-664">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="bf121-665">O runtime do ASP.NET Core não fornece garantias de que uma única instância do filtro será criada.</span><span class="sxs-lookup"><span data-stu-id="bf121-665">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="bf121-666">Não deve ser usado com um filtro que dependa dos serviços com um tempo de vida diferente de singleton.</span><span class="sxs-lookup"><span data-stu-id="bf121-666">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="bf121-667">O exemplo a seguir mostra como passar argumentos para um tipo usando `TypeFilterAttribute`:</span><span class="sxs-lookup"><span data-stu-id="bf121-667">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="bf121-668">Filtros de autorização</span><span class="sxs-lookup"><span data-stu-id="bf121-668">Authorization filters</span></span>

<span data-ttu-id="bf121-669">Filtros de autorização:</span><span class="sxs-lookup"><span data-stu-id="bf121-669">Authorization filters:</span></span>

* <span data-ttu-id="bf121-670">São os primeiros filtros executados no pipeline de filtro.</span><span class="sxs-lookup"><span data-stu-id="bf121-670">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="bf121-671">Controlam o acesso aos métodos de ação.</span><span class="sxs-lookup"><span data-stu-id="bf121-671">Control access to action methods.</span></span>
* <span data-ttu-id="bf121-672">Têm um método anterior, mas não têm um método posterior.</span><span class="sxs-lookup"><span data-stu-id="bf121-672">Have a before method, but no after method.</span></span>

<span data-ttu-id="bf121-673">Filtros de autorização personalizados exigem uma estrutura de autorização personalizada.</span><span class="sxs-lookup"><span data-stu-id="bf121-673">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="bf121-674">Prefira configurar as políticas de autorização ou escrever uma política de autorização personalizada em vez de escrever um filtro personalizado.</span><span class="sxs-lookup"><span data-stu-id="bf121-674">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="bf121-675">O filtro de autorização interno:</span><span class="sxs-lookup"><span data-stu-id="bf121-675">The built-in authorization filter:</span></span>

* <span data-ttu-id="bf121-676">Chama o sistema de autorização.</span><span class="sxs-lookup"><span data-stu-id="bf121-676">Calls the authorization system.</span></span>
* <span data-ttu-id="bf121-677">Não autoriza solicitações.</span><span class="sxs-lookup"><span data-stu-id="bf121-677">Does not authorize requests.</span></span>

<span data-ttu-id="bf121-678">**Não** gera exceções dentro de filtros de autorização:</span><span class="sxs-lookup"><span data-stu-id="bf121-678">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="bf121-679">A exceção não será tratada.</span><span class="sxs-lookup"><span data-stu-id="bf121-679">The exception will not be handled.</span></span>
* <span data-ttu-id="bf121-680">Os filtros de exceção não tratarão a exceção.</span><span class="sxs-lookup"><span data-stu-id="bf121-680">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="bf121-681">Considere a possibilidade de emitir um desafio quando ocorrer uma exceção em um filtro de autorização.</span><span class="sxs-lookup"><span data-stu-id="bf121-681">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="bf121-682">Saiba mais sobre [Autorização](xref:security/authorization/introduction).</span><span class="sxs-lookup"><span data-stu-id="bf121-682">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="bf121-683">Filtros de recurso</span><span class="sxs-lookup"><span data-stu-id="bf121-683">Resource filters</span></span>

<span data-ttu-id="bf121-684">Filtros de recurso:</span><span class="sxs-lookup"><span data-stu-id="bf121-684">Resource filters:</span></span>

* <span data-ttu-id="bf121-685">Implementam a interface <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.</span><span class="sxs-lookup"><span data-stu-id="bf121-685">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="bf121-686">A execução encapsula grande parte do pipeline de filtro.</span><span class="sxs-lookup"><span data-stu-id="bf121-686">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="bf121-687">Somente os [Filtros de autorização](#authorization-filters) são executados antes dos filtros de recurso.</span><span class="sxs-lookup"><span data-stu-id="bf121-687">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="bf121-688">Os filtros de recursos são úteis para causar um curto-circuito na maior parte do pipeline.</span><span class="sxs-lookup"><span data-stu-id="bf121-688">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="bf121-689">Por exemplo, um filtro de cache pode evitar o restante do pipeline em uma ocorrência no cache.</span><span class="sxs-lookup"><span data-stu-id="bf121-689">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="bf121-690">Exemplos de filtros de recurso:</span><span class="sxs-lookup"><span data-stu-id="bf121-690">Resource filter examples:</span></span>

* <span data-ttu-id="bf121-691">[O filtro de recurso em curto-circuito](#short-circuiting-resource-filter) mostrado anteriormente.</span><span class="sxs-lookup"><span data-stu-id="bf121-691">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="bf121-692">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span><span class="sxs-lookup"><span data-stu-id="bf121-692">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="bf121-693">Impede o model binding de acessar os dados do formulário.</span><span class="sxs-lookup"><span data-stu-id="bf121-693">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="bf121-694">Usado para uploads de arquivos grandes para impedir que os dados de formulário sejam lidos na memória.</span><span class="sxs-lookup"><span data-stu-id="bf121-694">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="bf121-695">Filtros de ação</span><span class="sxs-lookup"><span data-stu-id="bf121-695">Action filters</span></span>

> [!IMPORTANT]
> <span data-ttu-id="bf121-696">Os filtros de ação **não** se aplicam ao Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="bf121-696">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="bf121-697">O Razor Pages é compatível com <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> e <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter>.</span><span class="sxs-lookup"><span data-stu-id="bf121-697">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="bf121-698">Para obter mais informações, confira [Métodos de filtro para Páginas Razor](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="bf121-698">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="bf121-699">Filtros de ação:</span><span class="sxs-lookup"><span data-stu-id="bf121-699">Action filters:</span></span>

* <span data-ttu-id="bf121-700">Implementam a interface <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.</span><span class="sxs-lookup"><span data-stu-id="bf121-700">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="bf121-701">A execução deles envolve a execução de métodos de ação.</span><span class="sxs-lookup"><span data-stu-id="bf121-701">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="bf121-702">O código a seguir mostra um exemplo de filtro de ação:</span><span class="sxs-lookup"><span data-stu-id="bf121-702">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="bf121-703">A classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> fornece as seguintes propriedades:</span><span class="sxs-lookup"><span data-stu-id="bf121-703">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="bf121-704"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - permite a leitura das entradas para um método de ação.</span><span class="sxs-lookup"><span data-stu-id="bf121-704"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables the inputs to an action method be read.</span></span>
* <span data-ttu-id="bf121-705"><xref:Microsoft.AspNetCore.Mvc.Controller> – permite a manipulação da instância do controlador.</span><span class="sxs-lookup"><span data-stu-id="bf121-705"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="bf121-706"><xref:System.Web.Mvc.ActionExecutingContext.Result> – configuração de `Result` execução de curtos-circuitos do método de ação e dos filtros de ação posteriores.</span><span class="sxs-lookup"><span data-stu-id="bf121-706"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="bf121-707">Gerar uma exceção em um método de ação:</span><span class="sxs-lookup"><span data-stu-id="bf121-707">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="bf121-708">Impede a execução de filtros subsequentes.</span><span class="sxs-lookup"><span data-stu-id="bf121-708">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="bf121-709">Ao contrário da configuração `Result`, é tratada como uma falha e não como um resultado bem-sucedido.</span><span class="sxs-lookup"><span data-stu-id="bf121-709">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="bf121-710">A classe <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> fornece `Controller` e `Result`, além das seguintes propriedades:</span><span class="sxs-lookup"><span data-stu-id="bf121-710">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="bf121-711"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> – verdadeiro se a execução da ação tiver sofrido curto-circuito por outro filtro.</span><span class="sxs-lookup"><span data-stu-id="bf121-711"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="bf121-712"><xref:System.Web.Mvc.ActionExecutedContext.Exception> – não será nulo se a ação ou um filtro de ação executado antes tiver apresentado uma exceção.</span><span class="sxs-lookup"><span data-stu-id="bf121-712"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="bf121-713">Definir essa propriedade como nulo:</span><span class="sxs-lookup"><span data-stu-id="bf121-713">Setting this property to null:</span></span>

  * <span data-ttu-id="bf121-714">Trata efetivamente a exceção.</span><span class="sxs-lookup"><span data-stu-id="bf121-714">Effectively handles the exception.</span></span>
  * <span data-ttu-id="bf121-715">`Result` é executado como se tivesse retornado do método de ação.</span><span class="sxs-lookup"><span data-stu-id="bf121-715">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="bf121-716">Para um `IAsyncActionFilter`, uma chamada para o <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span><span class="sxs-lookup"><span data-stu-id="bf121-716">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="bf121-717">Executa todos os próximos filtros de ação e o método de ação.</span><span class="sxs-lookup"><span data-stu-id="bf121-717">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="bf121-718">Retorna `ActionExecutedContext`.</span><span class="sxs-lookup"><span data-stu-id="bf121-718">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="bf121-719">Para fazer um curto-circuito, atribua <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> a uma instância de resultado e não chame o `next` (`ActionExecutionDelegate`).</span><span class="sxs-lookup"><span data-stu-id="bf121-719">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="bf121-720">A estrutura fornece um <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> abstrato que você pode colocar em uma subclasse.</span><span class="sxs-lookup"><span data-stu-id="bf121-720">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="bf121-721">O filtro de ação `OnActionExecuting` pode ser usado para:</span><span class="sxs-lookup"><span data-stu-id="bf121-721">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="bf121-722">Validar o estado do modelo.</span><span class="sxs-lookup"><span data-stu-id="bf121-722">Validate model state.</span></span>
* <span data-ttu-id="bf121-723">Retornar um erro se o estado for inválido.</span><span class="sxs-lookup"><span data-stu-id="bf121-723">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="bf121-724">O método `OnActionExecuted` é executado após o método de ação:</span><span class="sxs-lookup"><span data-stu-id="bf121-724">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="bf121-725">E pode ver e manipular os resultados da ação por meio da propriedade <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result>.</span><span class="sxs-lookup"><span data-stu-id="bf121-725">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="bf121-726"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> é definido como verdadeiro se a execução da ação tiver sofrido curto-circuito por outro filtro.</span><span class="sxs-lookup"><span data-stu-id="bf121-726"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="bf121-727"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> é definido como um valor não nulo se a ação ou um filtro de ação posterior tiver apresentado uma exceção.</span><span class="sxs-lookup"><span data-stu-id="bf121-727"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="bf121-728">Definindo `Exception` como nulo:</span><span class="sxs-lookup"><span data-stu-id="bf121-728">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="bf121-729">Trata efetivamente uma exceção.</span><span class="sxs-lookup"><span data-stu-id="bf121-729">Effectively handles an exception.</span></span>
  * <span data-ttu-id="bf121-730">`ActionExecutedContext.Result` é executado como se fosse retornado normalmente do método de ação.</span><span class="sxs-lookup"><span data-stu-id="bf121-730">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="bf121-731">Filtros de exceção</span><span class="sxs-lookup"><span data-stu-id="bf121-731">Exception filters</span></span>

<span data-ttu-id="bf121-732">Filtros de exceção:</span><span class="sxs-lookup"><span data-stu-id="bf121-732">Exception filters:</span></span>

* <span data-ttu-id="bf121-733">Implementam <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span><span class="sxs-lookup"><span data-stu-id="bf121-733">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span> 
* <span data-ttu-id="bf121-734">Podem ser usados para implementar políticas de tratamento de erro comuns.</span><span class="sxs-lookup"><span data-stu-id="bf121-734">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="bf121-735">O exemplo de filtro de exceção a seguir usa uma exibição de erro personalizada para mostrar detalhes sobre exceções que ocorrem quando o aplicativo está em desenvolvimento:</span><span class="sxs-lookup"><span data-stu-id="bf121-735">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="bf121-736">Filtros de exceção:</span><span class="sxs-lookup"><span data-stu-id="bf121-736">Exception filters:</span></span>

* <span data-ttu-id="bf121-737">Não têm eventos anteriores nem posteriores.</span><span class="sxs-lookup"><span data-stu-id="bf121-737">Don't have before and after events.</span></span>
* <span data-ttu-id="bf121-738">Implementam <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span><span class="sxs-lookup"><span data-stu-id="bf121-738">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="bf121-739">Tratam as exceções sem tratamento que ocorrem no Razor Page ou na criação do controlador, no [model binding](xref:mvc/models/model-binding), nos filtros de ação ou nos métodos de ação.</span><span class="sxs-lookup"><span data-stu-id="bf121-739">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="bf121-740">**Não** capturam as exceções que ocorrem em filtros de recurso, em filtros de resultado ou na execução do resultado de MVC.</span><span class="sxs-lookup"><span data-stu-id="bf121-740">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="bf121-741">Para tratar uma exceção, defina a propriedade <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> como `true` ou grave uma resposta.</span><span class="sxs-lookup"><span data-stu-id="bf121-741">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="bf121-742">Isso interrompe a propagação da exceção.</span><span class="sxs-lookup"><span data-stu-id="bf121-742">This stops propagation of the exception.</span></span> <span data-ttu-id="bf121-743">Um filtro de exceção não pode transformar uma exceção em "êxito".</span><span class="sxs-lookup"><span data-stu-id="bf121-743">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="bf121-744">Somente um filtro de ação pode fazer isso.</span><span class="sxs-lookup"><span data-stu-id="bf121-744">Only an action filter can do that.</span></span>

<span data-ttu-id="bf121-745">Filtros de exceção:</span><span class="sxs-lookup"><span data-stu-id="bf121-745">Exception filters:</span></span>

* <span data-ttu-id="bf121-746">São bons para interceptar as exceções que ocorrem nas ações.</span><span class="sxs-lookup"><span data-stu-id="bf121-746">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="bf121-747">Não são tão flexíveis quanto o middleware de tratamento de erro.</span><span class="sxs-lookup"><span data-stu-id="bf121-747">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="bf121-748">Prefira o middleware para tratamento de exceção.</span><span class="sxs-lookup"><span data-stu-id="bf121-748">Prefer middleware for exception handling.</span></span> <span data-ttu-id="bf121-749">Use filtros de exceção apenas quando o tratamento de erros *for diferente* com base no método de ação chamado.</span><span class="sxs-lookup"><span data-stu-id="bf121-749">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="bf121-750">Por exemplo, um aplicativo pode ter métodos de ação para os pontos de extremidade da API e para modos de exibição/HTML.</span><span class="sxs-lookup"><span data-stu-id="bf121-750">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="bf121-751">Os pontos de extremidade da API podem retornar informações de erro como JSON, enquanto as ações baseadas em modo de exibição podem retornar uma página de erro como HTML.</span><span class="sxs-lookup"><span data-stu-id="bf121-751">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="bf121-752">Filtros de resultado</span><span class="sxs-lookup"><span data-stu-id="bf121-752">Result filters</span></span>

<span data-ttu-id="bf121-753">Filtros de resultado:</span><span class="sxs-lookup"><span data-stu-id="bf121-753">Result filters:</span></span>

* <span data-ttu-id="bf121-754">Implemente uma interface:</span><span class="sxs-lookup"><span data-stu-id="bf121-754">Implement an interface:</span></span>
  * <span data-ttu-id="bf121-755"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="bf121-755"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="bf121-756"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> ou <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="bf121-756"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="bf121-757">A execução deles envolve a execução de resultados de ação.</span><span class="sxs-lookup"><span data-stu-id="bf121-757">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="bf121-758">IResultFilter e IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="bf121-758">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="bf121-759">O código a seguir mostra um filtro de resultado que adiciona um cabeçalho HTTP:</span><span class="sxs-lookup"><span data-stu-id="bf121-759">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="bf121-760">O tipo de resultado que está sendo executado depende da ação.</span><span class="sxs-lookup"><span data-stu-id="bf121-760">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="bf121-761">Uma ação que retorna um modo de exibição incluiria todo o processamento de Razor como parte do <xref:Microsoft.AspNetCore.Mvc.ViewResult> em execução.</span><span class="sxs-lookup"><span data-stu-id="bf121-761">An action returning a view would include all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="bf121-762">Um método de API pode executar alguma serialização como parte da execução do resultado.</span><span class="sxs-lookup"><span data-stu-id="bf121-762">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="bf121-763">Saiba mais sobre [os resultados da ação](xref:mvc/controllers/actions).</span><span class="sxs-lookup"><span data-stu-id="bf121-763">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="bf121-764">Os filtros de resultado são executados somente quando um filtro de ação ou Action produz um resultado de ação.</span><span class="sxs-lookup"><span data-stu-id="bf121-764">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="bf121-765">Os filtros de resultado não são executados quando:</span><span class="sxs-lookup"><span data-stu-id="bf121-765">Result filters are not executed when:</span></span>

* <span data-ttu-id="bf121-766">Um filtro de autorização ou filtro de recursos de curto-circuitos do pipeline.</span><span class="sxs-lookup"><span data-stu-id="bf121-766">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="bf121-767">Um filtro de exceção trata uma exceção produzindo um resultado de ação.</span><span class="sxs-lookup"><span data-stu-id="bf121-767">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="bf121-768">O método <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> pode fazer o curto-circuito da execução do resultado da ação e dos filtros de resultados posteriores definindo <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> como `true`.</span><span class="sxs-lookup"><span data-stu-id="bf121-768">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="bf121-769">Grave no objeto de resposta ao fazer um curto-circuito para evitar gerar uma resposta vazia.</span><span class="sxs-lookup"><span data-stu-id="bf121-769">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="bf121-770">A geração de uma exceção em `IResultFilter.OnResultExecuting`:</span><span class="sxs-lookup"><span data-stu-id="bf121-770">Throwing an exception in `IResultFilter.OnResultExecuting` will:</span></span>

* <span data-ttu-id="bf121-771">Impedir a execução do resultado da ação e dos próximos filtros.</span><span class="sxs-lookup"><span data-stu-id="bf121-771">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="bf121-772">Ser tratada como uma falha e não como um resultado com êxito.</span><span class="sxs-lookup"><span data-stu-id="bf121-772">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="bf121-773">Quando o método <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> é executado, a resposta provavelmente já foi enviada para o cliente.</span><span class="sxs-lookup"><span data-stu-id="bf121-773">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has likely already been sent to the client.</span></span> <span data-ttu-id="bf121-774">Se a resposta já tiver sido enviada ao cliente, ela não poderá ser alterada ainda mais.</span><span class="sxs-lookup"><span data-stu-id="bf121-774">If the response has already been sent to the client, it cannot be changed further.</span></span>

<span data-ttu-id="bf121-775">`ResultExecutedContext.Canceled` será definido como `true` se a execução do resultado da ação tiver sofrido curto-circuito por outro filtro.</span><span class="sxs-lookup"><span data-stu-id="bf121-775">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="bf121-776">`ResultExecutedContext.Exception` será definido como um valor não nulo se o resultado da ação ou um filtro de resultado posterior tiver gerado uma exceção.</span><span class="sxs-lookup"><span data-stu-id="bf121-776">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="bf121-777">Definir `Exception` como nulo trata uma exceção com eficiência e impede que ela seja gerada novamente pelo ASP.NET Core posteriormente no pipeline.</span><span class="sxs-lookup"><span data-stu-id="bf121-777">Setting `Exception` to null effectively handles an exception and prevents the exception from being rethrown by ASP.NET Core later in the pipeline.</span></span> <span data-ttu-id="bf121-778">Não há nenhuma maneira confiável para gravar dados em uma resposta ao manipular uma exceção em um filtro de resultado.</span><span class="sxs-lookup"><span data-stu-id="bf121-778">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="bf121-779">Se os cabeçalhos tiverem sido liberados para o cliente quando o resultado de uma ação gerar uma exceção, não haverá mecanismo confiável para enviar um código de falha.</span><span class="sxs-lookup"><span data-stu-id="bf121-779">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="bf121-780">Para um <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, uma chamada para `await next` no <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executa qualquer filtro de resultado posterior e o resultado da ação.</span><span class="sxs-lookup"><span data-stu-id="bf121-780">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="bf121-781">Para causar um curto-circuito, defina [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) como `true` e não chame o `ResultExecutionDelegate`:</span><span class="sxs-lookup"><span data-stu-id="bf121-781">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="bf121-782">A estrutura fornece um `ResultFilterAttribute` abstrato que você pode colocar em uma subclasse.</span><span class="sxs-lookup"><span data-stu-id="bf121-782">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="bf121-783">A classe [AddHeaderAttribute](#add-header-attribute) mostrada anteriormente é um exemplo de atributo de filtro de resultado.</span><span class="sxs-lookup"><span data-stu-id="bf121-783">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="bf121-784">IAlwaysRunResultFilter e IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="bf121-784">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="bf121-785">As interfaces <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> e <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> declaram uma implementação <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> que é executada para todos os resultados da ação.</span><span class="sxs-lookup"><span data-stu-id="bf121-785">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="bf121-786">Isso inclui resultados de ação produzidos por:</span><span class="sxs-lookup"><span data-stu-id="bf121-786">This includes action results produced by:</span></span>

* <span data-ttu-id="bf121-787">Filtros de autorização e filtros de recursos que curto-Circuit.</span><span class="sxs-lookup"><span data-stu-id="bf121-787">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="bf121-788">Filtros de exceção.</span><span class="sxs-lookup"><span data-stu-id="bf121-788">Exception filters.</span></span>

<span data-ttu-id="bf121-789">Por exemplo, o filtro a seguir sempre é executado e define o resultado de uma ação (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) com um código de status *422 Entidade Não Processável* quando ocorre falha na negociação de conteúdo:</span><span class="sxs-lookup"><span data-stu-id="bf121-789">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="bf121-790">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="bf121-790">IFilterFactory</span></span>

<span data-ttu-id="bf121-791"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="bf121-791"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="bf121-792">Portanto, uma instância `IFilterFactory` pode ser usada como uma instância `IFilterMetadata` em qualquer parte do pipeline de filtro.</span><span class="sxs-lookup"><span data-stu-id="bf121-792">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="bf121-793">Quando o runtime se prepara para invocar o filtro, tenta convertê-lo em um `IFilterFactory`.</span><span class="sxs-lookup"><span data-stu-id="bf121-793">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="bf121-794">Se essa conversão for bem-sucedida, o método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> será chamado para criar a instância `IFilterMetadata` invocada.</span><span class="sxs-lookup"><span data-stu-id="bf121-794">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="bf121-795">Isso fornece um design flexível, porque o pipeline de filtro preciso não precisa ser definido explicitamente quando o aplicativo é iniciado.</span><span class="sxs-lookup"><span data-stu-id="bf121-795">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="bf121-796">Implemente `IFilterFactory` usando implementações personalizadas de atributo como outra abordagem à criação de filtros:</span><span class="sxs-lookup"><span data-stu-id="bf121-796">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="bf121-797">O código anterior pode ser testado executando o [exemplo para download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span><span class="sxs-lookup"><span data-stu-id="bf121-797">The preceding code can be tested by running the [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span></span>

* <span data-ttu-id="bf121-798">Invocar as ferramentas de desenvolvedor F12.</span><span class="sxs-lookup"><span data-stu-id="bf121-798">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="bf121-799">Navegue para `https://localhost:5001/Sample/HeaderWithFactory`.</span><span class="sxs-lookup"><span data-stu-id="bf121-799">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="bf121-800">As ferramentas de desenvolvedor F12 exibem os seguintes cabeçalhos de resposta adicionados pelo código de exemplo:</span><span class="sxs-lookup"><span data-stu-id="bf121-800">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="bf121-801">**autor:** `Joe Smith`</span><span class="sxs-lookup"><span data-stu-id="bf121-801">**author:** `Joe Smith`</span></span>
* <span data-ttu-id="bf121-802">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="bf121-802">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="bf121-803">**interno:** `My header`</span><span class="sxs-lookup"><span data-stu-id="bf121-803">**internal:** `My header`</span></span>

<span data-ttu-id="bf121-804">O código anterior cria o cabeçalho de resposta **interno:** `My header`.</span><span class="sxs-lookup"><span data-stu-id="bf121-804">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="bf121-805">IFilterFactory implementado em um atributo</span><span class="sxs-lookup"><span data-stu-id="bf121-805">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="bf121-806">Filtros que implementam `IFilterFactory` são úteis para filtros que:</span><span class="sxs-lookup"><span data-stu-id="bf121-806">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="bf121-807">Não exigem a passagem de parâmetros.</span><span class="sxs-lookup"><span data-stu-id="bf121-807">Don't require passing parameters.</span></span>
* <span data-ttu-id="bf121-808">Tenha dependências de construtor que precisem ser atendidas pela DI.</span><span class="sxs-lookup"><span data-stu-id="bf121-808">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="bf121-809"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implementa <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span><span class="sxs-lookup"><span data-stu-id="bf121-809"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="bf121-810">`IFilterFactory` expõe o método <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> para criar uma instância de <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span><span class="sxs-lookup"><span data-stu-id="bf121-810">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="bf121-811">`CreateInstance` carrega o tipo especificado do contêiner de serviços (DI).</span><span class="sxs-lookup"><span data-stu-id="bf121-811">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="bf121-812">O código a seguir mostra três abordagens para aplicar o `[SampleActionFilter]`:</span><span class="sxs-lookup"><span data-stu-id="bf121-812">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="bf121-813">No código anterior, decorar o método com `[SampleActionFilter]` é a abordagem preferida para aplicar o `SampleActionFilter`.</span><span class="sxs-lookup"><span data-stu-id="bf121-813">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="bf121-814">Usando middleware no pipeline de filtros</span><span class="sxs-lookup"><span data-stu-id="bf121-814">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="bf121-815">Filtros de recursos funcionam como [middleware](xref:fundamentals/middleware/index), no sentido em que envolvem a execução de tudo o que vem depois no pipeline.</span><span class="sxs-lookup"><span data-stu-id="bf121-815">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="bf121-816">Mas os filtros diferem do middleware porque fazem parte do ASP.NET Core, o que significa que têm acesso ao contexto e a constructos do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bf121-816">But filters differ from middleware in that they're part of the ASP.NET Core runtime, which means that they have access to ASP.NET Core context and constructs.</span></span>

<span data-ttu-id="bf121-817">Para usar o middleware como um filtro, crie um tipo com um método `Configure` que especifica o middleware para injeção no pipeline de filtros.</span><span class="sxs-lookup"><span data-stu-id="bf121-817">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="bf121-818">O exemplo a seguir usa o middleware de localização para estabelecer a cultura atual para uma solicitação:</span><span class="sxs-lookup"><span data-stu-id="bf121-818">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="bf121-819">Use <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> para executar o middleware:</span><span class="sxs-lookup"><span data-stu-id="bf121-819">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="bf121-820">Filtros de middleware são executados no mesmo estágio do pipeline de filtros que filtros de recurso, antes do model binding e depois do restante do pipeline.</span><span class="sxs-lookup"><span data-stu-id="bf121-820">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="bf121-821">Próximas ações</span><span class="sxs-lookup"><span data-stu-id="bf121-821">Next actions</span></span>

* <span data-ttu-id="bf121-822">Consulte [métodos de filtro para Razor Pages](xref:razor-pages/filter).</span><span class="sxs-lookup"><span data-stu-id="bf121-822">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="bf121-823">Para fazer experiências com filtros, [baixe, teste e modifique o exemplo do GitHub](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span><span class="sxs-lookup"><span data-stu-id="bf121-823">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>

::: moniker-end
