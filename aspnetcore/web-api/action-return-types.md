---
title: Tipos de retorno de ação do controlador na API Web ASP.NET Core
author: scottaddie
description: Aprenda a usar os vários tipos de retorno do método de ação do controlador em uma API Web ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/04/2019
uid: web-api/action-return-types
ms.openlocfilehash: 180d76c2c2e53dbf64b8fcc5cdc6d2b6f4dab6eb
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64887111"
---
# <a name="controller-action-return-types-in-aspnet-core-web-api"></a>Tipos de retorno de ação do controlador na API Web ASP.NET Core

Por [Scott Addie](https://github.com/scottaddie)

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/action-return-types/samples) ([como baixar](xref:index#how-to-download-a-sample))

O ASP.NET Core oferece as seguintes opções para os tipos de retorno da ação do controlador da API Web:

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

[!code-csharp[](../web-api/action-return-types/samples/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_Get)]

Sem condições conhecidas contra as quais se proteger durante a execução da ação, retornar um tipo específico pode ser suficiente. A ação anterior não aceita parâmetros, assim, validação de restrições de parâmetro não é necessária.

Quando condições conhecidas precisarem ser incluídas em uma ação, vários caminhos de retorno serão introduzidos. Nesse caso, é comum combinar um tipo de retorno [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) com o tipo de retorno primitivo ou complexo. [IActionResult](#iactionresult-type) ou [ActionResult\<T >](#actionresultt-type) é necessário para acomodar esse tipo de ação.

## <a name="iactionresult-type"></a>Tipo IActionResult

O tipo de retorno [IActionResult](/dotnet/api/microsoft.aspnetcore.mvc.iactionresult) é apropriado quando vários tipos de retorno [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) são possíveis em uma ação. Os tipos `ActionResult` representam vários códigos de status HTTP. Alguns tipos de retorno comuns que se enquadram nessa categoria são [BadRequestResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestresult) (400) [NotFoundResult](/dotnet/api/microsoft.aspnetcore.mvc.notfoundresult) (404) e [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200).

Porque há vários tipos de retorno e caminhos na ação, o uso liberal do atributo [[ProducesResponseType]](/dotnet/api/microsoft.aspnetcore.mvc.producesresponsetypeattribute.-ctor) é necessário. Esse atributo produz detalhes de resposta mais descritivos para páginas de ajuda da API geradas por ferramentas como [Swagger](/aspnet/core/tutorials/web-api-help-pages-using-swagger). `[ProducesResponseType]` indica os tipos conhecidos e os códigos de status HTTP a serem retornados pela ação.

### <a name="synchronous-action"></a>Ação síncrona

Considere a seguinte ação síncrona em que há dois tipos de retorno possíveis:

[!code-csharp[](../web-api/action-return-types/samples/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_GetById&highlight=8,11)]

Na ação anterior, um código de status 404 é retornado quando o produto representado por `id` não existe no armazenamento de dados subjacente. O método auxiliar [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) é invocado como um atalho para `return new NotFoundResult();`. Se o produto existir, um objeto `Product` que representa o conteúdo será retornado com um código de status 200. O método auxiliar [OK](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.ok) é invocado como a forma abreviada de `return new OkObjectResult(product);`.

### <a name="asynchronous-action"></a>Ação assíncrona

Considere a seguinte ação assíncrona em que há dois tipos de retorno possíveis:

[!code-csharp[](../web-api/action-return-types/samples/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_CreateAsync&highlight=8,13)]

No código anterior:

* O tempo de execução do ASP.NET Core retorna um código de status 400 ([BadRequest](xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*)) quando a descrição do produto contém "Widget XYZ".
* O método [CreatedAtAction](xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*) gera um código de status 201 quando um produto é criado. Nesse caminho de código, o objeto `Product` é retornado.

Por exemplo, o modelo a seguir indica que as solicitações devem incluir as propriedades `Name` e `Description`. Portanto, a falha em fornecer `Name` e `Description` na solicitação causa falha na validação do modelo.

[!code-csharp[](../web-api/action-return-types/samples/WebApiSample.DataAccess/Models/Product.cs?name=snippet_ProductClass&highlight=5-6,8-9)]

::: moniker range=">= aspnetcore-2.1"

Se o atributo [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) no ASP.NET Core 2.1 ou posterior for aplicado, erros de validação de modelo resultarão em um código de status 400. Para obter mais informações, veja [Respostas automáticas HTTP 400](xref:web-api/index#automatic-http-400-responses).

## <a name="actionresultt-type"></a>Tipo ActionResult\<T>

O ASP.NET Core 2.1 apresenta o tipo de retorno [ActionResult\<T >](/dotnet/api/microsoft.aspnetcore.mvc.actionresult-1) para ações do controlador de API Web. Permite que você retorne um tipo derivado de [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) ou retorne um [tipo específico](#specific-type). `ActionResult<T>` oferece os seguintes benefícios em relação ao [tipo IActionResult](#iactionresult-type):

* O atributo [[ProducesResponseType]](/dotnet/api/microsoft.aspnetcore.mvc.producesresponsetypeattribute) pode ter sua propriedade `Type` excluída. Por exemplo, `[ProducesResponseType(200, Type = typeof(Product))]` é simplificado para `[ProducesResponseType(200)]`. O tipo de retorno esperado da ação é inferido do `T` em `ActionResult<T>`.
* [Operadores de conversão implícita](/dotnet/csharp/language-reference/keywords/implicit) são compatíveis com a conversão de `T` e `ActionResult` em `ActionResult<T>`. `T` converte em [ObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.objectresult), o que significa que `return new ObjectResult(T);` é simplificado para `return T;`.

C# não dá suporte a operadores de conversão implícita em interfaces. Consequentemente, a conversão da interface para um tipo concreto é necessário para usar `ActionResult<T>`. Por exemplo, o uso de `IEnumerable` no exemplo a seguir não funciona:

```csharp
[HttpGet]
public ActionResult<IEnumerable<Product>> Get()
{
    return _repository.GetProducts();
}
```

Uma opção para corrigir o código anterior é retornar a `_repository.GetProducts().ToList();`.

A maioria das ações tem um tipo de retorno específico. Condições inesperadas podem ocorrer durante a execução da ação, caso em que o tipo específico não é retornado. Por exemplo, o parâmetro de entrada de uma ação pode falhar na validação do modelo. Nesse caso, é comum retornar o tipo `ActionResult` adequado, em vez do tipo específico.

### <a name="synchronous-action"></a>Ação síncrona

Considere uma ação síncrona em que há dois tipos de retorno possíveis:

[!code-csharp[](../web-api/action-return-types/samples/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetById&highlight=8,11)]

No código anterior, um código de status 404 é retornado quando o produto não existe no banco de dados. Se o produto existir, o objeto `Product` correspondente será retornado. Antes do ASP.NET Core 2.1, a linha `return product;` teria sido `return Ok(product);`.

> [!TIP]
> Do ASP.NET Core 2.1 em diante, a inferência de origem de associação de parâmetro de ação é habilitada quando uma classe de controlador é decorada com o atributo `[ApiController]`. Um nome de parâmetro correspondente a um nome do modelo de rota é associado automaticamente usando os dados de rota de solicitação. Consequentemente, o parâmetro `id` da ação anterior não é explicitamente anotado com o atributo [[FromRoute]](/dotnet/api/microsoft.aspnetcore.mvc.fromrouteattribute).

### <a name="asynchronous-action"></a>Ação assíncrona

Considere uma ação assíncrona em que há dois tipos de retorno possíveis:

[!code-csharp[](../web-api/action-return-types/samples/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsync&highlight=8,13)]

No código anterior:

* O tempo de execução do ASP.NET Core retorna um código de status 400 ([BadRequest](xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*)) quando:
  * O atributo [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) tiver sido aplicado e o modelo de validação falhar.
  * A descrição do produto contém "Widget XYZ".
* O método [CreatedAtAction](xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*) gera um código de status 201 quando um produto é criado. Nesse caminho de código, o objeto `Product` é retornado.

> [!TIP]
> Do ASP.NET Core 2.1 em diante, a inferência de origem de associação de parâmetro de ação é habilitada quando uma classe de controlador é decorada com o atributo `[ApiController]`. Parâmetros de tipo complexo são vinculados automaticamente usando o corpo da solicitação. Consequentemente, o parâmetro `product` da ação anterior não é explicitamente anotado com o atributo [[FromBody]](/dotnet/api/microsoft.aspnetcore.mvc.frombodyattribute).

::: moniker-end

## <a name="additional-resources"></a>Recursos adicionais

* <xref:mvc/controllers/actions>
* <xref:mvc/models/validation>
* <xref:tutorials/web-api-help-pages-using-swagger>
