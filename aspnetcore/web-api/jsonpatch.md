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
# <a name="jsonpatch-in-aspnet-core-web-api"></a>JsonPatch na API Web do ASP.NET Core

Por [Tom Dykstra](https://github.com/tdykstra)

Este artigo explica como lidar com solicitações de JSON Patch em uma API Web do ASP.NET Core.

## <a name="patch-http-request-method"></a>Método de solicitação HTTP PATCH

Os métodos PUT e [PATCH](https://tools.ietf.org/html/rfc5789) são usados para atualizar um recurso existente. A diferença entre eles é que PUT substitui o recurso inteiro, enquanto PATCH especifica apenas as alterações.

## <a name="json-patch"></a>JSON Patch

[JSON Patch](https://tools.ietf.org/html/rfc6902) é um formato para especificar as atualizações a serem aplicadas a um recurso. Um documento de JSON Patch tem uma matriz de *operações*. Cada operação identifica um tipo específico de alteração, como adicionar um elemento de matriz ou substituir um valor de propriedade.

Por exemplo, os documentos JSON a seguir representam um recurso, um documento de JSON Patch para o recurso e o resultado da aplicação de operações patch.

### <a name="resource-example"></a>Exemplo de recurso

[!code-csharp[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a>Exemplo de JSON Patch

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

No JSON anterior:

* A propriedade `op` indica o tipo de operação.
* A propriedade `path` indica o elemento a ser atualizado.
* A propriedade `value` fornece o novo valor.

### <a name="resource-after-patch"></a>Recurso depois do patch

Este é o recurso após a aplicação do documento JSON Patch anterior:

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

As alterações feitas ao aplicar um documento JSON Patch a um recurso são atômicas: se alguma operação da lista falhar, nenhuma operação da lista será aplicada.

## <a name="path-syntax"></a>Sintaxe de path

A propriedade [path](http://tools.ietf.org/html/rfc6901) de um objeto de operação tem barras entre os níveis. Por exemplo, `"/address/zipCode"`.

Índices baseados em zero são usados para especificar os elementos da matriz. O primeiro elemento da matriz `addresses` estaria em `/addresses/0`. Para `add` ao final de uma matriz, use um hífen (-) ao invés de um número de índice: `/addresses/-`.

### <a name="operations"></a>Operações

A tabela a seguir mostra operações compatíveis conforme definido na [especificação de JSON Patch](https://tools.ietf.org/html/rfc6902):

|Operação  | Observações |
|-----------|--------------------------------|
| `add`     | Adicione uma propriedade ou elemento de matriz. Para a propriedade existente: defina o valor.|
| `remove`  | Remova uma propriedade ou elemento de matriz. |
| `replace` | É o mesmo que `remove`, seguido por `add` no mesmo local. |
| `move`    | É o mesmo que `remove` da origem, seguido por `add` ao destino usando um valor da origem. |
| `copy`    | É o mesmo que `add` ao destino usando um valor da origem. |
| `test`    | Retorna o código de status de êxito se o valor em `path` é igual ao `value` fornecido.|

## <a name="jsonpatch-in-aspnet-core"></a>JsonPatch em ASP.NET Core

A implementação do ASP.NET Core de JSON Patch é fornecida no pacote do NuGet [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/). O pacote está incluso no metapacote [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app).

## <a name="action-method-code"></a>Código do método de ação

Em um controlador de API, um método de ação para JSON Patch:

* É anotado com o atributo `HttpPatch`.
* Aceita um `JsonPatchDocument<T>`, normalmente com [FromBody].
* Chama `ApplyTo` no documento de patch para aplicar as alterações.

Veja um exemplo:

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

Esse código do aplicativo de exemplo funciona com o seguinte modelo `Customer`.

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

O exemplo de método de ação:

* Constrói um `Customer`.
* Aplica o patch.
* Retorna o resultado no corpo da resposta.

 Em um aplicativo real, o código recuperaria os dados de um repositório, como um banco de dados, e atualizaria o banco de dados após a aplicação do patch.

### <a name="model-state"></a>Estado do modelo

O exemplo de método de ação anterior chama uma sobrecarga de `ApplyTo` que utiliza o estado do modelo como um de seus parâmetros. Com essa opção, você pode receber mensagens de erro nas respostas. O exemplo a seguir mostra o corpo de uma resposta 400 Solicitação Incorreta para uma operação `test`:

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a>Objetos dinâmicos

O exemplo de método de ação a seguir mostra como aplicar um patch a um objeto dinâmico.

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a>A operação add

* Se `path` aponta para um elemento de matriz: insere um novo elemento antes do especificado por `path`.
* Se `path` aponta para uma propriedade: define o valor da propriedade.
* Se `path` aponta para um local não existente:
  * Se o recurso no qual fazer patch é um objeto dinâmico: adiciona uma propriedade.
  * Se o recurso no qual fazer patch é um objeto estático: a solicitação falha.

O exemplo de documento de patch a seguir define o valor de `CustomerName` e adiciona um objeto `Order` ao final da matriz `Orders`.

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a>A operação remove

* Se `path` aponta para um elemento de matriz: remove o elemento.
* Se `path` aponta para uma propriedade:
  * Se o recurso no qual fazer patch é um objeto dinâmico: remove a propriedade.
  * Se o recurso no qual fazer patch é um objeto estático: 
    * Se a propriedade é anulável: define como nulo.
    * Se a propriedade não é anulável: define como `default<T>`.

O exemplo de documento de patch a seguir define `CustomerName` como nulo e exclui `Orders[0]`.

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a>A operação replace

Esta operação é funcionalmente a mesma que `remove` seguida por `add`.

O exemplo de documento de patch a seguir define o valor de `CustomerName` e substitui `Orders[0]` por um novo objeto `Order`.

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a>A operação move

* Se `path` aponta para um elemento de matriz: copia o elemento `from` para o local do elemento `path` e, em seguida, executa uma operação `remove` no elemento `from`.
* Se `path` aponta para uma propriedade: copia o valor da propriedade `from` para a propriedade `path`, depois executa uma operação `remove` na propriedade `from`.
* Se `path` aponta para uma propriedade não existente:
  * Se o recurso no qual fazer patch é um objeto estático: a solicitação falha.
  * Se o recurso no qual fazer patch é um objeto dinâmico: copia a propriedade `from` para o local indicado por `path` e, em seguida, executa uma operação `remove` na propriedade `from`.

O seguinte exemplo de documento de patch:

* Copia o valor de `Orders[0].OrderName` para `CustomerName`.
* Define `Orders[0].OrderName` como nulo.
* Move `Orders[1]` para antes de `Orders[0]`.

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a>A operação copy

Esta operação é funcionalmente a mesma que uma operação `move`, sem a etapa final `remove`.

O seguinte exemplo de documento de patch:

* Copia o valor de `Orders[0].OrderName` para `CustomerName`.
* Insere uma cópia de `Orders[1]` antes de `Orders[0]`.

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a>A operação test

Se o valor no local indicado por `path` for diferente do valor fornecido em `value`, a solicitação falhará. Nesse caso, toda a solicitação de PATCH falhará, mesmo se todas as outras operações no documento de patch forem bem-sucedidas.

A operação `test` normalmente é usada para impedir uma atualização quando há um conflito de simultaneidade.

O seguinte exemplo de documento de patch não terá nenhum efeito se o valor inicial de `CustomerName` for "John", porque o teste falha:

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a>Obter o código

[Exibir ou baixar o código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2). ([Como baixar](xref:index#how-to-download-a-sample).)

Para testar o exemplo, execute o aplicativo e envie solicitações HTTP com as seguintes configurações:

* URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`
* Método HTTP: `PATCH`
* Cabeçalho: `Content-Type: application/json-patch+json`
* Corpo: Copie e cole um dos exemplos de documento de JSON Patch da pasta de projeto *JSON*.

## <a name="additional-resources"></a>Recursos adicionais

* [Especificação do método PATCH IETF RFC 5789](https://tools.ietf.org/html/rfc5789)
* [Especificação do JSON Patch IETF RFC 6902](https://tools.ietf.org/html/rfc6902)
* [Especificação de formato de caminho do JSON Patch IETF RFC 6901](http://tools.ietf.org/html/rfc6901)
* [Documentação do JSON Patch](http://jsonpatch.com/). Inclui links para recursos a fim de criar documentos de JSON Patch.
* [Código-fonte de JSON Patch do ASP.NET Core](https://github.com/aspnet/AspNetCore/tree/master/src/Features/JsonPatch/src)
