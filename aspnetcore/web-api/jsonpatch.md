---
title: JsonPatch na API Web do ASP.NET Core
author: tdykstra
description: Saiba como lidar com solicitações de JSON Patch em uma API Web do ASP.NET Core.
ms.author: tdykstra
ms.custom: mvc
ms.date: 03/24/2019
uid: web-api/jsonpatch
ms.openlocfilehash: 14710e6431a2a7ce60fa7f190bef184da85281a0
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64888411"
---
# <a name="jsonpatch-in-aspnet-core-web-api"></a><span data-ttu-id="bdf75-103">JsonPatch na API Web do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bdf75-103">JsonPatch in ASP.NET Core web API</span></span>

<span data-ttu-id="bdf75-104">Por [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="bdf75-104">By [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="bdf75-105">Este artigo explica como lidar com solicitações de JSON Patch em uma API Web do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bdf75-105">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="patch-http-request-method"></a><span data-ttu-id="bdf75-106">Método de solicitação HTTP PATCH</span><span class="sxs-lookup"><span data-stu-id="bdf75-106">PATCH HTTP request method</span></span>

<span data-ttu-id="bdf75-107">Os métodos PUT e [PATCH](https://tools.ietf.org/html/rfc5789) são usados para atualizar um recurso existente.</span><span class="sxs-lookup"><span data-stu-id="bdf75-107">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="bdf75-108">A diferença entre eles é que PUT substitui o recurso inteiro, enquanto PATCH especifica apenas as alterações.</span><span class="sxs-lookup"><span data-stu-id="bdf75-108">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="bdf75-109">JSON Patch</span><span class="sxs-lookup"><span data-stu-id="bdf75-109">JSON Patch</span></span>

<span data-ttu-id="bdf75-110">[JSON Patch](https://tools.ietf.org/html/rfc6902) é um formato para especificar as atualizações a serem aplicadas a um recurso.</span><span class="sxs-lookup"><span data-stu-id="bdf75-110">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="bdf75-111">Um documento de JSON Patch tem uma matriz de *operações*.</span><span class="sxs-lookup"><span data-stu-id="bdf75-111">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="bdf75-112">Cada operação identifica um tipo específico de alteração, como adicionar um elemento de matriz ou substituir um valor de propriedade.</span><span class="sxs-lookup"><span data-stu-id="bdf75-112">Each operation identifies a particular type of change, such as add an array element or replace a property value.</span></span>

<span data-ttu-id="bdf75-113">Por exemplo, os documentos JSON a seguir representam um recurso, um documento de JSON Patch para o recurso e o resultado da aplicação de operações patch.</span><span class="sxs-lookup"><span data-stu-id="bdf75-113">For example, the following JSON documents represent a resource, a JSON patch document for the resource, and the result of applying the patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="bdf75-114">Exemplo de recurso</span><span class="sxs-lookup"><span data-stu-id="bdf75-114">Resource example</span></span>

[!code-csharp[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="bdf75-115">Exemplo de JSON Patch</span><span class="sxs-lookup"><span data-stu-id="bdf75-115">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="bdf75-116">No JSON anterior:</span><span class="sxs-lookup"><span data-stu-id="bdf75-116">In the preceding JSON:</span></span>

* <span data-ttu-id="bdf75-117">A propriedade `op` indica o tipo de operação.</span><span class="sxs-lookup"><span data-stu-id="bdf75-117">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="bdf75-118">A propriedade `path` indica o elemento a ser atualizado.</span><span class="sxs-lookup"><span data-stu-id="bdf75-118">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="bdf75-119">A propriedade `value` fornece o novo valor.</span><span class="sxs-lookup"><span data-stu-id="bdf75-119">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="bdf75-120">Recurso depois do patch</span><span class="sxs-lookup"><span data-stu-id="bdf75-120">Resource after patch</span></span>

<span data-ttu-id="bdf75-121">Este é o recurso após a aplicação do documento JSON Patch anterior:</span><span class="sxs-lookup"><span data-stu-id="bdf75-121">Here's the resource after applying the preceding JSON Patch document:</span></span>

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

<span data-ttu-id="bdf75-122">As alterações feitas ao aplicar um documento JSON Patch a um recurso são atômicas: se alguma operação da lista falhar, nenhuma operação da lista será aplicada.</span><span class="sxs-lookup"><span data-stu-id="bdf75-122">The changes made by applying a JSON Patch document to a resource are atomic: if any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="bdf75-123">Sintaxe de path</span><span class="sxs-lookup"><span data-stu-id="bdf75-123">Path syntax</span></span>

<span data-ttu-id="bdf75-124">A propriedade [path](http://tools.ietf.org/html/rfc6901) de um objeto de operação tem barras entre os níveis.</span><span class="sxs-lookup"><span data-stu-id="bdf75-124">The [path](http://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="bdf75-125">Por exemplo, `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="bdf75-125">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="bdf75-126">Índices baseados em zero são usados para especificar os elementos da matriz.</span><span class="sxs-lookup"><span data-stu-id="bdf75-126">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="bdf75-127">O primeiro elemento da matriz `addresses` estaria em `/addresses/0`.</span><span class="sxs-lookup"><span data-stu-id="bdf75-127">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="bdf75-128">Para `add` ao final de uma matriz, use um hífen (-) ao invés de um número de índice: `/addresses/-`.</span><span class="sxs-lookup"><span data-stu-id="bdf75-128">To `add` to the end of an array, use a hyphen (-) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="bdf75-129">Operações</span><span class="sxs-lookup"><span data-stu-id="bdf75-129">Operations</span></span>

<span data-ttu-id="bdf75-130">A tabela a seguir mostra operações compatíveis conforme definido na [especificação de JSON Patch](https://tools.ietf.org/html/rfc6902):</span><span class="sxs-lookup"><span data-stu-id="bdf75-130">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="bdf75-131">Operação</span><span class="sxs-lookup"><span data-stu-id="bdf75-131">Operation</span></span>  | <span data-ttu-id="bdf75-132">Observações</span><span class="sxs-lookup"><span data-stu-id="bdf75-132">Notes</span></span> |
|-----------|--------------------------------|
| `add`     | <span data-ttu-id="bdf75-133">Adicione uma propriedade ou elemento de matriz.</span><span class="sxs-lookup"><span data-stu-id="bdf75-133">Add a property or array element.</span></span> <span data-ttu-id="bdf75-134">Para a propriedade existente: defina o valor.</span><span class="sxs-lookup"><span data-stu-id="bdf75-134">For existing property: set value.</span></span>|
| `remove`  | <span data-ttu-id="bdf75-135">Remova uma propriedade ou elemento de matriz.</span><span class="sxs-lookup"><span data-stu-id="bdf75-135">Remove a property or array element.</span></span> |
| `replace` | <span data-ttu-id="bdf75-136">É o mesmo que `remove`, seguido por `add` no mesmo local.</span><span class="sxs-lookup"><span data-stu-id="bdf75-136">Same as `remove` followed by `add` at same location.</span></span> |
| `move`    | <span data-ttu-id="bdf75-137">É o mesmo que `remove` da origem, seguido por `add` ao destino usando um valor da origem.</span><span class="sxs-lookup"><span data-stu-id="bdf75-137">Same as `remove` from source followed by `add` to destination using value from source.</span></span> |
| `copy`    | <span data-ttu-id="bdf75-138">É o mesmo que `add` ao destino usando um valor da origem.</span><span class="sxs-lookup"><span data-stu-id="bdf75-138">Same as `add` to destination using value from source.</span></span> |
| `test`    | <span data-ttu-id="bdf75-139">Retorna o código de status de êxito se o valor em `path` é igual ao `value` fornecido.</span><span class="sxs-lookup"><span data-stu-id="bdf75-139">Return success status code if value at `path` = provided `value`.</span></span>|

## <a name="jsonpatch-in-aspnet-core"></a><span data-ttu-id="bdf75-140">JsonPatch em ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bdf75-140">JsonPatch in ASP.NET Core</span></span>

<span data-ttu-id="bdf75-141">A implementação do ASP.NET Core de JSON Patch é fornecida no pacote do NuGet [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/).</span><span class="sxs-lookup"><span data-stu-id="bdf75-141">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span> <span data-ttu-id="bdf75-142">O pacote está incluso no metapacote [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="bdf75-142">The package is included in the [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app) metapackage.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="bdf75-143">Código do método de ação</span><span class="sxs-lookup"><span data-stu-id="bdf75-143">Action method code</span></span>

<span data-ttu-id="bdf75-144">Em um controlador de API, um método de ação para JSON Patch:</span><span class="sxs-lookup"><span data-stu-id="bdf75-144">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="bdf75-145">É anotado com o atributo `HttpPatch`.</span><span class="sxs-lookup"><span data-stu-id="bdf75-145">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="bdf75-146">Aceita um `JsonPatchDocument<T>`, normalmente com [FromBody].</span><span class="sxs-lookup"><span data-stu-id="bdf75-146">Accepts a `JsonPatchDocument<T>`, typically with [FromBody].</span></span>
* <span data-ttu-id="bdf75-147">Chama `ApplyTo` no documento de patch para aplicar as alterações.</span><span class="sxs-lookup"><span data-stu-id="bdf75-147">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="bdf75-148">Veja um exemplo:</span><span class="sxs-lookup"><span data-stu-id="bdf75-148">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="bdf75-149">Esse código do aplicativo de exemplo funciona com o seguinte modelo `Customer`.</span><span class="sxs-lookup"><span data-stu-id="bdf75-149">This code from the sample app works with the following `Customer` model.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="bdf75-150">O exemplo de método de ação:</span><span class="sxs-lookup"><span data-stu-id="bdf75-150">The sample action method:</span></span>

* <span data-ttu-id="bdf75-151">Constrói um `Customer`.</span><span class="sxs-lookup"><span data-stu-id="bdf75-151">Constructs a `Customer`.</span></span>
* <span data-ttu-id="bdf75-152">Aplica o patch.</span><span class="sxs-lookup"><span data-stu-id="bdf75-152">Applies the patch.</span></span>
* <span data-ttu-id="bdf75-153">Retorna o resultado no corpo da resposta.</span><span class="sxs-lookup"><span data-stu-id="bdf75-153">Returns the result in the body of the response.</span></span>

 <span data-ttu-id="bdf75-154">Em um aplicativo real, o código recuperaria os dados de um repositório, como um banco de dados, e atualizaria o banco de dados após a aplicação do patch.</span><span class="sxs-lookup"><span data-stu-id="bdf75-154">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="bdf75-155">Estado do modelo</span><span class="sxs-lookup"><span data-stu-id="bdf75-155">Model state</span></span>

<span data-ttu-id="bdf75-156">O exemplo de método de ação anterior chama uma sobrecarga de `ApplyTo` que utiliza o estado do modelo como um de seus parâmetros.</span><span class="sxs-lookup"><span data-stu-id="bdf75-156">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="bdf75-157">Com essa opção, você pode receber mensagens de erro nas respostas.</span><span class="sxs-lookup"><span data-stu-id="bdf75-157">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="bdf75-158">O exemplo a seguir mostra o corpo de uma resposta 400 Solicitação Incorreta para uma operação `test`:</span><span class="sxs-lookup"><span data-stu-id="bdf75-158">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="bdf75-159">Objetos dinâmicos</span><span class="sxs-lookup"><span data-stu-id="bdf75-159">Dynamic objects</span></span>

<span data-ttu-id="bdf75-160">O exemplo de método de ação a seguir mostra como aplicar um patch a um objeto dinâmico.</span><span class="sxs-lookup"><span data-stu-id="bdf75-160">The following action method example shows how to apply a patch to a dynamic object.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="bdf75-161">A operação add</span><span class="sxs-lookup"><span data-stu-id="bdf75-161">The add operation</span></span>

* <span data-ttu-id="bdf75-162">Se `path` aponta para um elemento de matriz: insere um novo elemento antes do especificado por `path`.</span><span class="sxs-lookup"><span data-stu-id="bdf75-162">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="bdf75-163">Se `path` aponta para uma propriedade: define o valor da propriedade.</span><span class="sxs-lookup"><span data-stu-id="bdf75-163">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="bdf75-164">Se `path` aponta para um local não existente:</span><span class="sxs-lookup"><span data-stu-id="bdf75-164">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="bdf75-165">Se o recurso no qual fazer patch é um objeto dinâmico: adiciona uma propriedade.</span><span class="sxs-lookup"><span data-stu-id="bdf75-165">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="bdf75-166">Se o recurso no qual fazer patch é um objeto estático: a solicitação falha.</span><span class="sxs-lookup"><span data-stu-id="bdf75-166">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="bdf75-167">O exemplo de documento de patch a seguir define o valor de `CustomerName` e adiciona um objeto `Order` ao final da matriz `Orders`.</span><span class="sxs-lookup"><span data-stu-id="bdf75-167">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="bdf75-168">A operação remove</span><span class="sxs-lookup"><span data-stu-id="bdf75-168">The remove operation</span></span>

* <span data-ttu-id="bdf75-169">Se `path` aponta para um elemento de matriz: remove o elemento.</span><span class="sxs-lookup"><span data-stu-id="bdf75-169">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="bdf75-170">Se `path` aponta para uma propriedade:</span><span class="sxs-lookup"><span data-stu-id="bdf75-170">If `path` points to a property:</span></span>
  * <span data-ttu-id="bdf75-171">Se o recurso no qual fazer patch é um objeto dinâmico: remove a propriedade.</span><span class="sxs-lookup"><span data-stu-id="bdf75-171">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="bdf75-172">Se o recurso no qual fazer patch é um objeto estático:</span><span class="sxs-lookup"><span data-stu-id="bdf75-172">If resource to patch is a static object:</span></span> 
    * <span data-ttu-id="bdf75-173">Se a propriedade é anulável: define como nulo.</span><span class="sxs-lookup"><span data-stu-id="bdf75-173">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="bdf75-174">Se a propriedade não é anulável: define como `default<T>`.</span><span class="sxs-lookup"><span data-stu-id="bdf75-174">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="bdf75-175">O exemplo de documento de patch a seguir define `CustomerName` como nulo e exclui `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="bdf75-175">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="bdf75-176">A operação replace</span><span class="sxs-lookup"><span data-stu-id="bdf75-176">The replace operation</span></span>

<span data-ttu-id="bdf75-177">Esta operação é funcionalmente a mesma que `remove` seguida por `add`.</span><span class="sxs-lookup"><span data-stu-id="bdf75-177">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="bdf75-178">O exemplo de documento de patch a seguir define o valor de `CustomerName` e substitui `Orders[0]` por um novo objeto `Order`.</span><span class="sxs-lookup"><span data-stu-id="bdf75-178">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="bdf75-179">A operação move</span><span class="sxs-lookup"><span data-stu-id="bdf75-179">The move operation</span></span>

* <span data-ttu-id="bdf75-180">Se `path` aponta para um elemento de matriz: copia o elemento `from` para o local do elemento `path` e, em seguida, executa uma operação `remove` no elemento `from`.</span><span class="sxs-lookup"><span data-stu-id="bdf75-180">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="bdf75-181">Se `path` aponta para uma propriedade: copia o valor da propriedade `from` para a propriedade `path`, depois executa uma operação `remove` na propriedade `from`.</span><span class="sxs-lookup"><span data-stu-id="bdf75-181">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="bdf75-182">Se `path` aponta para uma propriedade não existente:</span><span class="sxs-lookup"><span data-stu-id="bdf75-182">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="bdf75-183">Se o recurso no qual fazer patch é um objeto estático: a solicitação falha.</span><span class="sxs-lookup"><span data-stu-id="bdf75-183">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="bdf75-184">Se o recurso no qual fazer patch é um objeto dinâmico: copia a propriedade `from` para o local indicado por `path` e, em seguida, executa uma operação `remove` na propriedade `from`.</span><span class="sxs-lookup"><span data-stu-id="bdf75-184">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="bdf75-185">O seguinte exemplo de documento de patch:</span><span class="sxs-lookup"><span data-stu-id="bdf75-185">The following sample patch document:</span></span>

* <span data-ttu-id="bdf75-186">Copia o valor de `Orders[0].OrderName` para `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="bdf75-186">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="bdf75-187">Define `Orders[0].OrderName` como nulo.</span><span class="sxs-lookup"><span data-stu-id="bdf75-187">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="bdf75-188">Move `Orders[1]` para antes de `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="bdf75-188">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="bdf75-189">A operação copy</span><span class="sxs-lookup"><span data-stu-id="bdf75-189">The copy operation</span></span>

<span data-ttu-id="bdf75-190">Esta operação é funcionalmente a mesma que uma operação `move`, sem a etapa final `remove`.</span><span class="sxs-lookup"><span data-stu-id="bdf75-190">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="bdf75-191">O seguinte exemplo de documento de patch:</span><span class="sxs-lookup"><span data-stu-id="bdf75-191">The following sample patch document:</span></span>

* <span data-ttu-id="bdf75-192">Copia o valor de `Orders[0].OrderName` para `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="bdf75-192">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="bdf75-193">Insere uma cópia de `Orders[1]` antes de `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="bdf75-193">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="bdf75-194">A operação test</span><span class="sxs-lookup"><span data-stu-id="bdf75-194">The test operation</span></span>

<span data-ttu-id="bdf75-195">Se o valor no local indicado por `path` for diferente do valor fornecido em `value`, a solicitação falhará.</span><span class="sxs-lookup"><span data-stu-id="bdf75-195">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="bdf75-196">Nesse caso, toda a solicitação de PATCH falhará, mesmo se todas as outras operações no documento de patch forem bem-sucedidas.</span><span class="sxs-lookup"><span data-stu-id="bdf75-196">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="bdf75-197">A operação `test` normalmente é usada para impedir uma atualização quando há um conflito de simultaneidade.</span><span class="sxs-lookup"><span data-stu-id="bdf75-197">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="bdf75-198">O seguinte exemplo de documento de patch não terá nenhum efeito se o valor inicial de `CustomerName` for "John", porque o teste falha:</span><span class="sxs-lookup"><span data-stu-id="bdf75-198">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="bdf75-199">Obter o código</span><span class="sxs-lookup"><span data-stu-id="bdf75-199">Get the code</span></span>

<span data-ttu-id="bdf75-200">[Exibir ou baixar o código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span><span class="sxs-lookup"><span data-stu-id="bdf75-200">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span></span> <span data-ttu-id="bdf75-201">([Como baixar](xref:index#how-to-download-a-sample).)</span><span class="sxs-lookup"><span data-stu-id="bdf75-201">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="bdf75-202">Para testar o exemplo, execute o aplicativo e envie solicitações HTTP com as seguintes configurações:</span><span class="sxs-lookup"><span data-stu-id="bdf75-202">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="bdf75-203">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="bdf75-203">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="bdf75-204">Método HTTP: `PATCH`</span><span class="sxs-lookup"><span data-stu-id="bdf75-204">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="bdf75-205">Cabeçalho: `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="bdf75-205">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="bdf75-206">Corpo: Copie e cole um dos exemplos de documento de JSON Patch da pasta de projeto *JSON*.</span><span class="sxs-lookup"><span data-stu-id="bdf75-206">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bdf75-207">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="bdf75-207">Additional resources</span></span>

* [<span data-ttu-id="bdf75-208">Especificação do método PATCH IETF RFC 5789</span><span class="sxs-lookup"><span data-stu-id="bdf75-208">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="bdf75-209">Especificação do JSON Patch IETF RFC 6902</span><span class="sxs-lookup"><span data-stu-id="bdf75-209">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="bdf75-210">Especificação de formato de caminho do JSON Patch IETF RFC 6901</span><span class="sxs-lookup"><span data-stu-id="bdf75-210">IETF RFC 6901 JSON Patch path format spec</span></span>](http://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="bdf75-211">[Documentação do JSON Patch](http://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="bdf75-211">[JSON Patch documentation](http://jsonpatch.com/).</span></span> <span data-ttu-id="bdf75-212">Inclui links para recursos a fim de criar documentos de JSON Patch.</span><span class="sxs-lookup"><span data-stu-id="bdf75-212">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="bdf75-213">Código-fonte de JSON Patch do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bdf75-213">ASP.NET Core JSON Patch source code</span></span>](https://github.com/aspnet/AspNetCore/tree/master/src/Features/JsonPatch/src)
