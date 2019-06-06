---
title: Tipos de retorno de ação do controlador na API Web ASP.NET Core
author: scottaddie
description: Aprenda a usar os vários tipos de retorno do método de ação do controlador em uma API Web ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/04/2019
uid: web-api/action-return-types
ms.openlocfilehash: b89ead55cd46ef62a3bc28b1cfc9077d3ce9aba2
ms.sourcegitcommit: a04eb20e81243930ec829a9db5dd5de49f669450
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/03/2019
ms.locfileid: "66470403"
---
# <a name="controller-action-return-types-in-aspnet-core-web-api"></a><span data-ttu-id="f6775-103">Tipos de retorno de ação do controlador na API Web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f6775-103">Controller action return types in ASP.NET Core Web API</span></span>

<span data-ttu-id="f6775-104">Por [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="f6775-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="f6775-105">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/action-return-types/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f6775-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/action-return-types/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="f6775-106">O ASP.NET Core oferece as seguintes opções para os tipos de retorno da ação do controlador da API Web:</span><span class="sxs-lookup"><span data-stu-id="f6775-106">ASP.NET Core offers the following options for Web API controller action return types:</span></span>

::: moniker range=">= aspnetcore-2.1"

* [<span data-ttu-id="f6775-107">Tipo específico</span><span class="sxs-lookup"><span data-stu-id="f6775-107">Specific type</span></span>](#specific-type)
* [<span data-ttu-id="f6775-108">IActionResult</span><span class="sxs-lookup"><span data-stu-id="f6775-108">IActionResult</span></span>](#iactionresult-type)
* [<span data-ttu-id="f6775-109">ActionResult\<T></span><span class="sxs-lookup"><span data-stu-id="f6775-109">ActionResult\<T></span></span>](#actionresultt-type)

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* [<span data-ttu-id="f6775-110">Tipo específico</span><span class="sxs-lookup"><span data-stu-id="f6775-110">Specific type</span></span>](#specific-type)
* [<span data-ttu-id="f6775-111">IActionResult</span><span class="sxs-lookup"><span data-stu-id="f6775-111">IActionResult</span></span>](#iactionresult-type)

::: moniker-end

<span data-ttu-id="f6775-112">Este documento explica quando é mais adequado usar cada tipo de retorno.</span><span class="sxs-lookup"><span data-stu-id="f6775-112">This document explains when it's most appropriate to use each return type.</span></span>

## <a name="specific-type"></a><span data-ttu-id="f6775-113">Tipo específico</span><span class="sxs-lookup"><span data-stu-id="f6775-113">Specific type</span></span>

<span data-ttu-id="f6775-114">A ação mais simples retorna um tipo de dados complexo ou primitivo (por exemplo, `string` ou um tipo de objeto personalizado).</span><span class="sxs-lookup"><span data-stu-id="f6775-114">The simplest action returns a primitive or complex data type (for example, `string` or a custom object type).</span></span> <span data-ttu-id="f6775-115">Considere a seguinte ação, que retorna uma coleção de objetos `Product` personalizados:</span><span class="sxs-lookup"><span data-stu-id="f6775-115">Consider the following action, which returns a collection of custom `Product` objects:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_Get)]

<span data-ttu-id="f6775-116">Sem condições conhecidas contra as quais se proteger durante a execução da ação, retornar um tipo específico pode ser suficiente.</span><span class="sxs-lookup"><span data-stu-id="f6775-116">Without known conditions to safeguard against during action execution, returning a specific type could suffice.</span></span> <span data-ttu-id="f6775-117">A ação anterior não aceita parâmetros, assim, validação de restrições de parâmetro não é necessária.</span><span class="sxs-lookup"><span data-stu-id="f6775-117">The preceding action accepts no parameters, so parameter constraints validation isn't needed.</span></span>

<span data-ttu-id="f6775-118">Quando condições conhecidas precisarem ser incluídas em uma ação, vários caminhos de retorno serão introduzidos.</span><span class="sxs-lookup"><span data-stu-id="f6775-118">When known conditions need to be accounted for in an action, multiple return paths are introduced.</span></span> <span data-ttu-id="f6775-119">Nesse caso, é comum combinar um tipo de retorno [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) com o tipo de retorno primitivo ou complexo.</span><span class="sxs-lookup"><span data-stu-id="f6775-119">In such a case, it's common to mix an [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) return type with the primitive or complex return type.</span></span> <span data-ttu-id="f6775-120">[IActionResult](#iactionresult-type) ou [ActionResult\<T >](#actionresultt-type) é necessário para acomodar esse tipo de ação.</span><span class="sxs-lookup"><span data-stu-id="f6775-120">Either [IActionResult](#iactionresult-type) or [ActionResult\<T>](#actionresultt-type) are necessary to accommodate this type of action.</span></span>

## <a name="iactionresult-type"></a><span data-ttu-id="f6775-121">Tipo IActionResult</span><span class="sxs-lookup"><span data-stu-id="f6775-121">IActionResult type</span></span>

<span data-ttu-id="f6775-122">O tipo de retorno [IActionResult](/dotnet/api/microsoft.aspnetcore.mvc.iactionresult) é apropriado quando vários tipos de retorno [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) são possíveis em uma ação.</span><span class="sxs-lookup"><span data-stu-id="f6775-122">The [IActionResult](/dotnet/api/microsoft.aspnetcore.mvc.iactionresult) return type is appropriate when multiple [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) return types are possible in an action.</span></span> <span data-ttu-id="f6775-123">Os tipos `ActionResult` representam vários códigos de status HTTP.</span><span class="sxs-lookup"><span data-stu-id="f6775-123">The `ActionResult` types represent various HTTP status codes.</span></span> <span data-ttu-id="f6775-124">Alguns tipos de retorno comuns que se enquadram nessa categoria são [BadRequestResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestresult) (400) [NotFoundResult](/dotnet/api/microsoft.aspnetcore.mvc.notfoundresult) (404) e [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200).</span><span class="sxs-lookup"><span data-stu-id="f6775-124">Some common return types falling into this category are [BadRequestResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestresult) (400), [NotFoundResult](/dotnet/api/microsoft.aspnetcore.mvc.notfoundresult) (404), and [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200).</span></span>

<span data-ttu-id="f6775-125">Porque há vários tipos de retorno e caminhos na ação, o uso liberal do atributo [[ProducesResponseType]](/dotnet/api/microsoft.aspnetcore.mvc.producesresponsetypeattribute.-ctor) é necessário.</span><span class="sxs-lookup"><span data-stu-id="f6775-125">Because there are multiple return types and paths in the action, liberal use of the [[ProducesResponseType]](/dotnet/api/microsoft.aspnetcore.mvc.producesresponsetypeattribute.-ctor) attribute is necessary.</span></span> <span data-ttu-id="f6775-126">Esse atributo produz detalhes de resposta mais descritivos para páginas de ajuda da API geradas por ferramentas como [Swagger](/aspnet/core/tutorials/web-api-help-pages-using-swagger).</span><span class="sxs-lookup"><span data-stu-id="f6775-126">This attribute produces more descriptive response details for API help pages generated by tools like [Swagger](/aspnet/core/tutorials/web-api-help-pages-using-swagger).</span></span> <span data-ttu-id="f6775-127">`[ProducesResponseType]` indica os tipos conhecidos e os códigos de status HTTP a serem retornados pela ação.</span><span class="sxs-lookup"><span data-stu-id="f6775-127">`[ProducesResponseType]` indicates the known types and HTTP status codes to be returned by the action.</span></span>

### <a name="synchronous-action"></a><span data-ttu-id="f6775-128">Ação síncrona</span><span class="sxs-lookup"><span data-stu-id="f6775-128">Synchronous action</span></span>

<span data-ttu-id="f6775-129">Considere a seguinte ação síncrona em que há dois tipos de retorno possíveis:</span><span class="sxs-lookup"><span data-stu-id="f6775-129">Consider the following synchronous action in which there are two possible return types:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_GetById&highlight=8,11)]

<span data-ttu-id="f6775-130">Na ação anterior, um código de status 404 é retornado quando o produto representado por `id` não existe no armazenamento de dados subjacente.</span><span class="sxs-lookup"><span data-stu-id="f6775-130">In the preceding action, a 404 status code is returned when the product represented by `id` doesn't exist in the underlying data store.</span></span> <span data-ttu-id="f6775-131">O método auxiliar [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) é invocado como um atalho para `return new NotFoundResult();`.</span><span class="sxs-lookup"><span data-stu-id="f6775-131">The [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) helper method is invoked as a shortcut to `return new NotFoundResult();`.</span></span> <span data-ttu-id="f6775-132">Se o produto existir, um objeto `Product` que representa o conteúdo será retornado com um código de status 200.</span><span class="sxs-lookup"><span data-stu-id="f6775-132">If the product does exist, a `Product` object representing the payload is returned with a 200 status code.</span></span> <span data-ttu-id="f6775-133">O método auxiliar [OK](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.ok) é invocado como a forma abreviada de `return new OkObjectResult(product);`.</span><span class="sxs-lookup"><span data-stu-id="f6775-133">The [Ok](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.ok) helper method is invoked as the shorthand form of `return new OkObjectResult(product);`.</span></span>

### <a name="asynchronous-action"></a><span data-ttu-id="f6775-134">Ação assíncrona</span><span class="sxs-lookup"><span data-stu-id="f6775-134">Asynchronous action</span></span>

<span data-ttu-id="f6775-135">Considere a seguinte ação assíncrona em que há dois tipos de retorno possíveis:</span><span class="sxs-lookup"><span data-stu-id="f6775-135">Consider the following asynchronous action in which there are two possible return types:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_CreateAsync&highlight=8,13)]

<span data-ttu-id="f6775-136">No código anterior:</span><span class="sxs-lookup"><span data-stu-id="f6775-136">In the preceding code:</span></span>

* <span data-ttu-id="f6775-137">O tempo de execução do ASP.NET Core retorna um código de status 400 ([BadRequest](xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*)) quando a descrição do produto contém "Widget XYZ".</span><span class="sxs-lookup"><span data-stu-id="f6775-137">A 400 status code ([BadRequest](xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*)) is returned by the ASP.NET Core runtime when the product description contains "XYZ Widget".</span></span>
* <span data-ttu-id="f6775-138">O método [CreatedAtAction](xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*) gera um código de status 201 quando um produto é criado.</span><span class="sxs-lookup"><span data-stu-id="f6775-138">A 201 status code is generated by the [CreatedAtAction](xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*) method when a product is created.</span></span> <span data-ttu-id="f6775-139">Nesse caminho de código, o objeto `Product` é retornado.</span><span class="sxs-lookup"><span data-stu-id="f6775-139">In this code path, the `Product` object is returned.</span></span>

<span data-ttu-id="f6775-140">Por exemplo, o modelo a seguir indica que as solicitações devem incluir as propriedades `Name` e `Description`.</span><span class="sxs-lookup"><span data-stu-id="f6775-140">For example, the following model indicates that requests must include the `Name` and `Description` properties.</span></span> <span data-ttu-id="f6775-141">Portanto, a falha em fornecer `Name` e `Description` na solicitação causa falha na validação do modelo.</span><span class="sxs-lookup"><span data-stu-id="f6775-141">Therefore, failure to provide `Name` and `Description` in the request causes model validation to fail.</span></span>

[!code-csharp[](../web-api/action-return-types/samples/WebApiSample.DataAccess/Models/Product.cs?name=snippet_ProductClass&highlight=5-6,8-9)]

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="f6775-142">Se o atributo [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) no ASP.NET Core 2.1 ou posterior for aplicado, erros de validação de modelo resultarão em um código de status 400.</span><span class="sxs-lookup"><span data-stu-id="f6775-142">If the [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute in ASP.NET Core 2.1 or later  is applied, model validation errors result in a 400 status code.</span></span> <span data-ttu-id="f6775-143">Para obter mais informações, veja [Respostas automáticas HTTP 400](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="f6775-143">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="actionresultt-type"></a><span data-ttu-id="f6775-144">Tipo ActionResult\<T></span><span class="sxs-lookup"><span data-stu-id="f6775-144">ActionResult\<T> type</span></span>

<span data-ttu-id="f6775-145">O ASP.NET Core 2.1 apresenta o tipo de retorno [ActionResult\<T >](/dotnet/api/microsoft.aspnetcore.mvc.actionresult-1) para ações do controlador de API Web.</span><span class="sxs-lookup"><span data-stu-id="f6775-145">ASP.NET Core 2.1 introduces the [ActionResult\<T>](/dotnet/api/microsoft.aspnetcore.mvc.actionresult-1) return type for Web API controller actions.</span></span> <span data-ttu-id="f6775-146">Permite que você retorne um tipo derivado de [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) ou retorne um [tipo específico](#specific-type).</span><span class="sxs-lookup"><span data-stu-id="f6775-146">It enables you to return a type deriving from [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) or return a [specific type](#specific-type).</span></span> <span data-ttu-id="f6775-147">`ActionResult<T>` oferece os seguintes benefícios em relação ao [tipo IActionResult](#iactionresult-type):</span><span class="sxs-lookup"><span data-stu-id="f6775-147">`ActionResult<T>` offers the following benefits over the [IActionResult type](#iactionresult-type):</span></span>

* <span data-ttu-id="f6775-148">O atributo [[ProducesResponseType]](/dotnet/api/microsoft.aspnetcore.mvc.producesresponsetypeattribute) pode ter sua propriedade `Type` excluída.</span><span class="sxs-lookup"><span data-stu-id="f6775-148">The [[ProducesResponseType]](/dotnet/api/microsoft.aspnetcore.mvc.producesresponsetypeattribute) attribute's `Type` property can be excluded.</span></span> <span data-ttu-id="f6775-149">Por exemplo, `[ProducesResponseType(200, Type = typeof(Product))]` é simplificado para `[ProducesResponseType(200)]`.</span><span class="sxs-lookup"><span data-stu-id="f6775-149">For example, `[ProducesResponseType(200, Type = typeof(Product))]` is simplified to `[ProducesResponseType(200)]`.</span></span> <span data-ttu-id="f6775-150">O tipo de retorno esperado da ação é inferido do `T` em `ActionResult<T>`.</span><span class="sxs-lookup"><span data-stu-id="f6775-150">The action's expected return type is instead inferred from the `T` in `ActionResult<T>`.</span></span>
* <span data-ttu-id="f6775-151">[Operadores de conversão implícita](/dotnet/csharp/language-reference/keywords/implicit) são compatíveis com a conversão de `T` e `ActionResult` em `ActionResult<T>`.</span><span class="sxs-lookup"><span data-stu-id="f6775-151">[Implicit cast operators](/dotnet/csharp/language-reference/keywords/implicit) support the conversion of both `T` and `ActionResult` to `ActionResult<T>`.</span></span> <span data-ttu-id="f6775-152">`T` converte em [ObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.objectresult), o que significa que `return new ObjectResult(T);` é simplificado para `return T;`.</span><span class="sxs-lookup"><span data-stu-id="f6775-152">`T` converts to [ObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.objectresult), which means `return new ObjectResult(T);` is simplified to `return T;`.</span></span>

<span data-ttu-id="f6775-153">C# não dá suporte a operadores de conversão implícita em interfaces.</span><span class="sxs-lookup"><span data-stu-id="f6775-153">C# doesn't support implicit cast operators on interfaces.</span></span> <span data-ttu-id="f6775-154">Consequentemente, a conversão da interface para um tipo concreto é necessário para usar `ActionResult<T>`.</span><span class="sxs-lookup"><span data-stu-id="f6775-154">Consequently, conversion of the interface to a concrete type is necessary to use `ActionResult<T>`.</span></span> <span data-ttu-id="f6775-155">Por exemplo, o uso de `IEnumerable` no exemplo a seguir não funciona:</span><span class="sxs-lookup"><span data-stu-id="f6775-155">For example, use of `IEnumerable` in the following example doesn't work:</span></span>

```csharp
[HttpGet]
public ActionResult<IEnumerable<Product>> Get()
{
    return _repository.GetProducts();
}
```

<span data-ttu-id="f6775-156">Uma opção para corrigir o código anterior é retornar a `_repository.GetProducts().ToList();`.</span><span class="sxs-lookup"><span data-stu-id="f6775-156">One option to fix the preceding code is to return `_repository.GetProducts().ToList();`.</span></span>

<span data-ttu-id="f6775-157">A maioria das ações tem um tipo de retorno específico.</span><span class="sxs-lookup"><span data-stu-id="f6775-157">Most actions have a specific return type.</span></span> <span data-ttu-id="f6775-158">Condições inesperadas podem ocorrer durante a execução da ação, caso em que o tipo específico não é retornado.</span><span class="sxs-lookup"><span data-stu-id="f6775-158">Unexpected conditions can occur during action execution, in which case the specific type isn't returned.</span></span> <span data-ttu-id="f6775-159">Por exemplo, o parâmetro de entrada de uma ação pode falhar na validação do modelo.</span><span class="sxs-lookup"><span data-stu-id="f6775-159">For example, an action's input parameter may fail model validation.</span></span> <span data-ttu-id="f6775-160">Nesse caso, é comum retornar o tipo `ActionResult` adequado, em vez do tipo específico.</span><span class="sxs-lookup"><span data-stu-id="f6775-160">In such a case, it's common to return the appropriate `ActionResult` type instead of the specific type.</span></span>

### <a name="synchronous-action"></a><span data-ttu-id="f6775-161">Ação síncrona</span><span class="sxs-lookup"><span data-stu-id="f6775-161">Synchronous action</span></span>

<span data-ttu-id="f6775-162">Considere uma ação síncrona em que há dois tipos de retorno possíveis:</span><span class="sxs-lookup"><span data-stu-id="f6775-162">Consider a synchronous action in which there are two possible return types:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetById&highlight=7,10)]

<span data-ttu-id="f6775-163">No código anterior, um código de status 404 é retornado quando o produto não existe no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="f6775-163">In the preceding code, a 404 status code is returned when the product doesn't exist in the database.</span></span> <span data-ttu-id="f6775-164">Se o produto existir, o objeto `Product` correspondente será retornado.</span><span class="sxs-lookup"><span data-stu-id="f6775-164">If the product does exist, the corresponding `Product` object is returned.</span></span> <span data-ttu-id="f6775-165">Antes do ASP.NET Core 2.1, a linha `return product;` teria sido `return Ok(product);`.</span><span class="sxs-lookup"><span data-stu-id="f6775-165">Before ASP.NET Core 2.1, the `return product;` line would have been `return Ok(product);`.</span></span>

> [!TIP]
> <span data-ttu-id="f6775-166">Do ASP.NET Core 2.1 em diante, a inferência de origem de associação de parâmetro de ação é habilitada quando uma classe de controlador é decorada com o atributo `[ApiController]`.</span><span class="sxs-lookup"><span data-stu-id="f6775-166">As of ASP.NET Core 2.1, action parameter binding source inference is enabled when a controller class is decorated with the `[ApiController]` attribute.</span></span> <span data-ttu-id="f6775-167">Um nome de parâmetro correspondente a um nome do modelo de rota é associado automaticamente usando os dados de rota de solicitação.</span><span class="sxs-lookup"><span data-stu-id="f6775-167">A parameter name matching a name in the route template is automatically bound using the request route data.</span></span> <span data-ttu-id="f6775-168">Consequentemente, o parâmetro `id` da ação anterior não é explicitamente anotado com o atributo [[FromRoute]](/dotnet/api/microsoft.aspnetcore.mvc.fromrouteattribute).</span><span class="sxs-lookup"><span data-stu-id="f6775-168">Consequently, the preceding action's `id` parameter isn't explicitly annotated with the [[FromRoute]](/dotnet/api/microsoft.aspnetcore.mvc.fromrouteattribute) attribute.</span></span>

### <a name="asynchronous-action"></a><span data-ttu-id="f6775-169">Ação assíncrona</span><span class="sxs-lookup"><span data-stu-id="f6775-169">Asynchronous action</span></span>

<span data-ttu-id="f6775-170">Considere uma ação assíncrona em que há dois tipos de retorno possíveis:</span><span class="sxs-lookup"><span data-stu-id="f6775-170">Consider an asynchronous action in which there are two possible return types:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsync&highlight=8,13)]

<span data-ttu-id="f6775-171">No código anterior:</span><span class="sxs-lookup"><span data-stu-id="f6775-171">In the preceding code:</span></span>

* <span data-ttu-id="f6775-172">O tempo de execução do ASP.NET Core retorna um código de status 400 ([BadRequest](xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*)) quando:</span><span class="sxs-lookup"><span data-stu-id="f6775-172">A 400 status code ([BadRequest](xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*)) is returned by the ASP.NET Core runtime when:</span></span>
  * <span data-ttu-id="f6775-173">O atributo [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) tiver sido aplicado e o modelo de validação falhar.</span><span class="sxs-lookup"><span data-stu-id="f6775-173">The [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute has been applied and model validation fails.</span></span>
  * <span data-ttu-id="f6775-174">A descrição do produto contém "Widget XYZ".</span><span class="sxs-lookup"><span data-stu-id="f6775-174">The product description contains "XYZ Widget".</span></span>
* <span data-ttu-id="f6775-175">O método [CreatedAtAction](xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*) gera um código de status 201 quando um produto é criado.</span><span class="sxs-lookup"><span data-stu-id="f6775-175">A 201 status code is generated by the [CreatedAtAction](xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*) method when a product is created.</span></span> <span data-ttu-id="f6775-176">Nesse caminho de código, o objeto `Product` é retornado.</span><span class="sxs-lookup"><span data-stu-id="f6775-176">In this code path, the `Product` object is returned.</span></span>

> [!TIP]
> <span data-ttu-id="f6775-177">Do ASP.NET Core 2.1 em diante, a inferência de origem de associação de parâmetro de ação é habilitada quando uma classe de controlador é decorada com o atributo `[ApiController]`.</span><span class="sxs-lookup"><span data-stu-id="f6775-177">As of ASP.NET Core 2.1, action parameter binding source inference is enabled when a controller class is decorated with the `[ApiController]` attribute.</span></span> <span data-ttu-id="f6775-178">Parâmetros de tipo complexo são vinculados automaticamente usando o corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="f6775-178">Complex type parameters are automatically bound using the request body.</span></span> <span data-ttu-id="f6775-179">Consequentemente, o parâmetro `product` da ação anterior não é explicitamente anotado com o atributo [[FromBody]](/dotnet/api/microsoft.aspnetcore.mvc.frombodyattribute).</span><span class="sxs-lookup"><span data-stu-id="f6775-179">Consequently, the preceding action's `product` parameter isn't explicitly annotated with the [[FromBody]](/dotnet/api/microsoft.aspnetcore.mvc.frombodyattribute) attribute.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="f6775-180">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="f6775-180">Additional resources</span></span>

* <xref:mvc/controllers/actions>
* <xref:mvc/models/validation>
* <xref:tutorials/web-api-help-pages-using-swagger>
