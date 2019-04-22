---
title: Auxiliar de marca parcial no ASP.NET Core
author: scottaddie
description: Descubra o auxiliar de marca parcial do ASP.NET Core e a função de cada um de seus atributos na renderização de uma exibição parcial.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 04/06/2019
uid: mvc/views/tag-helpers/builtin-th/partial-tag-helper
ms.openlocfilehash: 116fce7af5dc138fbbb0351a4f38f59e88c8f338
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59468676"
---
# <a name="partial-tag-helper-in-aspnet-core"></a><span data-ttu-id="64623-103">Auxiliar de marca parcial no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="64623-103">Partial Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="64623-104">Por [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="64623-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="64623-105">Para obter uma visão geral dos Auxiliares de Marca, confira <xref:mvc/views/tag-helpers/intro>.</span><span class="sxs-lookup"><span data-stu-id="64623-105">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="64623-106">[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="64623-106">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview"></a><span data-ttu-id="64623-107">Visão geral</span><span class="sxs-lookup"><span data-stu-id="64623-107">Overview</span></span>

<span data-ttu-id="64623-108">O auxiliar de marca parcial é usado para renderizar uma [exibição parcial](xref:mvc/views/partial) em Páginas Razor e em aplicativos MVC.</span><span class="sxs-lookup"><span data-stu-id="64623-108">The Partial Tag Helper is used for rendering a [partial view](xref:mvc/views/partial) in Razor Pages and MVC apps.</span></span> <span data-ttu-id="64623-109">Considere que ele:</span><span class="sxs-lookup"><span data-stu-id="64623-109">Consider that it:</span></span>

* <span data-ttu-id="64623-110">Exige o ASP.NET Core 2.1 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="64623-110">Requires ASP.NET Core 2.1 or later.</span></span>
* <span data-ttu-id="64623-111">É uma alternativa à [sintaxe do HTML Helper](xref:mvc/views/partial#reference-a-partial-view).</span><span class="sxs-lookup"><span data-stu-id="64623-111">Is an alternative to [HTML Helper syntax](xref:mvc/views/partial#reference-a-partial-view).</span></span>
* <span data-ttu-id="64623-112">Renderiza a exibição parcial de forma assíncrona.</span><span class="sxs-lookup"><span data-stu-id="64623-112">Renders the partial view asynchronously.</span></span>

<span data-ttu-id="64623-113">As opções do HTML Helper para renderizar uma exibição parcial incluem:</span><span class="sxs-lookup"><span data-stu-id="64623-113">The HTML Helper options for rendering a partial view include:</span></span>

* [<span data-ttu-id="64623-114">@await Html.PartialAsync</span><span class="sxs-lookup"><span data-stu-id="64623-114">@await Html.PartialAsync</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.partialasync)
* [<span data-ttu-id="64623-115">@await Html.RenderPartialAsync</span><span class="sxs-lookup"><span data-stu-id="64623-115">@await Html.RenderPartialAsync</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.renderpartialasync)
* [@Html.Partial](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.partial)
* [@Html.RenderPartial](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.renderpartial)

<span data-ttu-id="64623-116">O modelo *Product* é usado nos exemplos ao longo deste documento:</span><span class="sxs-lookup"><span data-stu-id="64623-116">The *Product* model is used in samples throughout this document:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Models/Product.cs)]

<span data-ttu-id="64623-117">Segue um inventário dos atributos do auxiliar de marca parcial.</span><span class="sxs-lookup"><span data-stu-id="64623-117">An inventory of the Partial Tag Helper attributes follows.</span></span>

## <a name="name"></a><span data-ttu-id="64623-118">name</span><span class="sxs-lookup"><span data-stu-id="64623-118">name</span></span>

<span data-ttu-id="64623-119">O atributo `name` é necessário.</span><span class="sxs-lookup"><span data-stu-id="64623-119">The `name` attribute is required.</span></span> <span data-ttu-id="64623-120">Indica o nome ou o caminho da exibição parcial a ser renderizada.</span><span class="sxs-lookup"><span data-stu-id="64623-120">It indicates the name or the path of the partial view to be rendered.</span></span> <span data-ttu-id="64623-121">Quando é fornecido um nome de exibição parcial, o processo [descoberta de exibição](xref:mvc/views/overview#view-discovery) é iniciado.</span><span class="sxs-lookup"><span data-stu-id="64623-121">When a partial view name is provided, the [view discovery](xref:mvc/views/overview#view-discovery) process is initiated.</span></span> <span data-ttu-id="64623-122">Esse processo é ignorado quando um caminho explícito é fornecido.</span><span class="sxs-lookup"><span data-stu-id="64623-122">That process is bypassed when an explicit path is provided.</span></span> <span data-ttu-id="64623-123">Para todos os valores de `name` aceitáveis, consulte [Descoberta de exibição parcial](xref:mvc/views/partial#partial-view-discovery).</span><span class="sxs-lookup"><span data-stu-id="64623-123">For all acceptable `name` values, see [Partial view discovery](xref:mvc/views/partial#partial-view-discovery).</span></span>

<span data-ttu-id="64623-124">A marcação a seguir usa um caminho explícito, indicando que *_ProductPartial.cshtml* deve ser carregado da pasta *Compartilhado*.</span><span class="sxs-lookup"><span data-stu-id="64623-124">The following markup uses an explicit path, indicating that *_ProductPartial.cshtml* is to be loaded from the *Shared* folder.</span></span> <span data-ttu-id="64623-125">Usando o atributo [for](#for), um modelo é passado para a exibição parcial para associação.</span><span class="sxs-lookup"><span data-stu-id="64623-125">Using the [for](#for) attribute, a model is passed to the partial view for binding.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_Name)]

## <a name="for"></a><span data-ttu-id="64623-126">for</span><span class="sxs-lookup"><span data-stu-id="64623-126">for</span></span>

<span data-ttu-id="64623-127">O atributo `for` atribui uma [ModelExpression](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.modelexpression) a ser avaliada em relação ao modelo atual.</span><span class="sxs-lookup"><span data-stu-id="64623-127">The `for` attribute assigns a [ModelExpression](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.modelexpression) to be evaluated against the current model.</span></span> <span data-ttu-id="64623-128">Uma `ModelExpression` infere a sintaxe `@Model.`.</span><span class="sxs-lookup"><span data-stu-id="64623-128">A `ModelExpression` infers the `@Model.` syntax.</span></span> <span data-ttu-id="64623-129">Por exemplo, `for="Product"` pode ser usado em vez de `for="@Model.Product"`.</span><span class="sxs-lookup"><span data-stu-id="64623-129">For example, `for="Product"` can be used instead of `for="@Model.Product"`.</span></span> <span data-ttu-id="64623-130">Esse comportamento de inferência padrão é substituído usando o símbolo `@` para definir uma expressão embutida.</span><span class="sxs-lookup"><span data-stu-id="64623-130">This default inference behavior is overridden by using the `@` symbol to define an inline expression.</span></span>

<span data-ttu-id="64623-131">A seguinte marcação carrega *_ProductPartial.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="64623-131">The following markup loads *_ProductPartial.cshtml*:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_For)]

<span data-ttu-id="64623-132">A exibição parcial é associada à propriedade `Product` do modelo de página associado:</span><span class="sxs-lookup"><span data-stu-id="64623-132">The partial view is bound to the associated page model's `Product` property:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Pages/Product.cshtml.cs?highlight=8)]

## <a name="model"></a><span data-ttu-id="64623-133">modelo</span><span class="sxs-lookup"><span data-stu-id="64623-133">model</span></span>

<span data-ttu-id="64623-134">O atributo `model` atribui uma instância de modelo a ser passada para a exibição parcial.</span><span class="sxs-lookup"><span data-stu-id="64623-134">The `model` attribute assigns a model instance to pass to the partial view.</span></span> <span data-ttu-id="64623-135">O atributo `model` não pode ser usado com o atributo [for](#for).</span><span class="sxs-lookup"><span data-stu-id="64623-135">The `model` attribute can't be used with the [for](#for) attribute.</span></span>

<span data-ttu-id="64623-136">Na marcação a seguir, é criada uma nova instância do objeto `Product` que é passada ao atributo `model` para associação:</span><span class="sxs-lookup"><span data-stu-id="64623-136">In the following markup, a new `Product` object is instantiated and passed to the `model` attribute for binding:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_Model)]

## <a name="view-data"></a><span data-ttu-id="64623-137">view-data</span><span class="sxs-lookup"><span data-stu-id="64623-137">view-data</span></span>

<span data-ttu-id="64623-138">O atributo `view-data` atribui um [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) a ser passado para a exibição parcial.</span><span class="sxs-lookup"><span data-stu-id="64623-138">The `view-data` attribute assigns a [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) to pass to the partial view.</span></span> <span data-ttu-id="64623-139">A seguinte marcação faz com que toda a coleção ViewData fique acessível para a exibição parcial:</span><span class="sxs-lookup"><span data-stu-id="64623-139">The following markup makes the entire ViewData collection accessible to the partial view:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_ViewData&highlight=5-)]

<span data-ttu-id="64623-140">No código anterior, o valor da chave `IsNumberReadOnly` é definido como `true` e adicionado à coleção ViewData.</span><span class="sxs-lookup"><span data-stu-id="64623-140">In the preceding code, the `IsNumberReadOnly` key value is set to `true` and added to the ViewData collection.</span></span> <span data-ttu-id="64623-141">Consequentemente, `ViewData["IsNumberReadOnly"]` se torna acessível dentro da exibição parcial a seguir:</span><span class="sxs-lookup"><span data-stu-id="64623-141">Consequently, `ViewData["IsNumberReadOnly"]` is made accessible within the following partial view:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Shared/_ProductViewDataPartial.cshtml?highlight=5)]

<span data-ttu-id="64623-142">Neste exemplo, o valor de `ViewData["IsNumberReadOnly"]` determina se o campo *Número* é exibido como somente leitura.</span><span class="sxs-lookup"><span data-stu-id="64623-142">In this example, the value of `ViewData["IsNumberReadOnly"]` determines whether the *Number* field is displayed as read only.</span></span>

## <a name="migrate-from-an-html-helper"></a><span data-ttu-id="64623-143">Migrar de um auxiliar HTML</span><span class="sxs-lookup"><span data-stu-id="64623-143">Migrate from an HTML Helper</span></span>

<span data-ttu-id="64623-144">Considere o seguinte exemplo de auxiliar HTML assíncrono.</span><span class="sxs-lookup"><span data-stu-id="64623-144">Consider the following asynchronous HTML Helper example.</span></span> <span data-ttu-id="64623-145">Uma coleção de produtos é iterada e exibida.</span><span class="sxs-lookup"><span data-stu-id="64623-145">A collection of products is iterated and displayed.</span></span> <span data-ttu-id="64623-146">Segundo o primeiro parâmetro do método `PartialAsync`, a exibição parcial *_ProductPartial.cshtml* é carregada.</span><span class="sxs-lookup"><span data-stu-id="64623-146">Per the `PartialAsync` method's first parameter, the *_ProductPartial.cshtml* partial view is loaded.</span></span> <span data-ttu-id="64623-147">Uma instância do modelo `Product` é passada para a exibição parcial para associação.</span><span class="sxs-lookup"><span data-stu-id="64623-147">An instance of the `Product` model is passed to the partial view for binding.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Products.cshtml?name=snippet_HtmlHelper&highlight=3)]

<span data-ttu-id="64623-148">O auxiliar de marca parcial seguir alcança o mesmo comportamento de renderização assíncrona que o auxiliar HTML `PartialAsync`.</span><span class="sxs-lookup"><span data-stu-id="64623-148">The following Partial Tag Helper achieves the same asynchronous rendering behavior as the `PartialAsync` HTML Helper.</span></span> <span data-ttu-id="64623-149">Uma instância de modelo `Product` é atribuída ao atributo `model` para associação à exibição parcial.</span><span class="sxs-lookup"><span data-stu-id="64623-149">The `model` attribute is assigned a `Product` model instance for binding to the partial view.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Products.cshtml?name=snippet_TagHelper&highlight=3)]

## <a name="additional-resources"></a><span data-ttu-id="64623-150">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="64623-150">Additional resources</span></span>

* <xref:mvc/views/partial>
* <xref:mvc/views/overview#weakly-typed-data-viewdata-viewdata-attribute-and-viewbag>
