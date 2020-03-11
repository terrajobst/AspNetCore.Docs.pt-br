---
title: Tipos de retorno de ação do controlador em ASP.NET Core API Web
author: scottaddie
description: Saiba como usar os vários tipos de retorno do método de ação do controlador em uma API da Web do ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 02/03/2020
uid: web-api/action-return-types
ms.openlocfilehash: 17e290d3aba4f724fcbd1693af371017c4d3f03a
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78655242"
---
# <a name="controller-action-return-types-in-aspnet-core-web-api"></a>Tipos de retorno de ação do controlador em ASP.NET Core API Web

Por [Scott Addie](https://github.com/scottaddie)

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/action-return-types/samples) ([como baixar](xref:index#how-to-download-a-sample))

O ASP.NET Core oferece as seguintes opções para tipos de retorno de ação do controlador da API Web:

::: moniker range=">= aspnetcore-2.1"

* [Tipo específico](#specific-type)
* [IActionResult](#iactionresult-type)
* [ActionResult\<T>](#actionresultt-type)

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* [Tipo específico](#specific-type)
* [IActionResult](#iactionresult-type)

::: moniker-end

Este documento explica quando é mais adequado usar cada tipo de retorno.

## <a name="specific-type"></a>Tipo específico

A ação mais simples retorna um tipo de dados complexo ou primitivo (por exemplo, `string` ou um tipo de objeto personalizado). Considere a seguinte ação, que retorna uma coleção de objetos `Product` personalizados:

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_Get)]

Sem condições conhecidas contra as quais se proteger durante a execução da ação, retornar um tipo específico pode ser suficiente. A ação anterior não aceita parâmetros, assim, validação de restrições de parâmetro não é necessária.

Quando condições conhecidas precisarem ser incluídas em uma ação, vários caminhos de retorno serão introduzidos. Nesse caso, é comum misturar um tipo de retorno de <xref:Microsoft.AspNetCore.Mvc.ActionResult> com o tipo de retorno primitivo ou complexo. [IActionResult](#iactionresult-type) ou [ActionResult\<T >](#actionresultt-type) é necessário para acomodar esse tipo de ação.

### <a name="return-ienumerablet-or-iasyncenumerablet"></a>Retornar IEnumerable\<T > ou IAsyncEnumerable\<T >

No ASP.NET Core 2,2 e anteriores, retornar <xref:System.Collections.Generic.IEnumerable%601> de uma ação resulta em uma iteração de coleção síncrona pelo serializador. O resultado é o bloqueio de chamadas e um potencial para a privação do pool de threads. Para ilustrar, imagine que o núcleo do Entity Framework (EF) está sendo usado para as necessidades de acesso a dados da API Web. O tipo de retorno da ação a seguir é enumerado de forma síncrona durante a serialização:

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

Para evitar a enumeração síncrona e as esperas de bloqueio no banco de dados no ASP.NET Core 2,2 e anteriores, invoque `ToListAsync`:

```csharp
public async Task<IEnumerable<Product>> GetOnSaleProducts() =>
    await _context.Products.Where(p => p.IsOnSale).ToListAsync();
```

No ASP.NET Core 3,0 e posterior, retornando `IAsyncEnumerable<T>` de uma ação:

* Não resulta mais em iteração síncrona.
* Torna-se tão eficiente quanto retornar <xref:System.Collections.Generic.IEnumerable%601>.

ASP.NET Core 3,0 e posterior armazena em buffer o resultado da seguinte ação antes de fornecer ao serializador:

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

Considere declarar o tipo de retorno da assinatura de ação como `IAsyncEnumerable<T>` para garantir a iteração assíncrona. Por fim, o modo de iteração é baseado no tipo concreto subjacente que está sendo retornado. O MVC armazena em buffer automaticamente qualquer tipo concreto que implemente `IAsyncEnumerable<T>`.

Considere a seguinte ação, que retorna registros de produtos com preço de venda como `IEnumerable<Product>`:

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.30/Controllers/ProductsController.cs?name=snippet_GetOnSaleProducts)]

O `IAsyncEnumerable<Product>` equivalente da ação anterior é:

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.30/Controllers/ProductsController.cs?name=snippet_GetOnSaleProductsAsync)]

As duas ações anteriores não são bloqueadas a partir do ASP.NET Core 3,0.

## <a name="iactionresult-type"></a>Tipo IActionResult

O tipo de retorno <xref:Microsoft.AspNetCore.Mvc.IActionResult> é apropriado quando vários tipos de retorno de `ActionResult` são possíveis em uma ação. Os tipos `ActionResult` representam vários códigos de status HTTP. Qualquer classe não abstrata derivada de `ActionResult` é qualificada como um tipo de retorno válido. Alguns tipos de retorno comuns nessa categoria são <xref:Microsoft.AspNetCore.Mvc.BadRequestResult> (400), <xref:Microsoft.AspNetCore.Mvc.NotFoundResult> (404) e <xref:Microsoft.AspNetCore.Mvc.OkObjectResult> (200). Como alternativa, os métodos de conveniência na classe <xref:Microsoft.AspNetCore.Mvc.ControllerBase> podem ser usados para retornar `ActionResult` tipos de uma ação. Por exemplo, `return BadRequest();` é uma forma abreviada de `return new BadRequestResult();`.

Como há vários tipos de retorno e caminhos nesse tipo de ação, liberal uso do atributo [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) é necessário. Esse atributo produz detalhes de resposta mais descritivos para páginas de ajuda da API Web geradas por ferramentas como o [Swagger](xref:tutorials/web-api-help-pages-using-swagger). `[ProducesResponseType]` indica os tipos conhecidos e os códigos de status HTTP a serem retornados pela ação.

### <a name="synchronous-action"></a>Ação síncrona

Considere a seguinte ação síncrona em que há dois tipos de retorno possíveis:

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetByIdIActionResult&highlight=8,11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_GetById&highlight=8,11)]

::: moniker-end

Na ação anterior:

* Um código de status 404 é retornado quando o produto representado pelo `id` não existe no armazenamento de dados subjacente. O método de conveniência <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> é invocado como abreviação para `return new NotFoundResult();`.
* Um código de status 200 é retornado com o objeto `Product` quando o produto existe. O método de conveniência <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> é invocado como abreviação para `return new OkObjectResult(product);`.

### <a name="asynchronous-action"></a>Ação assíncrona

Considere a seguinte ação assíncrona em que há dois tipos de retorno possíveis:

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsyncIActionResult&highlight=9,14)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_CreateAsync&highlight=9,14)]

::: moniker-end

Na ação anterior:

* Um código de status 400 é retornado quando a descrição do produto contém "widget XYZ". O método de conveniência <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> é invocado como abreviação para `return new BadRequestResult();`.
* Um código de status 201 é gerado pelo método de conveniência <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> quando um produto é criado. Uma alternativa para chamar `CreatedAtAction` é `return new CreatedAtActionResult(nameof(GetById), "Products", new { id = product.Id }, product);`. Nesse caminho de código, o objeto `Product` é fornecido no corpo da resposta. É fornecido um cabeçalho de resposta `Location` que contém a URL do produto recém-criado.

Por exemplo, o modelo a seguir indica que as solicitações devem incluir as propriedades `Name` e `Description`. A falha ao fornecer `Name` e `Description` na solicitação faz com que a validação do modelo falhe.

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.DataAccess/Models/Product.cs?name=snippet_ProductClass&highlight=5-6,8-9)]

::: moniker range=">= aspnetcore-2.1"

Se o atributo [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) no ASP.NET Core 2,1 ou posterior for aplicado, os erros de validação de modelo resultarão em um código de status 400. Para obter mais informações, veja [Respostas automáticas HTTP 400](xref:web-api/index#automatic-http-400-responses).

## <a name="actionresultt-type"></a>Tipo ActionResult\<T>

ASP.NET Core 2,1 introduziu o tipo de retorno [ActionResult\<t >](xref:Microsoft.AspNetCore.Mvc.ActionResult`1) para ações do controlador da API Web. Ele permite que você retorne um tipo derivado de <xref:Microsoft.AspNetCore.Mvc.ActionResult> ou retorne um [tipo específico](#specific-type). `ActionResult<T>` oferece os seguintes benefícios em relação ao [tipo IActionResult](#iactionresult-type):

* A propriedade `Type` do atributo de [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) pode ser excluída. Por exemplo, `[ProducesResponseType(200, Type = typeof(Product))]` é simplificado para `[ProducesResponseType(200)]`. O tipo de retorno esperado da ação é inferido do `T` em `ActionResult<T>`.
* [Operadores de conversão implícita](/dotnet/csharp/language-reference/keywords/implicit) são compatíveis com a conversão de `T` e `ActionResult` em `ActionResult<T>`. `T` converte para <xref:Microsoft.AspNetCore.Mvc.ObjectResult>, o que significa que `return new ObjectResult(T);` é simplificado para `return T;`.

C# não dá suporte a operadores de conversão implícita em interfaces. Consequentemente, a conversão da interface para um tipo concreto é necessário para usar `ActionResult<T>`. Por exemplo, o uso de `IEnumerable` no exemplo a seguir não funciona:

```csharp
[HttpGet]
public ActionResult<IEnumerable<Product>> Get() =>
    _repository.GetProducts();
```

Uma opção para corrigir o código anterior é retornar a `_repository.GetProducts().ToList();`.

A maioria das ações tem um tipo de retorno específico. Condições inesperadas podem ocorrer durante a execução da ação, caso em que o tipo específico não é retornado. Por exemplo, o parâmetro de entrada de uma ação pode falhar na validação do modelo. Nesse caso, é comum retornar o tipo `ActionResult` adequado, em vez do tipo específico.

### <a name="synchronous-action"></a>Ação síncrona

Considere uma ação síncrona em que há dois tipos de retorno possíveis:

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetByIdActionResult&highlight=8,11)]

Na ação anterior:

* Um código de status 404 é retornado quando o produto não existe no banco de dados.
* Um código de status 200 é retornado com o objeto `Product` correspondente quando o produto existe. Antes de ASP.NET Core 2,1, a linha de `return product;` precisava ser `return Ok(product);`.

### <a name="asynchronous-action"></a>Ação assíncrona

Considere uma ação assíncrona em que há dois tipos de retorno possíveis:

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsyncActionResult&highlight=9,14)]

Na ação anterior:

* Um código de status 400 (<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>) é retornado pelo ASP.NET Core tempo de execução quando:
  * O atributo [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) foi aplicado e a validação do modelo falha.
  * A descrição do produto contém "Widget XYZ".
* Um código de status 201 é gerado pelo método <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> quando um produto é criado. Nesse caminho de código, o objeto `Product` é fornecido no corpo da resposta. É fornecido um cabeçalho de resposta `Location` que contém a URL do produto recém-criado.

::: moniker-end

## <a name="additional-resources"></a>Recursos adicionais

* <xref:mvc/controllers/actions>
* <xref:mvc/models/validation>
* <xref:tutorials/web-api-help-pages-using-swagger>
