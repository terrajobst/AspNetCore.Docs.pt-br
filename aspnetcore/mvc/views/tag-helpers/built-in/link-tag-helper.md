---
title: Auxiliar de marca de link no ASP.NET Core
author: rick-anderson
ms.author: riande
description: Descubra os atributos auxiliares de marca de link ASP.NET Core e a função que cada atributo desempenha na extensão do comportamento da marca de link HTML.
ms.custom: mvc
ms.date: 09/24/2019
uid: mvc/views/tag-helpers/builtin-th/link-tag-helper
ms.openlocfilehash: e1e2e58b4ab9087e1f9de5b5c03b587feb88f1b9
ms.sourcegitcommit: fae6f0e253f9d62d8f39de5884d2ba2b4b2a6050
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71256515"
---
# <a name="link-tag-helper-in-aspnet-core"></a><span data-ttu-id="60eb0-103">Auxiliar de marca de link no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="60eb0-103">Link Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="60eb0-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="60eb0-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="60eb0-105">O [auxiliar de marca de link](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper) gera um link para um arquivo CSS primário ou de retorno.</span><span class="sxs-lookup"><span data-stu-id="60eb0-105">The [Link Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper) generates a link to a primary or fall back CSS file.</span></span> <span data-ttu-id="60eb0-106">Normalmente, o arquivo CSS primário está em uma CDN ( [rede de distribuição de conteúdo](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) ).</span><span class="sxs-lookup"><span data-stu-id="60eb0-106">Typically the primary CSS file is on a [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).</span></span>

[!INCLUDE[](~/includes/cdn.md)]

<span data-ttu-id="60eb0-107">O auxiliar de marca de link permite que você especifique uma CDN para o arquivo CSS e um fallback quando a CDN não estiver disponível.</span><span class="sxs-lookup"><span data-stu-id="60eb0-107">The Link Tag Helper allows you to specify a CDN for the CSS file and a fallback when the CDN is not available.</span></span> <span data-ttu-id="60eb0-108">O auxiliar de marca de link fornece a vantagem de desempenho de uma CDN com a robustez da hospedagem local.</span><span class="sxs-lookup"><span data-stu-id="60eb0-108">The Link Tag Helper provides the performance advantage of a CDN with the robustness of local hosting.</span></span>

<span data-ttu-id="60eb0-109">A marcação Razor a seguir mostra `head` o elemento de um arquivo de layout criado com o modelo de aplicativo Web ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="60eb0-109">The following Razor markup shows the `head` element of a layout file created with the ASP.NET Core web app template:</span></span>

[!code-html[](link-tag-helper/sample/_Layout.cshtml?name=snippet)]

<span data-ttu-id="60eb0-110">Este é um HTML renderizado do código anterior (em um ambiente que não é de desenvolvimento):</span><span class="sxs-lookup"><span data-stu-id="60eb0-110">The following is rendered HTML from the preceding code (in a non-Development environment):</span></span>

[!code-csharp[](link-tag-helper/sample/HtmlPage1.html)]

<span data-ttu-id="60eb0-111">No código anterior, o auxiliar de marca de link gerou `<meta name="x-stylesheet-fallback-test" content="" class="sr-only" />` o elemento e o JavaScript a seguir, que é usado para verificar o arquivo *bootstrap. min. css* solicitado está disponível na CDN.</span><span class="sxs-lookup"><span data-stu-id="60eb0-111">In the preceding code, the Link Tag Helper generated the `<meta name="x-stylesheet-fallback-test" content="" class="sr-only" />` element and the following JavaScript which is used to verify the requested *bootstrap.min.css* file is available on the CDN.</span></span> <span data-ttu-id="60eb0-112">Nesse caso, o arquivo CSS estava disponível para que o auxiliar de marca gerasse o `<link />` elemento com o arquivo CSS da CDN.</span><span class="sxs-lookup"><span data-stu-id="60eb0-112">In this case, the CSS file was available so the Tag Helper generated the `<link />` element with the CDN CSS file.</span></span>

## <a name="commonly-used-link-tag-helper-attributes"></a><span data-ttu-id="60eb0-113">Atributos auxiliares de marca de link usados com frequência</span><span class="sxs-lookup"><span data-stu-id="60eb0-113">Commonly used Link Tag Helper attributes</span></span>

<span data-ttu-id="60eb0-114">Consulte [auxiliar de marca de link](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper) para todos os atributos, propriedades e métodos auxiliares de marca de link.</span><span class="sxs-lookup"><span data-stu-id="60eb0-114">See [Link Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper)  for all the Link Tag Helper attributes, properties, and methods.</span></span>

### <a name="href"></a><span data-ttu-id="60eb0-115">{1&gt;href&lt;1}</span><span class="sxs-lookup"><span data-stu-id="60eb0-115">href</span></span>

<span data-ttu-id="60eb0-116">Endereço preferencial do recurso vinculado.</span><span class="sxs-lookup"><span data-stu-id="60eb0-116">Preferred address of the linked resource.</span></span> <span data-ttu-id="60eb0-117">O endereço é passado para o HTML gerado em todos os casos.</span><span class="sxs-lookup"><span data-stu-id="60eb0-117">The address is passed thought to the generated HTML in all cases.</span></span>

### <a name="asp-fallback-href"></a><span data-ttu-id="60eb0-118">ASP-fallback-href</span><span class="sxs-lookup"><span data-stu-id="60eb0-118">asp-fallback-href</span></span>

<span data-ttu-id="60eb0-119">A URL de uma folha de estilos CSS para fazer fallback no caso em que a URL primária falhar.</span><span class="sxs-lookup"><span data-stu-id="60eb0-119">The URL of a CSS stylesheet to fallback to in the case the primary URL fails.</span></span>

### <a name="asp-fallback-test-class"></a><span data-ttu-id="60eb0-120">ASP-fallback-Test-Class</span><span class="sxs-lookup"><span data-stu-id="60eb0-120">asp-fallback-test-class</span></span>

<span data-ttu-id="60eb0-121">O nome da classe definida na folha de estilos a ser usada para o teste de fallback.</span><span class="sxs-lookup"><span data-stu-id="60eb0-121">The class name defined in the stylesheet to use for the fallback test.</span></span> <span data-ttu-id="60eb0-122">Para obter mais informações, consulte <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestClass>.</span><span class="sxs-lookup"><span data-stu-id="60eb0-122">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestClass>.</span></span>

### <a name="asp-fallback-test-property"></a><span data-ttu-id="60eb0-123">ASP-fallback-Test-Property</span><span class="sxs-lookup"><span data-stu-id="60eb0-123">asp-fallback-test-property</span></span>

<span data-ttu-id="60eb0-124">O nome da propriedade CSS a ser usado para o teste de fallback.</span><span class="sxs-lookup"><span data-stu-id="60eb0-124">The CSS property name to use for the fallback test.</span></span> <span data-ttu-id="60eb0-125">Para obter mais informações, consulte <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestProperty>.</span><span class="sxs-lookup"><span data-stu-id="60eb0-125">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestProperty>.</span></span>

### <a name="asp-fallback-test-value"></a><span data-ttu-id="60eb0-126">ASP-fallback-Test-Value</span><span class="sxs-lookup"><span data-stu-id="60eb0-126">asp-fallback-test-value</span></span>

<span data-ttu-id="60eb0-127">O valor da propriedade CSS a ser usado para o teste de fallback.</span><span class="sxs-lookup"><span data-stu-id="60eb0-127">The CSS property value to use for the fallback test.</span></span> <span data-ttu-id="60eb0-128">Para obter mais informações, consulte <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>.</span><span class="sxs-lookup"><span data-stu-id="60eb0-128">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>.</span></span>

### <a name="asp-fallback-test-value"></a><span data-ttu-id="60eb0-129">ASP-fallback-Test-Value</span><span class="sxs-lookup"><span data-stu-id="60eb0-129">asp-fallback-test-value</span></span>

<span data-ttu-id="60eb0-130">O valor da propriedade CSS a ser usado para o teste de fallback.</span><span class="sxs-lookup"><span data-stu-id="60eb0-130">The CSS property value to use for the fallback test.</span></span> <span data-ttu-id="60eb0-131">Para obter mais informações, consulte <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>.</span><span class="sxs-lookup"><span data-stu-id="60eb0-131">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue></span></span>

## <a name="additional-resources"></a><span data-ttu-id="60eb0-132">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="60eb0-132">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
