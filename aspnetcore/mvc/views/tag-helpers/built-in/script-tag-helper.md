---
title: Auxiliar de marca de script no ASP.NET Core
author: rick-anderson
ms.author: riande
description: Descubra os atributos auxiliares de marca de script ASP.NET Core e a função que cada atributo desempenha ao estender o comportamento da marca de script HTML.
ms.custom: mvc
ms.date: 12/18/2018
uid: mvc/views/tag-helpers/builtin-th/script-tag-helper
ms.openlocfilehash: c3d9148bd62dcc045873cc3a72884ae458349d70
ms.sourcegitcommit: 3e503ef510008e77be6dd82ee79213c9f7b97607
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74317116"
---
# <a name="script-tag-helper-in-aspnet-core"></a><span data-ttu-id="3c4ef-103">Auxiliar de marca de script no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3c4ef-103">Script Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="3c4ef-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3c4ef-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="3c4ef-105">O [auxiliar de marca de script](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) gera um link para um arquivo de script primário ou de retorno.</span><span class="sxs-lookup"><span data-stu-id="3c4ef-105">The [Script Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) generates a link to a primary or fall back script file.</span></span> <span data-ttu-id="3c4ef-106">Normalmente, o arquivo de script primário está em uma CDN ( [rede de distribuição de conteúdo](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) ).</span><span class="sxs-lookup"><span data-stu-id="3c4ef-106">Typically the primary script file is on a [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).</span></span>

[!INCLUDE[](~/includes/cdn.md)]

<span data-ttu-id="3c4ef-107">O auxiliar de marca de script permite que você especifique uma CDN para o arquivo de script e um fallback quando a CDN não estiver disponível.</span><span class="sxs-lookup"><span data-stu-id="3c4ef-107">The Script Tag Helper allows you to specify a CDN for the script file and a fallback when the CDN is not available.</span></span> <span data-ttu-id="3c4ef-108">O auxiliar de marca de script fornece a vantagem de desempenho de uma CDN com a robustez da hospedagem local.</span><span class="sxs-lookup"><span data-stu-id="3c4ef-108">The Script Tag Helper provides the performance advantage of a CDN with the robustness of local hosting.</span></span>

<span data-ttu-id="3c4ef-109">A marcação Razor a seguir mostra um elemento `script` com um fallback:</span><span class="sxs-lookup"><span data-stu-id="3c4ef-109">The following Razor markup shows a `script` element with a fallback:</span></span>

```HTML
<script src="https://ajax.aspnetcdn.com/ajax/jquery/jquery-3.3.1.min.js"
        asp-fallback-src="~/lib/jquery/dist/jquery.min.js"
        asp-fallback-test="window.jQuery"
        crossorigin="anonymous"
        integrity="sha384-tsQFqpEReu7ZLhBV2VZlAu7zcOV+rXbYlF2cqB8txI/8aZajjp4Bqd+V6D5IgvKT">
</script>
```

## <a name="commonly-used-script-tag-helper-attributes"></a><span data-ttu-id="3c4ef-110">Atributos auxiliares de marca de script comumente usados</span><span class="sxs-lookup"><span data-stu-id="3c4ef-110">Commonly used Script Tag Helper attributes</span></span>

<span data-ttu-id="3c4ef-111">Consulte [auxiliar de marca de script](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) para todos os atributos, propriedades e métodos auxiliares de marca de script.</span><span class="sxs-lookup"><span data-stu-id="3c4ef-111">See [Script Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) for all the Script Tag Helper attributes, properties, and methods.</span></span>

### <a name="asp-fallback-test"></a><span data-ttu-id="3c4ef-112">ASP – teste de fallback</span><span class="sxs-lookup"><span data-stu-id="3c4ef-112">asp-fallback-test</span></span>

<span data-ttu-id="3c4ef-113">O método de script definido no script primário a ser usado para o teste de fallback.</span><span class="sxs-lookup"><span data-stu-id="3c4ef-113">The script method defined in the primary script to use for the fallback test.</span></span> <span data-ttu-id="3c4ef-114">Para obter mais informações, consulte <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackTestExpression>.</span><span class="sxs-lookup"><span data-stu-id="3c4ef-114">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackTestExpression>.</span></span>

### <a name="asp-fallback-src"></a><span data-ttu-id="3c4ef-115">ASP-fallback-src</span><span class="sxs-lookup"><span data-stu-id="3c4ef-115">asp-fallback-src</span></span>

<span data-ttu-id="3c4ef-116">A URL de uma marca de script para fazer fallback no caso do primário falhar.</span><span class="sxs-lookup"><span data-stu-id="3c4ef-116">The URL of a Script tag to fallback to in the case the primary one fails.</span></span> <span data-ttu-id="3c4ef-117">Para obter mais informações, consulte <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackSrc>.</span><span class="sxs-lookup"><span data-stu-id="3c4ef-117">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackSrc>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3c4ef-118">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="3c4ef-118">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
