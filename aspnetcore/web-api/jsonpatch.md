---
title: JsonPatch na API Web do ASP.NET Core
author: rick-anderson
description: Saiba como lidar com solicitações de JSON Patch em uma API Web do ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 11/01/2019
uid: web-api/jsonpatch
ms.openlocfilehash: cf1a00c1928652bf5210b2442087209e23b8868e
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78661780"
---
# <a name="jsonpatch-in-aspnet-core-web-api"></a><span data-ttu-id="dbf91-103">JsonPatch na API Web do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dbf91-103">JsonPatch in ASP.NET Core web API</span></span>

<span data-ttu-id="dbf91-104">Por [Tom Dykstra](https://github.com/tdykstra) e [Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="dbf91-104">By [Tom Dykstra](https://github.com/tdykstra) and [Kirk Larkin](https://github.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="dbf91-105">Este artigo explica como lidar com solicitações de JSON Patch em uma API Web do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dbf91-105">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="package-installation"></a><span data-ttu-id="dbf91-106">Instalação do pacote</span><span class="sxs-lookup"><span data-stu-id="dbf91-106">Package installation</span></span>

<span data-ttu-id="dbf91-107">O suporte para JsonPatch é habilitado usando o pacote de `Microsoft.AspNetCore.Mvc.NewtonsoftJson`.</span><span class="sxs-lookup"><span data-stu-id="dbf91-107">Support for JsonPatch is enabled using the `Microsoft.AspNetCore.Mvc.NewtonsoftJson` package.</span></span> <span data-ttu-id="dbf91-108">Para habilitar esse recurso, os aplicativos devem:</span><span class="sxs-lookup"><span data-stu-id="dbf91-108">To enable this feature, apps must:</span></span>

* <span data-ttu-id="dbf91-109">Instale o pacote NuGet [Microsoft. AspNetCore. Mvc. NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) .</span><span class="sxs-lookup"><span data-stu-id="dbf91-109">Install the [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package.</span></span>
* <span data-ttu-id="dbf91-110">Atualize o método `Startup.ConfigureServices` do projeto para incluir uma chamada para `AddNewtonsoftJson`:</span><span class="sxs-lookup"><span data-stu-id="dbf91-110">Update the project's `Startup.ConfigureServices` method to include a call to `AddNewtonsoftJson`:</span></span>

  ```csharp
  services
      .AddControllersWithViews()
      .AddNewtonsoftJson();
  ```

<span data-ttu-id="dbf91-111">`AddNewtonsoftJson` é compatível com os métodos de registro do serviço MVC:</span><span class="sxs-lookup"><span data-stu-id="dbf91-111">`AddNewtonsoftJson` is compatible with the MVC service registration methods:</span></span>

  * `AddRazorPages`
  * `AddControllersWithViews`
  * `AddControllers`

## <a name="jsonpatch-addnewtonsoftjson-and-systemtextjson"></a><span data-ttu-id="dbf91-112">JsonPatch, AddNewtonsoftJson e System. Text. JSON</span><span class="sxs-lookup"><span data-stu-id="dbf91-112">JsonPatch, AddNewtonsoftJson, and System.Text.Json</span></span>
  
<span data-ttu-id="dbf91-113">`AddNewtonsoftJson` substitui os formatadores de entrada e saída com base em `System.Text.Json` usados para formatar **todo** o conteúdo JSON.</span><span class="sxs-lookup"><span data-stu-id="dbf91-113">`AddNewtonsoftJson` replaces the `System.Text.Json` based input and output formatters used for formatting **all** JSON content.</span></span> <span data-ttu-id="dbf91-114">Para adicionar suporte para `JsonPatch` usando `Newtonsoft.Json`, deixando os outros formatadores inalterados, atualize a `Startup.ConfigureServices` do projeto da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="dbf91-114">To add support for `JsonPatch` using `Newtonsoft.Json`, while leaving the other formatters unchanged, update the project's `Startup.ConfigureServices` as follows:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet)]

<span data-ttu-id="dbf91-115">O código anterior requer uma referência a [Microsoft. AspNetCore. Mvc. NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson) e às seguintes instruções using:</span><span class="sxs-lookup"><span data-stu-id="dbf91-115">The preceding code requires a reference to [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson) and the following using statements:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet1)]

## <a name="patch-http-request-method"></a><span data-ttu-id="dbf91-116">Método de solicitação HTTP PATCH</span><span class="sxs-lookup"><span data-stu-id="dbf91-116">PATCH HTTP request method</span></span>

<span data-ttu-id="dbf91-117">Os métodos PUT e [PATCH](https://tools.ietf.org/html/rfc5789) são usados para atualizar um recurso existente.</span><span class="sxs-lookup"><span data-stu-id="dbf91-117">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="dbf91-118">A diferença entre eles é que PUT substitui o recurso inteiro, enquanto PATCH especifica apenas as alterações.</span><span class="sxs-lookup"><span data-stu-id="dbf91-118">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="dbf91-119">JSON Patch</span><span class="sxs-lookup"><span data-stu-id="dbf91-119">JSON Patch</span></span>

<span data-ttu-id="dbf91-120">[JSON Patch](https://tools.ietf.org/html/rfc6902) é um formato para especificar as atualizações a serem aplicadas a um recurso.</span><span class="sxs-lookup"><span data-stu-id="dbf91-120">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="dbf91-121">Um documento de JSON Patch tem uma matriz de *operações*.</span><span class="sxs-lookup"><span data-stu-id="dbf91-121">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="dbf91-122">Cada operação identifica um tipo específico de alteração, como adicionar um elemento de matriz ou substituir um valor de propriedade.</span><span class="sxs-lookup"><span data-stu-id="dbf91-122">Each operation identifies a particular type of change, such as add an array element or replace a property value.</span></span>

<span data-ttu-id="dbf91-123">Por exemplo, os documentos JSON a seguir representam um recurso, um documento de JSON Patch para o recurso e o resultado da aplicação de operações patch.</span><span class="sxs-lookup"><span data-stu-id="dbf91-123">For example, the following JSON documents represent a resource, a JSON patch document for the resource, and the result of applying the patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="dbf91-124">Exemplo de recurso</span><span class="sxs-lookup"><span data-stu-id="dbf91-124">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="dbf91-125">Exemplo de JSON Patch</span><span class="sxs-lookup"><span data-stu-id="dbf91-125">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="dbf91-126">No JSON anterior:</span><span class="sxs-lookup"><span data-stu-id="dbf91-126">In the preceding JSON:</span></span>

* <span data-ttu-id="dbf91-127">A propriedade `op` indica o tipo de operação.</span><span class="sxs-lookup"><span data-stu-id="dbf91-127">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="dbf91-128">A propriedade `path` indica o elemento a ser atualizado.</span><span class="sxs-lookup"><span data-stu-id="dbf91-128">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="dbf91-129">A propriedade `value` fornece o novo valor.</span><span class="sxs-lookup"><span data-stu-id="dbf91-129">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="dbf91-130">Recurso depois do patch</span><span class="sxs-lookup"><span data-stu-id="dbf91-130">Resource after patch</span></span>

<span data-ttu-id="dbf91-131">Este é o recurso após a aplicação do documento JSON Patch anterior:</span><span class="sxs-lookup"><span data-stu-id="dbf91-131">Here's the resource after applying the preceding JSON Patch document:</span></span>

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

<span data-ttu-id="dbf91-132">As alterações feitas ao aplicar um documento JSON Patch a um recurso são atômicas: se alguma operação da lista falhar, nenhuma operação da lista será aplicada.</span><span class="sxs-lookup"><span data-stu-id="dbf91-132">The changes made by applying a JSON Patch document to a resource are atomic: if any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="dbf91-133">Sintaxe de path</span><span class="sxs-lookup"><span data-stu-id="dbf91-133">Path syntax</span></span>

<span data-ttu-id="dbf91-134">A propriedade [path](https://tools.ietf.org/html/rfc6901) de um objeto de operação tem barras entre os níveis.</span><span class="sxs-lookup"><span data-stu-id="dbf91-134">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="dbf91-135">Por exemplo, `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="dbf91-135">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="dbf91-136">Índices baseados em zero são usados para especificar os elementos da matriz.</span><span class="sxs-lookup"><span data-stu-id="dbf91-136">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="dbf91-137">O primeiro elemento da matriz `addresses` estaria em `/addresses/0`.</span><span class="sxs-lookup"><span data-stu-id="dbf91-137">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="dbf91-138">Para `add` ao final de uma matriz, use um hífen (-) ao invés de um número de índice: `/addresses/-`.</span><span class="sxs-lookup"><span data-stu-id="dbf91-138">To `add` to the end of an array, use a hyphen (-) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="dbf91-139">Operações</span><span class="sxs-lookup"><span data-stu-id="dbf91-139">Operations</span></span>

<span data-ttu-id="dbf91-140">A tabela a seguir mostra operações compatíveis conforme definido na [especificação de JSON Patch](https://tools.ietf.org/html/rfc6902):</span><span class="sxs-lookup"><span data-stu-id="dbf91-140">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="dbf91-141">Operação</span><span class="sxs-lookup"><span data-stu-id="dbf91-141">Operation</span></span>  | <span data-ttu-id="dbf91-142">{1&gt;Observações&lt;1}</span><span class="sxs-lookup"><span data-stu-id="dbf91-142">Notes</span></span> |
|-----------|--------------------------------|
| `add`     | <span data-ttu-id="dbf91-143">Adicione uma propriedade ou elemento de matriz.</span><span class="sxs-lookup"><span data-stu-id="dbf91-143">Add a property or array element.</span></span> <span data-ttu-id="dbf91-144">Para a propriedade existente: defina o valor.</span><span class="sxs-lookup"><span data-stu-id="dbf91-144">For existing property: set value.</span></span>|
| `remove`  | <span data-ttu-id="dbf91-145">Remova uma propriedade ou elemento de matriz.</span><span class="sxs-lookup"><span data-stu-id="dbf91-145">Remove a property or array element.</span></span> |
| `replace` | <span data-ttu-id="dbf91-146">É o mesmo que `remove`, seguido por `add` no mesmo local.</span><span class="sxs-lookup"><span data-stu-id="dbf91-146">Same as `remove` followed by `add` at same location.</span></span> |
| `move`    | <span data-ttu-id="dbf91-147">É o mesmo que `remove` da origem, seguido por `add` ao destino usando um valor da origem.</span><span class="sxs-lookup"><span data-stu-id="dbf91-147">Same as `remove` from source followed by `add` to destination using value from source.</span></span> |
| `copy`    | <span data-ttu-id="dbf91-148">É o mesmo que `add` ao destino usando um valor da origem.</span><span class="sxs-lookup"><span data-stu-id="dbf91-148">Same as `add` to destination using value from source.</span></span> |
| `test`    | <span data-ttu-id="dbf91-149">Retorna o código de status de êxito se o valor em `path` é igual ao `value` fornecido.</span><span class="sxs-lookup"><span data-stu-id="dbf91-149">Return success status code if value at `path` = provided `value`.</span></span>|

## <a name="jsonpatch-in-aspnet-core"></a><span data-ttu-id="dbf91-150">JsonPatch em ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dbf91-150">JsonPatch in ASP.NET Core</span></span>

<span data-ttu-id="dbf91-151">A implementação do ASP.NET Core de JSON Patch é fornecida no pacote do NuGet [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/).</span><span class="sxs-lookup"><span data-stu-id="dbf91-151">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="dbf91-152">Código do método de ação</span><span class="sxs-lookup"><span data-stu-id="dbf91-152">Action method code</span></span>

<span data-ttu-id="dbf91-153">Em um controlador de API, um método de ação para JSON Patch:</span><span class="sxs-lookup"><span data-stu-id="dbf91-153">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="dbf91-154">É anotado com o atributo `HttpPatch`.</span><span class="sxs-lookup"><span data-stu-id="dbf91-154">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="dbf91-155">Aceita um `JsonPatchDocument<T>`, normalmente com `[FromBody]`.</span><span class="sxs-lookup"><span data-stu-id="dbf91-155">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="dbf91-156">Chama `ApplyTo` no documento de patch para aplicar as alterações.</span><span class="sxs-lookup"><span data-stu-id="dbf91-156">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="dbf91-157">Aqui está um exemplo:</span><span class="sxs-lookup"><span data-stu-id="dbf91-157">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="dbf91-158">Esse código do aplicativo de exemplo funciona com o seguinte modelo `Customer`.</span><span class="sxs-lookup"><span data-stu-id="dbf91-158">This code from the sample app works with the following `Customer` model.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="dbf91-159">O exemplo de método de ação:</span><span class="sxs-lookup"><span data-stu-id="dbf91-159">The sample action method:</span></span>

* <span data-ttu-id="dbf91-160">Constrói um `Customer`.</span><span class="sxs-lookup"><span data-stu-id="dbf91-160">Constructs a `Customer`.</span></span>
* <span data-ttu-id="dbf91-161">Aplica o patch.</span><span class="sxs-lookup"><span data-stu-id="dbf91-161">Applies the patch.</span></span>
* <span data-ttu-id="dbf91-162">Retorna o resultado no corpo da resposta.</span><span class="sxs-lookup"><span data-stu-id="dbf91-162">Returns the result in the body of the response.</span></span>

 <span data-ttu-id="dbf91-163">Em um aplicativo real, o código recuperaria os dados de um repositório, como um banco de dados, e atualizaria o banco de dados após a aplicação do patch.</span><span class="sxs-lookup"><span data-stu-id="dbf91-163">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="dbf91-164">Estado do modelo</span><span class="sxs-lookup"><span data-stu-id="dbf91-164">Model state</span></span>

<span data-ttu-id="dbf91-165">O exemplo de método de ação anterior chama uma sobrecarga de `ApplyTo` que utiliza o estado do modelo como um de seus parâmetros.</span><span class="sxs-lookup"><span data-stu-id="dbf91-165">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="dbf91-166">Com essa opção, você pode receber mensagens de erro nas respostas.</span><span class="sxs-lookup"><span data-stu-id="dbf91-166">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="dbf91-167">O exemplo a seguir mostra o corpo de uma resposta 400 Solicitação Incorreta para uma operação `test`:</span><span class="sxs-lookup"><span data-stu-id="dbf91-167">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="dbf91-168">Objetos dinâmicos</span><span class="sxs-lookup"><span data-stu-id="dbf91-168">Dynamic objects</span></span>

<span data-ttu-id="dbf91-169">O exemplo de método de ação a seguir mostra como aplicar um patch a um objeto dinâmico.</span><span class="sxs-lookup"><span data-stu-id="dbf91-169">The following action method example shows how to apply a patch to a dynamic object.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="dbf91-170">A operação add</span><span class="sxs-lookup"><span data-stu-id="dbf91-170">The add operation</span></span>

* <span data-ttu-id="dbf91-171">Se `path` aponta para um elemento de matriz: insere um novo elemento antes do especificado por `path`.</span><span class="sxs-lookup"><span data-stu-id="dbf91-171">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="dbf91-172">Se `path` aponta para uma propriedade: define o valor da propriedade.</span><span class="sxs-lookup"><span data-stu-id="dbf91-172">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="dbf91-173">Se `path` aponta para um local não existente:</span><span class="sxs-lookup"><span data-stu-id="dbf91-173">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="dbf91-174">Se o recurso no qual fazer patch é um objeto dinâmico: adiciona uma propriedade.</span><span class="sxs-lookup"><span data-stu-id="dbf91-174">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="dbf91-175">Se o recurso no qual fazer patch é um objeto estático: a solicitação falha.</span><span class="sxs-lookup"><span data-stu-id="dbf91-175">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="dbf91-176">O exemplo de documento de patch a seguir define o valor de `CustomerName` e adiciona um objeto `Order` ao final da matriz `Orders`.</span><span class="sxs-lookup"><span data-stu-id="dbf91-176">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="dbf91-177">A operação remove</span><span class="sxs-lookup"><span data-stu-id="dbf91-177">The remove operation</span></span>

* <span data-ttu-id="dbf91-178">Se `path` aponta para um elemento de matriz: remove o elemento.</span><span class="sxs-lookup"><span data-stu-id="dbf91-178">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="dbf91-179">Se `path` aponta para uma propriedade:</span><span class="sxs-lookup"><span data-stu-id="dbf91-179">If `path` points to a property:</span></span>
  * <span data-ttu-id="dbf91-180">Se o recurso no qual fazer patch é um objeto dinâmico: remove a propriedade.</span><span class="sxs-lookup"><span data-stu-id="dbf91-180">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="dbf91-181">Se o recurso no qual fazer patch é um objeto estático:</span><span class="sxs-lookup"><span data-stu-id="dbf91-181">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="dbf91-182">Se a propriedade é anulável: define como nulo.</span><span class="sxs-lookup"><span data-stu-id="dbf91-182">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="dbf91-183">Se a propriedade não é anulável: define como `default<T>`.</span><span class="sxs-lookup"><span data-stu-id="dbf91-183">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="dbf91-184">O exemplo de documento de patch a seguir define `CustomerName` como nulo e exclui `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="dbf91-184">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="dbf91-185">A operação replace</span><span class="sxs-lookup"><span data-stu-id="dbf91-185">The replace operation</span></span>

<span data-ttu-id="dbf91-186">Esta operação é funcionalmente a mesma que `remove` seguida por `add`.</span><span class="sxs-lookup"><span data-stu-id="dbf91-186">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="dbf91-187">O exemplo de documento de patch a seguir define o valor de `CustomerName` e substitui `Orders[0]` por um novo objeto `Order`.</span><span class="sxs-lookup"><span data-stu-id="dbf91-187">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="dbf91-188">A operação move</span><span class="sxs-lookup"><span data-stu-id="dbf91-188">The move operation</span></span>

* <span data-ttu-id="dbf91-189">Se `path` aponta para um elemento de matriz: copia o elemento `from` para o local do elemento `path` e, em seguida, executa uma operação `remove` no elemento `from`.</span><span class="sxs-lookup"><span data-stu-id="dbf91-189">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="dbf91-190">Se `path` aponta para uma propriedade: copia o valor da propriedade `from` para a propriedade `path`, depois executa uma operação `remove` na propriedade `from`.</span><span class="sxs-lookup"><span data-stu-id="dbf91-190">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="dbf91-191">Se `path` aponta para uma propriedade não existente:</span><span class="sxs-lookup"><span data-stu-id="dbf91-191">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="dbf91-192">Se o recurso no qual fazer patch é um objeto estático: a solicitação falha.</span><span class="sxs-lookup"><span data-stu-id="dbf91-192">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="dbf91-193">Se o recurso no qual fazer patch é um objeto dinâmico: copia a propriedade `from` para o local indicado por `path` e, em seguida, executa uma operação `remove` na propriedade `from`.</span><span class="sxs-lookup"><span data-stu-id="dbf91-193">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="dbf91-194">O seguinte exemplo de documento de patch:</span><span class="sxs-lookup"><span data-stu-id="dbf91-194">The following sample patch document:</span></span>

* <span data-ttu-id="dbf91-195">Copia o valor de `Orders[0].OrderName` para `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="dbf91-195">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="dbf91-196">Define `Orders[0].OrderName` como nulo.</span><span class="sxs-lookup"><span data-stu-id="dbf91-196">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="dbf91-197">Move `Orders[1]` para antes de `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="dbf91-197">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="dbf91-198">A operação copy</span><span class="sxs-lookup"><span data-stu-id="dbf91-198">The copy operation</span></span>

<span data-ttu-id="dbf91-199">Esta operação é funcionalmente a mesma que uma operação `move`, sem a etapa final `remove`.</span><span class="sxs-lookup"><span data-stu-id="dbf91-199">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="dbf91-200">O seguinte exemplo de documento de patch:</span><span class="sxs-lookup"><span data-stu-id="dbf91-200">The following sample patch document:</span></span>

* <span data-ttu-id="dbf91-201">Copia o valor de `Orders[0].OrderName` para `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="dbf91-201">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="dbf91-202">Insere uma cópia de `Orders[1]` antes de `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="dbf91-202">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="dbf91-203">A operação test</span><span class="sxs-lookup"><span data-stu-id="dbf91-203">The test operation</span></span>

<span data-ttu-id="dbf91-204">Se o valor no local indicado por `path` for diferente do valor fornecido em `value`, a solicitação falhará.</span><span class="sxs-lookup"><span data-stu-id="dbf91-204">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="dbf91-205">Nesse caso, toda a solicitação de PATCH falhará, mesmo se todas as outras operações no documento de patch forem bem-sucedidas.</span><span class="sxs-lookup"><span data-stu-id="dbf91-205">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="dbf91-206">A operação `test` normalmente é usada para impedir uma atualização quando há um conflito de simultaneidade.</span><span class="sxs-lookup"><span data-stu-id="dbf91-206">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="dbf91-207">O seguinte exemplo de documento de patch não terá nenhum efeito se o valor inicial de `CustomerName` for "John", porque o teste falha:</span><span class="sxs-lookup"><span data-stu-id="dbf91-207">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="dbf91-208">Obter o código</span><span class="sxs-lookup"><span data-stu-id="dbf91-208">Get the code</span></span>

<span data-ttu-id="dbf91-209">[Exibir ou baixar o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span><span class="sxs-lookup"><span data-stu-id="dbf91-209">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span></span> <span data-ttu-id="dbf91-210">([Como baixar](xref:index#how-to-download-a-sample).)</span><span class="sxs-lookup"><span data-stu-id="dbf91-210">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="dbf91-211">Para testar o exemplo, execute o aplicativo e envie solicitações HTTP com as seguintes configurações:</span><span class="sxs-lookup"><span data-stu-id="dbf91-211">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="dbf91-212">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="dbf91-212">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="dbf91-213">Método HTTP: `PATCH`</span><span class="sxs-lookup"><span data-stu-id="dbf91-213">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="dbf91-214">Cabeçalho: `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="dbf91-214">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="dbf91-215">Corpo: Copie e cole um dos exemplos de documento de patch JSON da pasta de projeto *JSON* .</span><span class="sxs-lookup"><span data-stu-id="dbf91-215">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dbf91-216">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="dbf91-216">Additional resources</span></span>

* [<span data-ttu-id="dbf91-217">Especificação do método PATCH IETF RFC 5789</span><span class="sxs-lookup"><span data-stu-id="dbf91-217">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="dbf91-218">Especificação do JSON Patch IETF RFC 6902</span><span class="sxs-lookup"><span data-stu-id="dbf91-218">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="dbf91-219">Especificação de formato de caminho do JSON Patch IETF RFC 6901</span><span class="sxs-lookup"><span data-stu-id="dbf91-219">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="dbf91-220">[Documentação do JSON Patch](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="dbf91-220">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="dbf91-221">Inclui links para recursos a fim de criar documentos de JSON Patch.</span><span class="sxs-lookup"><span data-stu-id="dbf91-221">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="dbf91-222">Código-fonte de JSON Patch do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dbf91-222">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="dbf91-223">Este artigo explica como lidar com solicitações de JSON Patch em uma API Web do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dbf91-223">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="patch-http-request-method"></a><span data-ttu-id="dbf91-224">Método de solicitação HTTP PATCH</span><span class="sxs-lookup"><span data-stu-id="dbf91-224">PATCH HTTP request method</span></span>

<span data-ttu-id="dbf91-225">Os métodos PUT e [PATCH](https://tools.ietf.org/html/rfc5789) são usados para atualizar um recurso existente.</span><span class="sxs-lookup"><span data-stu-id="dbf91-225">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="dbf91-226">A diferença entre eles é que PUT substitui o recurso inteiro, enquanto PATCH especifica apenas as alterações.</span><span class="sxs-lookup"><span data-stu-id="dbf91-226">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="dbf91-227">JSON Patch</span><span class="sxs-lookup"><span data-stu-id="dbf91-227">JSON Patch</span></span>

<span data-ttu-id="dbf91-228">[JSON Patch](https://tools.ietf.org/html/rfc6902) é um formato para especificar as atualizações a serem aplicadas a um recurso.</span><span class="sxs-lookup"><span data-stu-id="dbf91-228">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="dbf91-229">Um documento de JSON Patch tem uma matriz de *operações*.</span><span class="sxs-lookup"><span data-stu-id="dbf91-229">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="dbf91-230">Cada operação identifica um tipo específico de alteração, como adicionar um elemento de matriz ou substituir um valor de propriedade.</span><span class="sxs-lookup"><span data-stu-id="dbf91-230">Each operation identifies a particular type of change, such as add an array element or replace a property value.</span></span>

<span data-ttu-id="dbf91-231">Por exemplo, os documentos JSON a seguir representam um recurso, um documento de JSON Patch para o recurso e o resultado da aplicação de operações patch.</span><span class="sxs-lookup"><span data-stu-id="dbf91-231">For example, the following JSON documents represent a resource, a JSON patch document for the resource, and the result of applying the patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="dbf91-232">Exemplo de recurso</span><span class="sxs-lookup"><span data-stu-id="dbf91-232">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="dbf91-233">Exemplo de JSON Patch</span><span class="sxs-lookup"><span data-stu-id="dbf91-233">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="dbf91-234">No JSON anterior:</span><span class="sxs-lookup"><span data-stu-id="dbf91-234">In the preceding JSON:</span></span>

* <span data-ttu-id="dbf91-235">A propriedade `op` indica o tipo de operação.</span><span class="sxs-lookup"><span data-stu-id="dbf91-235">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="dbf91-236">A propriedade `path` indica o elemento a ser atualizado.</span><span class="sxs-lookup"><span data-stu-id="dbf91-236">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="dbf91-237">A propriedade `value` fornece o novo valor.</span><span class="sxs-lookup"><span data-stu-id="dbf91-237">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="dbf91-238">Recurso depois do patch</span><span class="sxs-lookup"><span data-stu-id="dbf91-238">Resource after patch</span></span>

<span data-ttu-id="dbf91-239">Este é o recurso após a aplicação do documento JSON Patch anterior:</span><span class="sxs-lookup"><span data-stu-id="dbf91-239">Here's the resource after applying the preceding JSON Patch document:</span></span>

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

<span data-ttu-id="dbf91-240">As alterações feitas ao aplicar um documento JSON Patch a um recurso são atômicas: se alguma operação da lista falhar, nenhuma operação da lista será aplicada.</span><span class="sxs-lookup"><span data-stu-id="dbf91-240">The changes made by applying a JSON Patch document to a resource are atomic: if any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="dbf91-241">Sintaxe de path</span><span class="sxs-lookup"><span data-stu-id="dbf91-241">Path syntax</span></span>

<span data-ttu-id="dbf91-242">A propriedade [path](https://tools.ietf.org/html/rfc6901) de um objeto de operação tem barras entre os níveis.</span><span class="sxs-lookup"><span data-stu-id="dbf91-242">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="dbf91-243">Por exemplo, `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="dbf91-243">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="dbf91-244">Índices baseados em zero são usados para especificar os elementos da matriz.</span><span class="sxs-lookup"><span data-stu-id="dbf91-244">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="dbf91-245">O primeiro elemento da matriz `addresses` estaria em `/addresses/0`.</span><span class="sxs-lookup"><span data-stu-id="dbf91-245">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="dbf91-246">Para `add` ao final de uma matriz, use um hífen (-) ao invés de um número de índice: `/addresses/-`.</span><span class="sxs-lookup"><span data-stu-id="dbf91-246">To `add` to the end of an array, use a hyphen (-) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="dbf91-247">Operações</span><span class="sxs-lookup"><span data-stu-id="dbf91-247">Operations</span></span>

<span data-ttu-id="dbf91-248">A tabela a seguir mostra operações compatíveis conforme definido na [especificação de JSON Patch](https://tools.ietf.org/html/rfc6902):</span><span class="sxs-lookup"><span data-stu-id="dbf91-248">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="dbf91-249">Operação</span><span class="sxs-lookup"><span data-stu-id="dbf91-249">Operation</span></span>  | <span data-ttu-id="dbf91-250">{1&gt;Observações&lt;1}</span><span class="sxs-lookup"><span data-stu-id="dbf91-250">Notes</span></span> |
|-----------|--------------------------------|
| `add`     | <span data-ttu-id="dbf91-251">Adicione uma propriedade ou elemento de matriz.</span><span class="sxs-lookup"><span data-stu-id="dbf91-251">Add a property or array element.</span></span> <span data-ttu-id="dbf91-252">Para a propriedade existente: defina o valor.</span><span class="sxs-lookup"><span data-stu-id="dbf91-252">For existing property: set value.</span></span>|
| `remove`  | <span data-ttu-id="dbf91-253">Remova uma propriedade ou elemento de matriz.</span><span class="sxs-lookup"><span data-stu-id="dbf91-253">Remove a property or array element.</span></span> |
| `replace` | <span data-ttu-id="dbf91-254">É o mesmo que `remove`, seguido por `add` no mesmo local.</span><span class="sxs-lookup"><span data-stu-id="dbf91-254">Same as `remove` followed by `add` at same location.</span></span> |
| `move`    | <span data-ttu-id="dbf91-255">É o mesmo que `remove` da origem, seguido por `add` ao destino usando um valor da origem.</span><span class="sxs-lookup"><span data-stu-id="dbf91-255">Same as `remove` from source followed by `add` to destination using value from source.</span></span> |
| `copy`    | <span data-ttu-id="dbf91-256">É o mesmo que `add` ao destino usando um valor da origem.</span><span class="sxs-lookup"><span data-stu-id="dbf91-256">Same as `add` to destination using value from source.</span></span> |
| `test`    | <span data-ttu-id="dbf91-257">Retorna o código de status de êxito se o valor em `path` é igual ao `value` fornecido.</span><span class="sxs-lookup"><span data-stu-id="dbf91-257">Return success status code if value at `path` = provided `value`.</span></span>|

## <a name="jsonpatch-in-aspnet-core"></a><span data-ttu-id="dbf91-258">JsonPatch em ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dbf91-258">JsonPatch in ASP.NET Core</span></span>

<span data-ttu-id="dbf91-259">A implementação do ASP.NET Core de JSON Patch é fornecida no pacote do NuGet [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/).</span><span class="sxs-lookup"><span data-stu-id="dbf91-259">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span> <span data-ttu-id="dbf91-260">O pacote está incluso no metapacote [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="dbf91-260">The package is included in the [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app) metapackage.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="dbf91-261">Código do método de ação</span><span class="sxs-lookup"><span data-stu-id="dbf91-261">Action method code</span></span>

<span data-ttu-id="dbf91-262">Em um controlador de API, um método de ação para JSON Patch:</span><span class="sxs-lookup"><span data-stu-id="dbf91-262">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="dbf91-263">É anotado com o atributo `HttpPatch`.</span><span class="sxs-lookup"><span data-stu-id="dbf91-263">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="dbf91-264">Aceita um `JsonPatchDocument<T>`, normalmente com `[FromBody]`.</span><span class="sxs-lookup"><span data-stu-id="dbf91-264">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="dbf91-265">Chama `ApplyTo` no documento de patch para aplicar as alterações.</span><span class="sxs-lookup"><span data-stu-id="dbf91-265">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="dbf91-266">Aqui está um exemplo:</span><span class="sxs-lookup"><span data-stu-id="dbf91-266">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="dbf91-267">Esse código do aplicativo de exemplo funciona com o seguinte modelo `Customer`.</span><span class="sxs-lookup"><span data-stu-id="dbf91-267">This code from the sample app works with the following `Customer` model.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="dbf91-268">O exemplo de método de ação:</span><span class="sxs-lookup"><span data-stu-id="dbf91-268">The sample action method:</span></span>

* <span data-ttu-id="dbf91-269">Constrói um `Customer`.</span><span class="sxs-lookup"><span data-stu-id="dbf91-269">Constructs a `Customer`.</span></span>
* <span data-ttu-id="dbf91-270">Aplica o patch.</span><span class="sxs-lookup"><span data-stu-id="dbf91-270">Applies the patch.</span></span>
* <span data-ttu-id="dbf91-271">Retorna o resultado no corpo da resposta.</span><span class="sxs-lookup"><span data-stu-id="dbf91-271">Returns the result in the body of the response.</span></span>

 <span data-ttu-id="dbf91-272">Em um aplicativo real, o código recuperaria os dados de um repositório, como um banco de dados, e atualizaria o banco de dados após a aplicação do patch.</span><span class="sxs-lookup"><span data-stu-id="dbf91-272">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="dbf91-273">Estado do modelo</span><span class="sxs-lookup"><span data-stu-id="dbf91-273">Model state</span></span>

<span data-ttu-id="dbf91-274">O exemplo de método de ação anterior chama uma sobrecarga de `ApplyTo` que utiliza o estado do modelo como um de seus parâmetros.</span><span class="sxs-lookup"><span data-stu-id="dbf91-274">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="dbf91-275">Com essa opção, você pode receber mensagens de erro nas respostas.</span><span class="sxs-lookup"><span data-stu-id="dbf91-275">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="dbf91-276">O exemplo a seguir mostra o corpo de uma resposta 400 Solicitação Incorreta para uma operação `test`:</span><span class="sxs-lookup"><span data-stu-id="dbf91-276">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="dbf91-277">Objetos dinâmicos</span><span class="sxs-lookup"><span data-stu-id="dbf91-277">Dynamic objects</span></span>

<span data-ttu-id="dbf91-278">O exemplo de método de ação a seguir mostra como aplicar um patch a um objeto dinâmico.</span><span class="sxs-lookup"><span data-stu-id="dbf91-278">The following action method example shows how to apply a patch to a dynamic object.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="dbf91-279">A operação add</span><span class="sxs-lookup"><span data-stu-id="dbf91-279">The add operation</span></span>

* <span data-ttu-id="dbf91-280">Se `path` aponta para um elemento de matriz: insere um novo elemento antes do especificado por `path`.</span><span class="sxs-lookup"><span data-stu-id="dbf91-280">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="dbf91-281">Se `path` aponta para uma propriedade: define o valor da propriedade.</span><span class="sxs-lookup"><span data-stu-id="dbf91-281">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="dbf91-282">Se `path` aponta para um local não existente:</span><span class="sxs-lookup"><span data-stu-id="dbf91-282">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="dbf91-283">Se o recurso no qual fazer patch é um objeto dinâmico: adiciona uma propriedade.</span><span class="sxs-lookup"><span data-stu-id="dbf91-283">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="dbf91-284">Se o recurso no qual fazer patch é um objeto estático: a solicitação falha.</span><span class="sxs-lookup"><span data-stu-id="dbf91-284">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="dbf91-285">O exemplo de documento de patch a seguir define o valor de `CustomerName` e adiciona um objeto `Order` ao final da matriz `Orders`.</span><span class="sxs-lookup"><span data-stu-id="dbf91-285">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="dbf91-286">A operação remove</span><span class="sxs-lookup"><span data-stu-id="dbf91-286">The remove operation</span></span>

* <span data-ttu-id="dbf91-287">Se `path` aponta para um elemento de matriz: remove o elemento.</span><span class="sxs-lookup"><span data-stu-id="dbf91-287">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="dbf91-288">Se `path` aponta para uma propriedade:</span><span class="sxs-lookup"><span data-stu-id="dbf91-288">If `path` points to a property:</span></span>
  * <span data-ttu-id="dbf91-289">Se o recurso no qual fazer patch é um objeto dinâmico: remove a propriedade.</span><span class="sxs-lookup"><span data-stu-id="dbf91-289">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="dbf91-290">Se o recurso no qual fazer patch é um objeto estático:</span><span class="sxs-lookup"><span data-stu-id="dbf91-290">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="dbf91-291">Se a propriedade é anulável: define como nulo.</span><span class="sxs-lookup"><span data-stu-id="dbf91-291">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="dbf91-292">Se a propriedade não é anulável: define como `default<T>`.</span><span class="sxs-lookup"><span data-stu-id="dbf91-292">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="dbf91-293">O exemplo de documento de patch a seguir define `CustomerName` como nulo e exclui `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="dbf91-293">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="dbf91-294">A operação replace</span><span class="sxs-lookup"><span data-stu-id="dbf91-294">The replace operation</span></span>

<span data-ttu-id="dbf91-295">Esta operação é funcionalmente a mesma que `remove` seguida por `add`.</span><span class="sxs-lookup"><span data-stu-id="dbf91-295">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="dbf91-296">O exemplo de documento de patch a seguir define o valor de `CustomerName` e substitui `Orders[0]` por um novo objeto `Order`.</span><span class="sxs-lookup"><span data-stu-id="dbf91-296">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="dbf91-297">A operação move</span><span class="sxs-lookup"><span data-stu-id="dbf91-297">The move operation</span></span>

* <span data-ttu-id="dbf91-298">Se `path` aponta para um elemento de matriz: copia o elemento `from` para o local do elemento `path` e, em seguida, executa uma operação `remove` no elemento `from`.</span><span class="sxs-lookup"><span data-stu-id="dbf91-298">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="dbf91-299">Se `path` aponta para uma propriedade: copia o valor da propriedade `from` para a propriedade `path`, depois executa uma operação `remove` na propriedade `from`.</span><span class="sxs-lookup"><span data-stu-id="dbf91-299">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="dbf91-300">Se `path` aponta para uma propriedade não existente:</span><span class="sxs-lookup"><span data-stu-id="dbf91-300">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="dbf91-301">Se o recurso no qual fazer patch é um objeto estático: a solicitação falha.</span><span class="sxs-lookup"><span data-stu-id="dbf91-301">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="dbf91-302">Se o recurso no qual fazer patch é um objeto dinâmico: copia a propriedade `from` para o local indicado por `path` e, em seguida, executa uma operação `remove` na propriedade `from`.</span><span class="sxs-lookup"><span data-stu-id="dbf91-302">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="dbf91-303">O seguinte exemplo de documento de patch:</span><span class="sxs-lookup"><span data-stu-id="dbf91-303">The following sample patch document:</span></span>

* <span data-ttu-id="dbf91-304">Copia o valor de `Orders[0].OrderName` para `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="dbf91-304">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="dbf91-305">Define `Orders[0].OrderName` como nulo.</span><span class="sxs-lookup"><span data-stu-id="dbf91-305">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="dbf91-306">Move `Orders[1]` para antes de `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="dbf91-306">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="dbf91-307">A operação copy</span><span class="sxs-lookup"><span data-stu-id="dbf91-307">The copy operation</span></span>

<span data-ttu-id="dbf91-308">Esta operação é funcionalmente a mesma que uma operação `move`, sem a etapa final `remove`.</span><span class="sxs-lookup"><span data-stu-id="dbf91-308">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="dbf91-309">O seguinte exemplo de documento de patch:</span><span class="sxs-lookup"><span data-stu-id="dbf91-309">The following sample patch document:</span></span>

* <span data-ttu-id="dbf91-310">Copia o valor de `Orders[0].OrderName` para `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="dbf91-310">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="dbf91-311">Insere uma cópia de `Orders[1]` antes de `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="dbf91-311">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="dbf91-312">A operação test</span><span class="sxs-lookup"><span data-stu-id="dbf91-312">The test operation</span></span>

<span data-ttu-id="dbf91-313">Se o valor no local indicado por `path` for diferente do valor fornecido em `value`, a solicitação falhará.</span><span class="sxs-lookup"><span data-stu-id="dbf91-313">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="dbf91-314">Nesse caso, toda a solicitação de PATCH falhará, mesmo se todas as outras operações no documento de patch forem bem-sucedidas.</span><span class="sxs-lookup"><span data-stu-id="dbf91-314">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="dbf91-315">A operação `test` normalmente é usada para impedir uma atualização quando há um conflito de simultaneidade.</span><span class="sxs-lookup"><span data-stu-id="dbf91-315">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="dbf91-316">O seguinte exemplo de documento de patch não terá nenhum efeito se o valor inicial de `CustomerName` for "John", porque o teste falha:</span><span class="sxs-lookup"><span data-stu-id="dbf91-316">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="dbf91-317">Obter o código</span><span class="sxs-lookup"><span data-stu-id="dbf91-317">Get the code</span></span>

<span data-ttu-id="dbf91-318">[Exibir ou baixar o código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span><span class="sxs-lookup"><span data-stu-id="dbf91-318">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span></span> <span data-ttu-id="dbf91-319">([Como baixar](xref:index#how-to-download-a-sample).)</span><span class="sxs-lookup"><span data-stu-id="dbf91-319">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="dbf91-320">Para testar o exemplo, execute o aplicativo e envie solicitações HTTP com as seguintes configurações:</span><span class="sxs-lookup"><span data-stu-id="dbf91-320">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="dbf91-321">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="dbf91-321">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="dbf91-322">Método HTTP: `PATCH`</span><span class="sxs-lookup"><span data-stu-id="dbf91-322">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="dbf91-323">Cabeçalho: `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="dbf91-323">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="dbf91-324">Corpo: Copie e cole um dos exemplos de documento de patch JSON da pasta de projeto *JSON* .</span><span class="sxs-lookup"><span data-stu-id="dbf91-324">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dbf91-325">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="dbf91-325">Additional resources</span></span>

* [<span data-ttu-id="dbf91-326">Especificação do método PATCH IETF RFC 5789</span><span class="sxs-lookup"><span data-stu-id="dbf91-326">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="dbf91-327">Especificação do JSON Patch IETF RFC 6902</span><span class="sxs-lookup"><span data-stu-id="dbf91-327">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="dbf91-328">Especificação de formato de caminho do JSON Patch IETF RFC 6901</span><span class="sxs-lookup"><span data-stu-id="dbf91-328">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="dbf91-329">[Documentação do JSON Patch](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="dbf91-329">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="dbf91-330">Inclui links para recursos a fim de criar documentos de JSON Patch.</span><span class="sxs-lookup"><span data-stu-id="dbf91-330">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="dbf91-331">Código-fonte de JSON Patch do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dbf91-331">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end
