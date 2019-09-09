---
title: Tipos de retorno de ação do controlador em ASP.NET Core API Web
author: scottaddie
description: Saiba como usar os vários tipos de retorno do método de ação do controlador em uma API da Web do ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 09/09/2019
uid: web-api/action-return-types
ms.openlocfilehash: 79134ab252f309f8b39b8db5f8f3e82035e0eb7f
ms.sourcegitcommit: 2d4c1732c4866ed26b83da35f7bc2ad021a9c701
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/09/2019
ms.locfileid: "70815757"
---
# <a name="controller-action-return-types-in-aspnet-core-web-api"></a><span data-ttu-id="9b5ec-103">Tipos de retorno de ação do controlador em ASP.NET Core API Web</span><span class="sxs-lookup"><span data-stu-id="9b5ec-103">Controller action return types in ASP.NET Core web API</span></span>

<span data-ttu-id="9b5ec-104">Por [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="9b5ec-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="9b5ec-105">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/action-return-types/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9b5ec-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/action-return-types/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="9b5ec-106">O ASP.NET Core oferece as seguintes opções para tipos de retorno de ação do controlador da API Web:</span><span class="sxs-lookup"><span data-stu-id="9b5ec-106">ASP.NET Core offers the following options for web API controller action return types:</span></span>

::: moniker range=">= aspnetcore-2.1"

* [<span data-ttu-id="9b5ec-107">Tipo específico</span><span class="sxs-lookup"><span data-stu-id="9b5ec-107">Specific type</span></span>](#specific-type)
* [<span data-ttu-id="9b5ec-108">IActionResult</span><span class="sxs-lookup"><span data-stu-id="9b5ec-108">IActionResult</span></span>](#iactionresult-type)
* [<span data-ttu-id="9b5ec-109">ActionResult\<T></span><span class="sxs-lookup"><span data-stu-id="9b5ec-109">ActionResult\<T></span></span>](#actionresultt-type)

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* [<span data-ttu-id="9b5ec-110">Tipo específico</span><span class="sxs-lookup"><span data-stu-id="9b5ec-110">Specific type</span></span>](#specific-type)
* [<span data-ttu-id="9b5ec-111">IActionResult</span><span class="sxs-lookup"><span data-stu-id="9b5ec-111">IActionResult</span></span>](#iactionresult-type)

::: moniker-end

<span data-ttu-id="9b5ec-112">Este documento explica quando é mais adequado usar cada tipo de retorno.</span><span class="sxs-lookup"><span data-stu-id="9b5ec-112">This document explains when it's most appropriate to use each return type.</span></span>

## <a name="specific-type"></a><span data-ttu-id="9b5ec-113">Tipo específico</span><span class="sxs-lookup"><span data-stu-id="9b5ec-113">Specific type</span></span>

<span data-ttu-id="9b5ec-114">A ação mais simples retorna um tipo de dados complexo ou primitivo (por exemplo, `string` ou um tipo de objeto personalizado).</span><span class="sxs-lookup"><span data-stu-id="9b5ec-114">The simplest action returns a primitive or complex data type (for example, `string` or a custom object type).</span></span> <span data-ttu-id="9b5ec-115">Considere a seguinte ação, que retorna uma coleção de objetos `Product` personalizados:</span><span class="sxs-lookup"><span data-stu-id="9b5ec-115">Consider the following action, which returns a collection of custom `Product` objects:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_Get)]

<span data-ttu-id="9b5ec-116">Sem condições conhecidas contra as quais se proteger durante a execução da ação, retornar um tipo específico pode ser suficiente.</span><span class="sxs-lookup"><span data-stu-id="9b5ec-116">Without known conditions to safeguard against during action execution, returning a specific type could suffice.</span></span> <span data-ttu-id="9b5ec-117">A ação anterior não aceita parâmetros, assim, validação de restrições de parâmetro não é necessária.</span><span class="sxs-lookup"><span data-stu-id="9b5ec-117">The preceding action accepts no parameters, so parameter constraints validation isn't needed.</span></span>

<span data-ttu-id="9b5ec-118">Quando condições conhecidas precisarem ser incluídas em uma ação, vários caminhos de retorno serão introduzidos.</span><span class="sxs-lookup"><span data-stu-id="9b5ec-118">When known conditions need to be accounted for in an action, multiple return paths are introduced.</span></span> <span data-ttu-id="9b5ec-119">Nesse caso, é comum misturar um <xref:Microsoft.AspNetCore.Mvc.ActionResult> tipo de retorno com o tipo de retorno primitivo ou complexo.</span><span class="sxs-lookup"><span data-stu-id="9b5ec-119">In such a case, it's common to mix an <xref:Microsoft.AspNetCore.Mvc.ActionResult> return type with the primitive or complex return type.</span></span> <span data-ttu-id="9b5ec-120">[IActionResult](#iactionresult-type) ou [ActionResult\<T >](#actionresultt-type) é necessário para acomodar esse tipo de ação.</span><span class="sxs-lookup"><span data-stu-id="9b5ec-120">Either [IActionResult](#iactionresult-type) or [ActionResult\<T>](#actionresultt-type) are necessary to accommodate this type of action.</span></span>

### <a name="return-ienumerablet-or-iasyncenumerablet"></a><span data-ttu-id="9b5ec-121">Retornar IEnumerable\<T > ou IAsyncEnumerable\<t ></span><span class="sxs-lookup"><span data-stu-id="9b5ec-121">Return IEnumerable\<T> or IAsyncEnumerable\<T></span></span>

<span data-ttu-id="9b5ec-122">No ASP.NET Core 2,2 e anteriores, retornar <xref:System.Collections.Generic.IAsyncEnumerable%601> de uma ação resulta em uma iteração de coleção síncrona pelo serializador.</span><span class="sxs-lookup"><span data-stu-id="9b5ec-122">In ASP.NET Core 2.2 and earlier, returning <xref:System.Collections.Generic.IAsyncEnumerable%601> from an action results in synchronous collection iteration by the serializer.</span></span> <span data-ttu-id="9b5ec-123">O resultado é o bloqueio de chamadas e um potencial para a privação do pool de threads.</span><span class="sxs-lookup"><span data-stu-id="9b5ec-123">The result is the blocking of calls and a potential for thread pool starvation.</span></span> <span data-ttu-id="9b5ec-124">Para ilustrar, imagine que o núcleo do Entity Framework (EF) está sendo usado para as necessidades de acesso a dados da API Web.</span><span class="sxs-lookup"><span data-stu-id="9b5ec-124">To illustrate, imagine that Entity Framework (EF) Core is being used for the web API's data access needs.</span></span> <span data-ttu-id="9b5ec-125">O tipo de retorno da ação a seguir é enumerado de forma síncrona durante a serialização:</span><span class="sxs-lookup"><span data-stu-id="9b5ec-125">The following action's return type is synchronously enumerated during serialization:</span></span>

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

<span data-ttu-id="9b5ec-126">Para evitar a enumeração síncrona e as esperas de bloqueio no banco de dados no ASP.NET Core 2,2 `ToListAsync`e anteriores, invoque:</span><span class="sxs-lookup"><span data-stu-id="9b5ec-126">To avoid synchronous enumeration and blocking waits on the database in ASP.NET Core 2.2 and earlier, invoke `ToListAsync`:</span></span>

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale).ToListAsync();
```

<span data-ttu-id="9b5ec-127">No ASP.NET Core 3,0 e posterior, retornando `IAsyncEnumerable<T>` de uma ação:</span><span class="sxs-lookup"><span data-stu-id="9b5ec-127">In ASP.NET Core 3.0 and later, returning `IAsyncEnumerable<T>` from an action:</span></span>

* <span data-ttu-id="9b5ec-128">Não resulta mais em iteração síncrona.</span><span class="sxs-lookup"><span data-stu-id="9b5ec-128">No longer results in synchronous iteration.</span></span>
* <span data-ttu-id="9b5ec-129">Torna-se tão eficiente <xref:System.Collections.Generic.IEnumerable%601>quanto retornar.</span><span class="sxs-lookup"><span data-stu-id="9b5ec-129">Becomes as efficient as returning <xref:System.Collections.Generic.IEnumerable%601>.</span></span>

<span data-ttu-id="9b5ec-130">ASP.NET Core 3,0 e posterior armazena em buffer o resultado da seguinte ação antes de fornecer ao serializador:</span><span class="sxs-lookup"><span data-stu-id="9b5ec-130">ASP.NET Core 3.0 and later buffers the result of the following action before providing it to the serializer:</span></span>

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

<span data-ttu-id="9b5ec-131">Considere declarar o tipo de retorno da assinatura de `IAsyncEnumerable<T>` ação como para garantir a iteração assíncrona.</span><span class="sxs-lookup"><span data-stu-id="9b5ec-131">Consider declaring the action signature's return type as `IAsyncEnumerable<T>` to guarantee the asynchronous iteration.</span></span> <span data-ttu-id="9b5ec-132">Por fim, o modo de iteração é baseado no tipo concreto subjacente que está sendo retornado.</span><span class="sxs-lookup"><span data-stu-id="9b5ec-132">Ultimately, the iteration mode is based on the underlying concrete type being returned.</span></span> <span data-ttu-id="9b5ec-133">O MVC armazena em buffer automaticamente qualquer tipo concreto `IAsyncEnumerable<T>`que implemente.</span><span class="sxs-lookup"><span data-stu-id="9b5ec-133">MVC automatically buffers any concrete type that implements `IAsyncEnumerable<T>`.</span></span>

<span data-ttu-id="9b5ec-134">Considere a seguinte ação, que retorna registros de produtos com preço de venda `IEnumerable<Product>`como:</span><span class="sxs-lookup"><span data-stu-id="9b5ec-134">Consider the following action, which returns sale-priced product records as `IEnumerable<Product>`:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.30/Controllers/ProductsController.cs?name=snippet_GetOnSaleProducts)]

<span data-ttu-id="9b5ec-135">O `IAsyncEnumerable<Product>` equivalente da ação anterior é:</span><span class="sxs-lookup"><span data-stu-id="9b5ec-135">The `IAsyncEnumerable<Product>` equivalent of the preceding action is:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.30/Controllers/ProductsController.cs?name=snippet_GetOnSaleProductsAsync)]

<span data-ttu-id="9b5ec-136">As duas ações anteriores não são bloqueadas a partir do ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="9b5ec-136">Both of the preceding actions are non-blocking as of ASP.NET Core 3.0.</span></span>

## <a name="iactionresult-type"></a><span data-ttu-id="9b5ec-137">Tipo IActionResult</span><span class="sxs-lookup"><span data-stu-id="9b5ec-137">IActionResult type</span></span>

<span data-ttu-id="9b5ec-138">O <xref:Microsoft.AspNetCore.Mvc.IActionResult> tipo de retorno é apropriado quando `ActionResult` vários tipos de retorno são possíveis em uma ação.</span><span class="sxs-lookup"><span data-stu-id="9b5ec-138">The <xref:Microsoft.AspNetCore.Mvc.IActionResult> return type is appropriate when multiple `ActionResult` return types are possible in an action.</span></span> <span data-ttu-id="9b5ec-139">Os tipos `ActionResult` representam vários códigos de status HTTP.</span><span class="sxs-lookup"><span data-stu-id="9b5ec-139">The `ActionResult` types represent various HTTP status codes.</span></span> <span data-ttu-id="9b5ec-140">Qualquer classe não abstrata derivada de `ActionResult` é qualificada como um tipo de retorno válido.</span><span class="sxs-lookup"><span data-stu-id="9b5ec-140">Any non-abstract class deriving from `ActionResult` qualifies as a valid return type.</span></span> <span data-ttu-id="9b5ec-141">Alguns tipos de retorno comuns nessa categoria são <xref:Microsoft.AspNetCore.Mvc.BadRequestResult> (400), <xref:Microsoft.AspNetCore.Mvc.NotFoundResult> (404) e <xref:Microsoft.AspNetCore.Mvc.OkObjectResult> (200).</span><span class="sxs-lookup"><span data-stu-id="9b5ec-141">Some common return types in this category are <xref:Microsoft.AspNetCore.Mvc.BadRequestResult> (400), <xref:Microsoft.AspNetCore.Mvc.NotFoundResult> (404), and <xref:Microsoft.AspNetCore.Mvc.OkObjectResult> (200).</span></span> <span data-ttu-id="9b5ec-142">Como alternativa, os <xref:Microsoft.AspNetCore.Mvc.ControllerBase> métodos de conveniência na classe podem ser usados para retornar `ActionResult` tipos de uma ação.</span><span class="sxs-lookup"><span data-stu-id="9b5ec-142">Alternatively, convenience methods in the <xref:Microsoft.AspNetCore.Mvc.ControllerBase> class can be used to return `ActionResult` types from an action.</span></span> <span data-ttu-id="9b5ec-143">Por exemplo, `return BadRequest();` é uma forma abreviada `return new BadRequestResult();`de.</span><span class="sxs-lookup"><span data-stu-id="9b5ec-143">For example, `return BadRequest();` is a shorthand form of `return new BadRequestResult();`.</span></span>

<span data-ttu-id="9b5ec-144">Como há vários tipos de retorno e caminhos nesse tipo de ação, liberal uso do atributo [[ProducesResponseType]](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) é necessário.</span><span class="sxs-lookup"><span data-stu-id="9b5ec-144">Because there are multiple return types and paths in this type of action, liberal use of the [[ProducesResponseType]](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) attribute is necessary.</span></span> <span data-ttu-id="9b5ec-145">Esse atributo produz detalhes de resposta mais descritivos para páginas de ajuda da API Web geradas por ferramentas como o [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span><span class="sxs-lookup"><span data-stu-id="9b5ec-145">This attribute produces more descriptive response details for web API help pages generated by tools like [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span> <span data-ttu-id="9b5ec-146">`[ProducesResponseType]` indica os tipos conhecidos e os códigos de status HTTP a serem retornados pela ação.</span><span class="sxs-lookup"><span data-stu-id="9b5ec-146">`[ProducesResponseType]` indicates the known types and HTTP status codes to be returned by the action.</span></span>

### <a name="synchronous-action"></a><span data-ttu-id="9b5ec-147">Ação síncrona</span><span class="sxs-lookup"><span data-stu-id="9b5ec-147">Synchronous action</span></span>

<span data-ttu-id="9b5ec-148">Considere a seguinte ação síncrona em que há dois tipos de retorno possíveis:</span><span class="sxs-lookup"><span data-stu-id="9b5ec-148">Consider the following synchronous action in which there are two possible return types:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetById&highlight=8,11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_GetById&highlight=8,11)]

::: moniker-end

<span data-ttu-id="9b5ec-149">Na ação anterior:</span><span class="sxs-lookup"><span data-stu-id="9b5ec-149">In the preceding action:</span></span>

* <span data-ttu-id="9b5ec-150">Um código de status 404 é retornado quando o produto representado `id` pelo não existe no armazenamento de dados subjacente.</span><span class="sxs-lookup"><span data-stu-id="9b5ec-150">A 404 status code is returned when the product represented by `id` doesn't exist in the underlying data store.</span></span> <span data-ttu-id="9b5ec-151">O <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> método de conveniência é invocado como `return new NotFoundResult();`abreviação para.</span><span class="sxs-lookup"><span data-stu-id="9b5ec-151">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> convenience method is invoked as shorthand for `return new NotFoundResult();`.</span></span>
* <span data-ttu-id="9b5ec-152">Um código de status 200 é retornado com `Product` o objeto quando o produto existe.</span><span class="sxs-lookup"><span data-stu-id="9b5ec-152">A 200 status code is returned with the `Product` object when the product does exist.</span></span> <span data-ttu-id="9b5ec-153">O <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> método de conveniência é invocado como `return new OkObjectResult(product);`abreviação para.</span><span class="sxs-lookup"><span data-stu-id="9b5ec-153">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> convenience method is invoked as shorthand for `return new OkObjectResult(product);`.</span></span>

### <a name="asynchronous-action"></a><span data-ttu-id="9b5ec-154">Ação assíncrona</span><span class="sxs-lookup"><span data-stu-id="9b5ec-154">Asynchronous action</span></span>

<span data-ttu-id="9b5ec-155">Considere a seguinte ação assíncrona em que há dois tipos de retorno possíveis:</span><span class="sxs-lookup"><span data-stu-id="9b5ec-155">Consider the following asynchronous action in which there are two possible return types:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsync&highlight=8,13)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_CreateAsync&highlight=8,13)]

::: moniker-end

<span data-ttu-id="9b5ec-156">Na ação anterior:</span><span class="sxs-lookup"><span data-stu-id="9b5ec-156">In the preceding action:</span></span>

* <span data-ttu-id="9b5ec-157">Um código de status 400 é retornado quando a descrição do produto contém "widget XYZ".</span><span class="sxs-lookup"><span data-stu-id="9b5ec-157">A 400 status code is returned when the product description contains "XYZ Widget".</span></span> <span data-ttu-id="9b5ec-158">O <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> método de conveniência é invocado como `return new BadRequestResult();`abreviação para.</span><span class="sxs-lookup"><span data-stu-id="9b5ec-158">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> convenience method is invoked as shorthand for `return new BadRequestResult();`.</span></span>
* <span data-ttu-id="9b5ec-159">Um código de status 201 é gerado pelo <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> método de conveniência quando um produto é criado.</span><span class="sxs-lookup"><span data-stu-id="9b5ec-159">A 201 status code is generated by the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> convenience method when a product is created.</span></span> <span data-ttu-id="9b5ec-160">Uma alternativa para chamar `CreatedAtAction` é `return new CreatedAtActionResult(nameof(GetById), "Products", new { id = product.Id }, product);`.</span><span class="sxs-lookup"><span data-stu-id="9b5ec-160">An alternative to calling `CreatedAtAction` is `return new CreatedAtActionResult(nameof(GetById), "Products", new { id = product.Id }, product);`.</span></span> <span data-ttu-id="9b5ec-161">Nesse caminho de código, o `Product` objeto é fornecido no corpo da resposta.</span><span class="sxs-lookup"><span data-stu-id="9b5ec-161">In this code path, the `Product` object is provided in the response body.</span></span> <span data-ttu-id="9b5ec-162">É `Location` fornecido um cabeçalho de resposta contendo a URL do produto recém-criado.</span><span class="sxs-lookup"><span data-stu-id="9b5ec-162">A `Location` response header containing the newly created product's URL is provided.</span></span>

<span data-ttu-id="9b5ec-163">Por exemplo, o modelo a seguir indica que as solicitações devem incluir as propriedades `Name` e `Description`.</span><span class="sxs-lookup"><span data-stu-id="9b5ec-163">For example, the following model indicates that requests must include the `Name` and `Description` properties.</span></span> <span data-ttu-id="9b5ec-164">A falha em `Name` fornecer `Description` e na solicitação faz com que a validação do modelo falhe.</span><span class="sxs-lookup"><span data-stu-id="9b5ec-164">Failure to provide `Name` and `Description` in the request causes model validation to fail.</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.DataAccess/Models/Product.cs?name=snippet_ProductClass&highlight=5-6,8-9)]

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="9b5ec-165">Se o atributo [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) no ASP.NET Core 2,1 ou posterior for aplicado, os erros de validação de modelo resultarão em um código de status 400.</span><span class="sxs-lookup"><span data-stu-id="9b5ec-165">If the [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute in ASP.NET Core 2.1 or later is applied, model validation errors result in a 400 status code.</span></span> <span data-ttu-id="9b5ec-166">Para obter mais informações, veja [Respostas automáticas HTTP 400](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="9b5ec-166">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="actionresultt-type"></a><span data-ttu-id="9b5ec-167">Tipo ActionResult\<T></span><span class="sxs-lookup"><span data-stu-id="9b5ec-167">ActionResult\<T> type</span></span>

<span data-ttu-id="9b5ec-168">ASP.NET Core 2,1 introduziu o tipo de retorno [\<ActionResult T >](xref:Microsoft.AspNetCore.Mvc.ActionResult`1) para ações do controlador da API Web.</span><span class="sxs-lookup"><span data-stu-id="9b5ec-168">ASP.NET Core 2.1 introduced the [ActionResult\<T>](xref:Microsoft.AspNetCore.Mvc.ActionResult`1) return type for web API controller actions.</span></span> <span data-ttu-id="9b5ec-169">Ele permite que você retorne um tipo derivado de <xref:Microsoft.AspNetCore.Mvc.ActionResult> ou retorne um [tipo específico](#specific-type).</span><span class="sxs-lookup"><span data-stu-id="9b5ec-169">It enables you to return a type deriving from <xref:Microsoft.AspNetCore.Mvc.ActionResult> or return a [specific type](#specific-type).</span></span> <span data-ttu-id="9b5ec-170">`ActionResult<T>` oferece os seguintes benefícios em relação ao [tipo IActionResult](#iactionresult-type):</span><span class="sxs-lookup"><span data-stu-id="9b5ec-170">`ActionResult<T>` offers the following benefits over the [IActionResult type](#iactionresult-type):</span></span>

* <span data-ttu-id="9b5ec-171">O atributo [[ProducesResponseType]](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) pode ter sua propriedade `Type` excluída.</span><span class="sxs-lookup"><span data-stu-id="9b5ec-171">The [[ProducesResponseType]](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) attribute's `Type` property can be excluded.</span></span> <span data-ttu-id="9b5ec-172">Por exemplo, `[ProducesResponseType(200, Type = typeof(Product))]` é simplificado para `[ProducesResponseType(200)]`.</span><span class="sxs-lookup"><span data-stu-id="9b5ec-172">For example, `[ProducesResponseType(200, Type = typeof(Product))]` is simplified to `[ProducesResponseType(200)]`.</span></span> <span data-ttu-id="9b5ec-173">O tipo de retorno esperado da ação é inferido do `T` em `ActionResult<T>`.</span><span class="sxs-lookup"><span data-stu-id="9b5ec-173">The action's expected return type is instead inferred from the `T` in `ActionResult<T>`.</span></span>
* <span data-ttu-id="9b5ec-174">[Operadores de conversão implícita](/dotnet/csharp/language-reference/keywords/implicit) são compatíveis com a conversão de `T` e `ActionResult` em `ActionResult<T>`.</span><span class="sxs-lookup"><span data-stu-id="9b5ec-174">[Implicit cast operators](/dotnet/csharp/language-reference/keywords/implicit) support the conversion of both `T` and `ActionResult` to `ActionResult<T>`.</span></span> <span data-ttu-id="9b5ec-175">`T`Converte para <xref:Microsoft.AspNetCore.Mvc.ObjectResult>, que significa `return new ObjectResult(T);` é simplificado `return T;`para.</span><span class="sxs-lookup"><span data-stu-id="9b5ec-175">`T` converts to <xref:Microsoft.AspNetCore.Mvc.ObjectResult>, which means `return new ObjectResult(T);` is simplified to `return T;`.</span></span>

<span data-ttu-id="9b5ec-176">C# não dá suporte a operadores de conversão implícita em interfaces.</span><span class="sxs-lookup"><span data-stu-id="9b5ec-176">C# doesn't support implicit cast operators on interfaces.</span></span> <span data-ttu-id="9b5ec-177">Consequentemente, a conversão da interface para um tipo concreto é necessário para usar `ActionResult<T>`.</span><span class="sxs-lookup"><span data-stu-id="9b5ec-177">Consequently, conversion of the interface to a concrete type is necessary to use `ActionResult<T>`.</span></span> <span data-ttu-id="9b5ec-178">Por exemplo, o uso de `IEnumerable` no exemplo a seguir não funciona:</span><span class="sxs-lookup"><span data-stu-id="9b5ec-178">For example, use of `IEnumerable` in the following example doesn't work:</span></span>

```csharp
[HttpGet]
public ActionResult<IEnumerable<Product>> Get() =>
    _repository.GetProducts();
```

<span data-ttu-id="9b5ec-179">Uma opção para corrigir o código anterior é retornar a `_repository.GetProducts().ToList();`.</span><span class="sxs-lookup"><span data-stu-id="9b5ec-179">One option to fix the preceding code is to return `_repository.GetProducts().ToList();`.</span></span>

<span data-ttu-id="9b5ec-180">A maioria das ações tem um tipo de retorno específico.</span><span class="sxs-lookup"><span data-stu-id="9b5ec-180">Most actions have a specific return type.</span></span> <span data-ttu-id="9b5ec-181">Condições inesperadas podem ocorrer durante a execução da ação, caso em que o tipo específico não é retornado.</span><span class="sxs-lookup"><span data-stu-id="9b5ec-181">Unexpected conditions can occur during action execution, in which case the specific type isn't returned.</span></span> <span data-ttu-id="9b5ec-182">Por exemplo, o parâmetro de entrada de uma ação pode falhar na validação do modelo.</span><span class="sxs-lookup"><span data-stu-id="9b5ec-182">For example, an action's input parameter may fail model validation.</span></span> <span data-ttu-id="9b5ec-183">Nesse caso, é comum retornar o tipo `ActionResult` adequado, em vez do tipo específico.</span><span class="sxs-lookup"><span data-stu-id="9b5ec-183">In such a case, it's common to return the appropriate `ActionResult` type instead of the specific type.</span></span>

### <a name="synchronous-action"></a><span data-ttu-id="9b5ec-184">Ação síncrona</span><span class="sxs-lookup"><span data-stu-id="9b5ec-184">Synchronous action</span></span>

<span data-ttu-id="9b5ec-185">Considere uma ação síncrona em que há dois tipos de retorno possíveis:</span><span class="sxs-lookup"><span data-stu-id="9b5ec-185">Consider a synchronous action in which there are two possible return types:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetById&highlight=7,10)]

<span data-ttu-id="9b5ec-186">Na ação anterior:</span><span class="sxs-lookup"><span data-stu-id="9b5ec-186">In the preceding action:</span></span>

* <span data-ttu-id="9b5ec-187">Um código de status 404 é retornado quando o produto não existe no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="9b5ec-187">A 404 status code is returned when the product doesn't exist in the database.</span></span>
* <span data-ttu-id="9b5ec-188">Um código de status 200 é retornado com o `Product` objeto correspondente quando o produto existe.</span><span class="sxs-lookup"><span data-stu-id="9b5ec-188">A 200 status code is returned with the corresponding `Product` object when the product does exist.</span></span> <span data-ttu-id="9b5ec-189">Antes ASP.NET Core 2,1, a `return product;` linha tinha que ser `return Ok(product);`.</span><span class="sxs-lookup"><span data-stu-id="9b5ec-189">Before ASP.NET Core 2.1, the `return product;` line had to be `return Ok(product);`.</span></span>

### <a name="asynchronous-action"></a><span data-ttu-id="9b5ec-190">Ação assíncrona</span><span class="sxs-lookup"><span data-stu-id="9b5ec-190">Asynchronous action</span></span>

<span data-ttu-id="9b5ec-191">Considere uma ação assíncrona em que há dois tipos de retorno possíveis:</span><span class="sxs-lookup"><span data-stu-id="9b5ec-191">Consider an asynchronous action in which there are two possible return types:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsync&highlight=8,13)]

<span data-ttu-id="9b5ec-192">Na ação anterior:</span><span class="sxs-lookup"><span data-stu-id="9b5ec-192">In the preceding action:</span></span>

* <span data-ttu-id="9b5ec-193">Um código de status 400<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>() é retornado pelo tempo de execução de ASP.NET Core quando:</span><span class="sxs-lookup"><span data-stu-id="9b5ec-193">A 400 status code (<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>) is returned by the ASP.NET Core runtime when:</span></span>
  * <span data-ttu-id="9b5ec-194">O atributo [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) tiver sido aplicado e o modelo de validação falhar.</span><span class="sxs-lookup"><span data-stu-id="9b5ec-194">The [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute has been applied and model validation fails.</span></span>
  * <span data-ttu-id="9b5ec-195">A descrição do produto contém "Widget XYZ".</span><span class="sxs-lookup"><span data-stu-id="9b5ec-195">The product description contains "XYZ Widget".</span></span>
* <span data-ttu-id="9b5ec-196">Um código de status 201 é gerado pelo <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> método quando um produto é criado.</span><span class="sxs-lookup"><span data-stu-id="9b5ec-196">A 201 status code is generated by the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method when a product is created.</span></span> <span data-ttu-id="9b5ec-197">Nesse caminho de código, o `Product` objeto é fornecido no corpo da resposta.</span><span class="sxs-lookup"><span data-stu-id="9b5ec-197">In this code path, the `Product` object is provided in the response body.</span></span> <span data-ttu-id="9b5ec-198">É `Location` fornecido um cabeçalho de resposta contendo a URL do produto recém-criado.</span><span class="sxs-lookup"><span data-stu-id="9b5ec-198">A `Location` response header containing the newly created product's URL is provided.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="9b5ec-199">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="9b5ec-199">Additional resources</span></span>

* <xref:mvc/controllers/actions>
* <xref:mvc/models/validation>
* <xref:tutorials/web-api-help-pages-using-swagger>
