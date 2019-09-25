---
title: Auxiliar de marca de script no ASP.NET Core
author: rick-anderson
ms.author: riande
description: Descubra os atributos auxiliares de marca de script ASP.NET Core e a função que cada atributo desempenha ao estender o comportamento da marca de script HTML.
ms.custom: mvc
ms.date: 12/18/2018
uid: mvc/views/tag-helpers/builtin-th/script-tag-helper
ms.openlocfilehash: 5f2fb8a45048804afa8aff2989cd53489e45a33b
ms.sourcegitcommit: fae6f0e253f9d62d8f39de5884d2ba2b4b2a6050
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71256494"
---
# <a name="script-tag-helper-in-aspnet-core"></a><span data-ttu-id="d31ac-103">Auxiliar de marca de script no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d31ac-103">Script Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="d31ac-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d31ac-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d31ac-105">O [auxiliar de marca de script](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) gera um link para um arquivo de script primário ou de retorno.</span><span class="sxs-lookup"><span data-stu-id="d31ac-105">The [Script Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) generates a link to a primary or fall back script file.</span></span> <span data-ttu-id="d31ac-106">Normalmente, o arquivo de script primário está em uma CDN ( [rede de distribuição de conteúdo](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) ).</span><span class="sxs-lookup"><span data-stu-id="d31ac-106">Typically the primary script file is on a [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).</span></span>

[!INCLUDE[](~/includes/cdn.md)]

<span data-ttu-id="d31ac-107">O auxiliar de marca de script permite que você especifique uma CDN para o arquivo de script e um fallback quando a CDN não estiver disponível.</span><span class="sxs-lookup"><span data-stu-id="d31ac-107">The Script Tag Helper allows you to specify a CDN for the script file and a fallback when the CDN is not available.</span></span> <span data-ttu-id="d31ac-108">O auxiliar de marca de script fornece a vantagem de desempenho de uma CDN com a robustez da hospedagem local.</span><span class="sxs-lookup"><span data-stu-id="d31ac-108">The Script Tag Helper provides the performance advantage of a CDN with the robustness of local hosting.</span></span>

<span data-ttu-id="d31ac-109">A marcação Razor a seguir mostra `script` o elemento de um arquivo de layout criado com o modelo de aplicativo Web ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="d31ac-109">The following Razor markup shows the `script` element of a layout file created with the ASP.NET Core web app template:</span></span>

[!code-html[](link-tag-helper/sample/_Layout.cshtml?name=snippet2)]

<span data-ttu-id="d31ac-110">O seguinte é semelhante ao HTML renderizado do código anterior (em um ambiente que não é de desenvolvimento):</span><span class="sxs-lookup"><span data-stu-id="d31ac-110">The following is similar to the rendered HTML from the preceding code (in a non-Development environment):</span></span>

[!code-csharp[](link-tag-helper/sample/HtmlPage2.html)]

<span data-ttu-id="d31ac-111">No código anterior, o auxiliar de marca de script gerou o segundo elemento `<script>  (window.jQuery || document.write(`script (), que testa `window.jQuery`o para.</span><span class="sxs-lookup"><span data-stu-id="d31ac-111">In the preceding code, the Script Tag Helper generated the second script ( `<script>  (window.jQuery || document.write(`) element, which tests for `window.jQuery`.</span></span> <span data-ttu-id="d31ac-112">Se `window.jQuery` não for encontrado, `document.write(` o executará e criará um script</span><span class="sxs-lookup"><span data-stu-id="d31ac-112">If `window.jQuery` is not found, `document.write(` runs and creates a script</span></span> 

## <a name="commonly-used-script-tag-helper-attributes"></a><span data-ttu-id="d31ac-113">Atributos auxiliares de marca de script comumente usados</span><span class="sxs-lookup"><span data-stu-id="d31ac-113">Commonly used Script Tag Helper attributes</span></span>

<span data-ttu-id="d31ac-114">Consulte [auxiliar de marca de script](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) para todos os atributos, propriedades e métodos auxiliares de marca de script.</span><span class="sxs-lookup"><span data-stu-id="d31ac-114">See [Script Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) for all the Script Tag Helper attributes, properties, and methods.</span></span>

### <a name="href"></a><span data-ttu-id="d31ac-115">{1&gt;href&lt;1}</span><span class="sxs-lookup"><span data-stu-id="d31ac-115">href</span></span>

<span data-ttu-id="d31ac-116">Endereço preferencial do recurso vinculado.</span><span class="sxs-lookup"><span data-stu-id="d31ac-116">Preferred address of the linked resource.</span></span> <span data-ttu-id="d31ac-117">O endereço é passado para o HTML gerado em todos os casos.</span><span class="sxs-lookup"><span data-stu-id="d31ac-117">The address is passed thought to the generated HTML in all cases.</span></span>

### <a name="asp-fallback-href"></a><span data-ttu-id="d31ac-118">ASP-fallback-href</span><span class="sxs-lookup"><span data-stu-id="d31ac-118">asp-fallback-href</span></span>

<span data-ttu-id="d31ac-119">A URL de uma folha de estilos CSS para fazer fallback no caso em que a URL primária falhar.</span><span class="sxs-lookup"><span data-stu-id="d31ac-119">The URL of a CSS stylesheet to fallback to in the case the primary URL fails.</span></span>

### <a name="asp-fallback-test-class"></a><span data-ttu-id="d31ac-120">ASP-fallback-Test-Class</span><span class="sxs-lookup"><span data-stu-id="d31ac-120">asp-fallback-test-class</span></span>

<span data-ttu-id="d31ac-121">O nome da classe definida na folha de estilos a ser usada para o teste de fallback.</span><span class="sxs-lookup"><span data-stu-id="d31ac-121">The class name defined in the stylesheet to use for the fallback test.</span></span> <span data-ttu-id="d31ac-122">Para obter mais informações, consulte <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestClass>.</span><span class="sxs-lookup"><span data-stu-id="d31ac-122">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestClass>.</span></span>

### <a name="asp-fallback-test-property"></a><span data-ttu-id="d31ac-123">ASP-fallback-Test-Property</span><span class="sxs-lookup"><span data-stu-id="d31ac-123">asp-fallback-test-property</span></span>

<span data-ttu-id="d31ac-124">O nome da propriedade CSS a ser usado para o teste de fallback.</span><span class="sxs-lookup"><span data-stu-id="d31ac-124">The CSS property name to use for the fallback test.</span></span> <span data-ttu-id="d31ac-125">Para obter mais informações, consulte <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestProperty>.</span><span class="sxs-lookup"><span data-stu-id="d31ac-125">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestProperty>.</span></span>

### <a name="asp-fallback-test-value"></a><span data-ttu-id="d31ac-126">ASP-fallback-Test-Value</span><span class="sxs-lookup"><span data-stu-id="d31ac-126">asp-fallback-test-value</span></span>

<span data-ttu-id="d31ac-127">O valor da propriedade CSS a ser usado para o teste de fallback.</span><span class="sxs-lookup"><span data-stu-id="d31ac-127">The CSS property value to use for the fallback test.</span></span> <span data-ttu-id="d31ac-128">Para obter mais informações, consulte <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>.</span><span class="sxs-lookup"><span data-stu-id="d31ac-128">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>.</span></span>

### <a name="asp-fallback-test-value"></a><span data-ttu-id="d31ac-129">ASP-fallback-Test-Value</span><span class="sxs-lookup"><span data-stu-id="d31ac-129">asp-fallback-test-value</span></span>

<span data-ttu-id="d31ac-130">O valor da propriedade CSS a ser usado para o teste de fallback.</span><span class="sxs-lookup"><span data-stu-id="d31ac-130">The CSS property value to use for the fallback test.</span></span> <span data-ttu-id="d31ac-131">Para obter mais informações, consulte <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>.</span><span class="sxs-lookup"><span data-stu-id="d31ac-131">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue></span></span>

## <a name="additional-resources"></a><span data-ttu-id="d31ac-132">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="d31ac-132">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
