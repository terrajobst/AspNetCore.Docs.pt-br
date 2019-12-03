---
title: Auxiliar de marca de script no ASP.NET Core
author: rick-anderson
ms.author: riande
description: Descubra os atributos auxiliares de marca de script ASP.NET Core e a função que cada atributo desempenha ao estender o comportamento da marca de script HTML.
ms.custom: mvc
ms.date: 12/02/2019
uid: mvc/views/tag-helpers/builtin-th/script-tag-helper
ms.openlocfilehash: 8a90eb5a74ff3f8178a47c59ad7ba1b6a389ab87
ms.sourcegitcommit: 3b6b0a54b20dc99b0c8c5978400c60adf431072f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74717371"
---
# <a name="script-tag-helper-in-aspnet-core"></a><span data-ttu-id="f7de6-103">Auxiliar de marca de script no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f7de6-103">Script Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="f7de6-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f7de6-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f7de6-105">O [auxiliar de marca de script](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) gera um link para um arquivo de script primário ou de retorno.</span><span class="sxs-lookup"><span data-stu-id="f7de6-105">The [Script Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) generates a link to a primary or fall back script file.</span></span> <span data-ttu-id="f7de6-106">Normalmente, o arquivo de script primário está em uma CDN ( [rede de distribuição de conteúdo](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) ).</span><span class="sxs-lookup"><span data-stu-id="f7de6-106">Typically the primary script file is on a [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).</span></span>

[!INCLUDE[](~/includes/cdn.md)]

<span data-ttu-id="f7de6-107">O auxiliar de marca de script permite que você especifique uma CDN para o arquivo de script e um fallback quando a CDN não estiver disponível.</span><span class="sxs-lookup"><span data-stu-id="f7de6-107">The Script Tag Helper allows you to specify a CDN for the script file and a fallback when the CDN is not available.</span></span> <span data-ttu-id="f7de6-108">O auxiliar de marca de script fornece a vantagem de desempenho de uma CDN com a robustez da hospedagem local.</span><span class="sxs-lookup"><span data-stu-id="f7de6-108">The Script Tag Helper provides the performance advantage of a CDN with the robustness of local hosting.</span></span>

<span data-ttu-id="f7de6-109">A marcação Razor a seguir mostra um elemento `script` com um fallback:</span><span class="sxs-lookup"><span data-stu-id="f7de6-109">The following Razor markup shows a `script` element with a fallback:</span></span>

```HTML
<script src="https://ajax.aspnetcdn.com/ajax/jquery/jquery-3.3.1.min.js"
        asp-fallback-src="~/lib/jquery/dist/jquery.min.js"
        asp-fallback-test="window.jQuery"
        crossorigin="anonymous"
        integrity="sha384-tsQFqpEReu7ZLhBV2VZlAu7zcOV+rXbYlF2cqB8txI/8aZajjp4Bqd+V6D5IgvKT">
</script>
```

<span data-ttu-id="f7de6-110">Não use a `<script>` atributo [Defer](https://developer.mozilla.org/docs/Web/HTML/Element/script) do elemento para adiar o carregamento do script CDN.</span><span class="sxs-lookup"><span data-stu-id="f7de6-110">Don't use the `<script>` element's [defer](https://developer.mozilla.org/docs/Web/HTML/Element/script) attribute to defer loading the CDN script.</span></span> <span data-ttu-id="f7de6-111">O auxiliar de marca de script processa o JavaScript que executa imediatamente a expressão [ASP-fallback-Test](#asp-fallback-test) .</span><span class="sxs-lookup"><span data-stu-id="f7de6-111">The Script Tag Helper renders JavaScript that immediately executes the [asp-fallback-test](#asp-fallback-test) expression.</span></span> <span data-ttu-id="f7de6-112">A expressão falhará se o carregamento do script CDN for adiado.</span><span class="sxs-lookup"><span data-stu-id="f7de6-112">The expression fails if loading the CDN script is deferred.</span></span>

## <a name="commonly-used-script-tag-helper-attributes"></a><span data-ttu-id="f7de6-113">Atributos auxiliares de marca de script comumente usados</span><span class="sxs-lookup"><span data-stu-id="f7de6-113">Commonly used Script Tag Helper attributes</span></span>

<span data-ttu-id="f7de6-114">Consulte [auxiliar de marca de script](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) para todos os atributos, propriedades e métodos auxiliares de marca de script.</span><span class="sxs-lookup"><span data-stu-id="f7de6-114">See [Script Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) for all the Script Tag Helper attributes, properties, and methods.</span></span>

### <a name="asp-fallback-test"></a><span data-ttu-id="f7de6-115">ASP – teste de fallback</span><span class="sxs-lookup"><span data-stu-id="f7de6-115">asp-fallback-test</span></span>

<span data-ttu-id="f7de6-116">O método de script definido no script primário a ser usado para o teste de fallback.</span><span class="sxs-lookup"><span data-stu-id="f7de6-116">The script method defined in the primary script to use for the fallback test.</span></span> <span data-ttu-id="f7de6-117">Para obter mais informações, consulte <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackTestExpression>.</span><span class="sxs-lookup"><span data-stu-id="f7de6-117">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackTestExpression>.</span></span>

### <a name="asp-fallback-src"></a><span data-ttu-id="f7de6-118">ASP-fallback-src</span><span class="sxs-lookup"><span data-stu-id="f7de6-118">asp-fallback-src</span></span>

<span data-ttu-id="f7de6-119">A URL de uma marca de script para fazer fallback no caso do primário falhar.</span><span class="sxs-lookup"><span data-stu-id="f7de6-119">The URL of a Script tag to fallback to in the case the primary one fails.</span></span> <span data-ttu-id="f7de6-120">Para obter mais informações, consulte <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackSrc>.</span><span class="sxs-lookup"><span data-stu-id="f7de6-120">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackSrc>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f7de6-121">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="f7de6-121">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
