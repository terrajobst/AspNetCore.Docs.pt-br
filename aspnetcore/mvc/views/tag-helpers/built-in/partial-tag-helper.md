---
title: Auxiliar de marca parcial no ASP.NET Core
author: scottaddie
description: Descubra o auxiliar de marca parcial do ASP.NET Core e a função de cada um de seus atributos na renderização de uma exibição parcial.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 04/06/2019
uid: mvc/views/tag-helpers/builtin-th/partial-tag-helper
ms.openlocfilehash: 269be9ece674b39d03cb50720f4fb182c565a639
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78659645"
---
# <a name="partial-tag-helper-in-aspnet-core"></a>Auxiliar de marca parcial no ASP.NET Core

Por [Scott Addie](https://github.com/scottaddie)

Para obter uma visão geral dos Auxiliares de Marca, confira <xref:mvc/views/tag-helpers/intro>.

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([como baixar](xref:index#how-to-download-a-sample))

## <a name="overview"></a>Visão geral

O auxiliar de marca parcial é usado para renderizar uma [exibição parcial](xref:mvc/views/partial) em Páginas Razor e em aplicativos MVC. Considere que ele:

* Exige o ASP.NET Core 2.1 ou posterior.
* É uma alternativa à [sintaxe do HTML Helper](xref:mvc/views/partial#reference-a-partial-view).
* Renderiza a exibição parcial de forma assíncrona.

As opções do HTML Helper para renderizar uma exibição parcial incluem:

* [`@await Html.PartialAsync`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.partialasync)
* [`@await Html.RenderPartialAsync`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.renderpartialasync)
* [`@Html.Partial`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.partial)
* [`@Html.RenderPartial`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.renderpartial)

O modelo *Product* é usado nos exemplos ao longo deste documento:

[!code-csharp[](samples/TagHelpersBuiltIn/Models/Product.cs)]

Segue um inventário dos atributos do auxiliar de marca parcial.

## <a name="name"></a>{1&gt;name&lt;1}

O atributo `name` é necessário. Indica o nome ou o caminho da exibição parcial a ser renderizada. Quando é fornecido um nome de exibição parcial, o processo [descoberta de exibição](xref:mvc/views/overview#view-discovery) é iniciado. Esse processo é ignorado quando um caminho explícito é fornecido. Para todos os valores de `name` aceitáveis, consulte [Descoberta de exibição parcial](xref:mvc/views/partial#partial-view-discovery).

A marcação a seguir usa um caminho explícito, indicando que *_ProductPartial.cshtml* deve ser carregado da pasta *Compartilhado*. Usando o atributo [for](#for), um modelo é passado para a exibição parcial para associação.

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_Name)]

## <a name="for"></a>for

O atributo `for` atribui uma [ModelExpression](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.modelexpression) a ser avaliada em relação ao modelo atual. Uma `ModelExpression` infere a sintaxe `@Model.`. Por exemplo, `for="Product"` pode ser usado em vez de `for="@Model.Product"`. Esse comportamento de inferência padrão é substituído usando o símbolo `@` para definir uma expressão embutida.

A seguinte marcação carrega *_ProductPartial.cshtml*:

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_For)]

A exibição parcial é associada à propriedade `Product` do modelo de página associado:

[!code-csharp[](samples/TagHelpersBuiltIn/Pages/Product.cshtml.cs?highlight=8)]

## <a name="model"></a>{1&gt;modelo&lt;1}

O atributo `model` atribui uma instância de modelo a ser passada para a exibição parcial. O atributo `model` não pode ser usado com o atributo [for](#for).

Na marcação a seguir, é criada uma nova instância do objeto `Product` que é passada ao atributo `model` para associação:

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_Model)]

## <a name="view-data"></a>view-data

O atributo `view-data` atribui um [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) a ser passado para a exibição parcial. A seguinte marcação faz com que toda a coleção ViewData fique acessível para a exibição parcial:

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_ViewData&highlight=5-)]

No código anterior, o valor da chave `IsNumberReadOnly` é definido como `true` e adicionado à coleção ViewData. Consequentemente, `ViewData["IsNumberReadOnly"]` se torna acessível dentro da exibição parcial a seguir:

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Shared/_ProductViewDataPartial.cshtml?highlight=5)]

Neste exemplo, o valor de `ViewData["IsNumberReadOnly"]` determina se o campo *Número* é exibido como somente leitura.

## <a name="migrate-from-an-html-helper"></a>Migrar de um auxiliar HTML

Considere o seguinte exemplo de auxiliar HTML assíncrono. Uma coleção de produtos é iterada e exibida. Segundo o primeiro parâmetro do método `PartialAsync`, a exibição parcial *_ProductPartial.cshtml* é carregada. Uma instância do modelo `Product` é passada para a exibição parcial para associação.

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Products.cshtml?name=snippet_HtmlHelper&highlight=3)]

O auxiliar de marca parcial seguir alcança o mesmo comportamento de renderização assíncrona que o auxiliar HTML `PartialAsync`. Uma instância de modelo `model` é atribuída ao atributo `Product` para associação à exibição parcial.

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Products.cshtml?name=snippet_TagHelper&highlight=3)]

## <a name="additional-resources"></a>Recursos adicionais

* <xref:mvc/views/partial>
* <xref:mvc/views/overview#weakly-typed-data-viewdata-viewdata-attribute-and-viewbag>
