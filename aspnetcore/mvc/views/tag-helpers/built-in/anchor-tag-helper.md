---
title: Auxiliar de Marca de Âncora no ASP.NET Core
author: pkellner
description: Descubra os atributos do Auxiliar de Marca de Âncora do ASP.NET e a função que cada atributo desempenha no comportamento de extensão da marca de âncora de HTML.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/18/2018
uid: mvc/views/tag-helpers/builtin-th/anchor-tag-helper
ms.openlocfilehash: 60fa0c00e40878a8227ca2bc8bdb0bc2bf9f8336
ms.sourcegitcommit: ea215df889e89db44037a6ac2f01baede0450da9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/19/2018
ms.locfileid: "53595335"
---
# <a name="anchor-tag-helper-in-aspnet-core"></a><span data-ttu-id="0df08-103">Auxiliar de Marca de Âncora no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0df08-103">Anchor Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="0df08-104">De [Peter Kellner](http://peterkellner.net) e [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="0df08-104">By [Peter Kellner](http://peterkellner.net) and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="0df08-105">O [Auxiliar de Marca de Âncora](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper) aprimora a marca de âncora HTML padrão (`<a ... ></a>`) adicionando novos atributos.</span><span class="sxs-lookup"><span data-stu-id="0df08-105">The [Anchor Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper) enhances the standard HTML anchor (`<a ... ></a>`) tag by adding new attributes.</span></span> <span data-ttu-id="0df08-106">Por convenção, os nomes de atributos são prefixados com `asp-`.</span><span class="sxs-lookup"><span data-stu-id="0df08-106">By convention, the attribute names are prefixed with `asp-`.</span></span> <span data-ttu-id="0df08-107">O valor do atributo `href` do elemento de âncora renderizado é determinado pelos valores dos atributos `asp-`.</span><span class="sxs-lookup"><span data-stu-id="0df08-107">The rendered anchor element's `href` attribute value is determined by the values of the `asp-` attributes.</span></span>

<span data-ttu-id="0df08-108">Para obter uma visão geral dos Auxiliares de Marca, confira <xref:mvc/views/tag-helpers/intro>.</span><span class="sxs-lookup"><span data-stu-id="0df08-108">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="0df08-109">[Exibir ou baixar código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0df08-109">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="0df08-110">*SpeakerController* é usado em exemplos ao longo de todo este documento:</span><span class="sxs-lookup"><span data-stu-id="0df08-110">*SpeakerController* is used in samples throughout this document:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?name=snippet_SpeakerController)]

<span data-ttu-id="0df08-111">Segue um inventário dos atributos `asp-`.</span><span class="sxs-lookup"><span data-stu-id="0df08-111">An inventory of the `asp-` attributes follows.</span></span>

## <a name="asp-controller"></a><span data-ttu-id="0df08-112">asp-controller</span><span class="sxs-lookup"><span data-stu-id="0df08-112">asp-controller</span></span>

<span data-ttu-id="0df08-113">O atributo [asp-controller](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Controller*) designa o controlador usado para gerar a URL.</span><span class="sxs-lookup"><span data-stu-id="0df08-113">The [asp-controller](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Controller*) attribute assigns the controller used for generating the URL.</span></span> <span data-ttu-id="0df08-114">A marcação a seguir lista todos os palestrantes:</span><span class="sxs-lookup"><span data-stu-id="0df08-114">The following markup lists all speakers:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspController)]

<span data-ttu-id="0df08-115">O HTML gerado:</span><span class="sxs-lookup"><span data-stu-id="0df08-115">The generated HTML:</span></span>

```html
<a href="/Speaker">All Speakers</a>
```

<span data-ttu-id="0df08-116">Se o atributo `asp-controller` for especificado e `asp-action` não for, o valor `asp-action` padrão é a ação do controlador associada ao modo de exibição em execução no momento.</span><span class="sxs-lookup"><span data-stu-id="0df08-116">If the `asp-controller` attribute is specified and `asp-action` isn't, the default `asp-action` value is the controller action associated with the currently executing view.</span></span> <span data-ttu-id="0df08-117">Se `asp-action` for omitido da marcação anterior e o Auxiliar de Marca de Âncora for usado no modo de exibição *Índice* (*/home*) do *HomeController*, o HTML gerado será:</span><span class="sxs-lookup"><span data-stu-id="0df08-117">If `asp-action` is omitted from the preceding markup, and the Anchor Tag Helper is used in *HomeController*'s *Index* view (*/Home*), the generated HTML is:</span></span>

```html
<a href="/Home">All Speakers</a>
```

## <a name="asp-action"></a><span data-ttu-id="0df08-118">asp-action</span><span class="sxs-lookup"><span data-stu-id="0df08-118">asp-action</span></span>

<span data-ttu-id="0df08-119">O valor do atributo [asp-action](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Action*) representa o nome da ação do controlador incluído no atributo `href` gerado.</span><span class="sxs-lookup"><span data-stu-id="0df08-119">The [asp-action](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Action*) attribute value represents the controller action name included in the generated `href` attribute.</span></span> <span data-ttu-id="0df08-120">A seguinte marcação define o valor do atributo `href` gerado para a página de avaliações do palestrante:</span><span class="sxs-lookup"><span data-stu-id="0df08-120">The following markup sets the generated `href` attribute value to the speaker evaluations page:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAction)]

<span data-ttu-id="0df08-121">O HTML gerado:</span><span class="sxs-lookup"><span data-stu-id="0df08-121">The generated HTML:</span></span>

```html
<a href="/Speaker/Evaluations">Speaker Evaluations</a>
```

<span data-ttu-id="0df08-122">Se nenhum atributo `asp-controller` for especificado, o controlador padrão que está chamando a exibição que executa a exibição atual é usado.</span><span class="sxs-lookup"><span data-stu-id="0df08-122">If no `asp-controller` attribute is specified, the default controller calling the view executing the current view is used.</span></span>

<span data-ttu-id="0df08-123">Se o valor do atributo `asp-action` for `Index`, nenhuma ação será anexada à URL, causando a invocação da ação `Index` padrão.</span><span class="sxs-lookup"><span data-stu-id="0df08-123">If the `asp-action` attribute value is `Index`, then no action is appended to the URL, leading to the invocation of the default `Index` action.</span></span> <span data-ttu-id="0df08-124">A ação especificada (ou padrão), deve existir no controlador referenciado em `asp-controller`.</span><span class="sxs-lookup"><span data-stu-id="0df08-124">The action specified (or defaulted), must exist in the controller referenced in `asp-controller`.</span></span>

## <a name="asp-route-value"></a><span data-ttu-id="0df08-125">asp-route-{value}</span><span class="sxs-lookup"><span data-stu-id="0df08-125">asp-route-{value}</span></span>

<span data-ttu-id="0df08-126">O atributo [asp-route-{value}](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) permite um prefixo de roteamento de caractere curinga.</span><span class="sxs-lookup"><span data-stu-id="0df08-126">The [asp-route-{value}](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) attribute enables a wildcard route prefix.</span></span> <span data-ttu-id="0df08-127">Qualquer valor que esteja ocupando o espaço reservado `{value}` é interpretado como um possível parâmetro de roteamento.</span><span class="sxs-lookup"><span data-stu-id="0df08-127">Any value occupying the `{value}` placeholder is interpreted as a potential route parameter.</span></span> <span data-ttu-id="0df08-128">Se uma rota padrão não for encontrada, esse prefixo de rota será anexado ao atributo `href` gerado como um valor e parâmetro de solicitação.</span><span class="sxs-lookup"><span data-stu-id="0df08-128">If a default route isn't found, this route prefix is appended to the generated `href` attribute as a request parameter and value.</span></span> <span data-ttu-id="0df08-129">Caso contrário, ele será substituído no modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="0df08-129">Otherwise, it's substituted in the route template.</span></span>

<span data-ttu-id="0df08-130">Considere a seguinte ação do controlador:</span><span class="sxs-lookup"><span data-stu-id="0df08-130">Consider the following controller action:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/BuiltInTagController.cs?name=snippet_AnchorTagHelperAction)]

<span data-ttu-id="0df08-131">Com um modelo de rota padrão definido em *Startup.Configure*:</span><span class="sxs-lookup"><span data-stu-id="0df08-131">With a default route template defined in *Startup.Configure*:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_UseMvc&highlight=8-10)]

<span data-ttu-id="0df08-132">O modo de exibição MVC usa o modelo fornecido pela ação, da seguinte forma:</span><span class="sxs-lookup"><span data-stu-id="0df08-132">The MVC view uses the model, provided by the action, as follows:</span></span>

```cshtml
@model Speaker
<!DOCTYPE html>
<html>
<body>
    <a asp-controller="Speaker"
       asp-action="Detail" 
       asp-route-id="@Model.SpeakerId">SpeakerId: @Model.SpeakerId</a>
</body>
</html>
```

<span data-ttu-id="0df08-133">O espaço reservado `{id?}` da rota padrão foi correspondido.</span><span class="sxs-lookup"><span data-stu-id="0df08-133">The default route's `{id?}` placeholder was matched.</span></span> <span data-ttu-id="0df08-134">O HTML gerado:</span><span class="sxs-lookup"><span data-stu-id="0df08-134">The generated HTML:</span></span>

```html
<a href="/Speaker/Detail/12">SpeakerId: 12</a>
```

<span data-ttu-id="0df08-135">Suponha que o prefixo de roteamento não faça parte do modelo de roteamento de correspondência, como com o seguinte modo de exibição de MVC:</span><span class="sxs-lookup"><span data-stu-id="0df08-135">Assume the route prefix isn't part of the matching routing template, as with the following MVC view:</span></span>

```cshtml
@model Speaker
<!DOCTYPE html>
<html>
<body>
    <a asp-controller="Speaker"
       asp-action="Detail"
       asp-route-speakerid="@Model.SpeakerId">SpeakerId: @Model.SpeakerId</a>
<body>
</html>
```

<span data-ttu-id="0df08-136">O seguinte HTML é gerado porque o `speakerid` não foi encontrado na rota correspondente:</span><span class="sxs-lookup"><span data-stu-id="0df08-136">The following HTML is generated because `speakerid` wasn't found in the matching route:</span></span>

```html
<a href="/Speaker/Detail?speakerid=12">SpeakerId: 12</a>
```

<span data-ttu-id="0df08-137">Se `asp-controller` ou `asp-action` não forem especificados, o mesmo processamento padrão será seguido, como no atributo `asp-route`.</span><span class="sxs-lookup"><span data-stu-id="0df08-137">If either `asp-controller` or `asp-action` aren't specified, then the same default processing is followed as is in the `asp-route` attribute.</span></span>

## <a name="asp-route"></a><span data-ttu-id="0df08-138">asp-route</span><span class="sxs-lookup"><span data-stu-id="0df08-138">asp-route</span></span>

<span data-ttu-id="0df08-139">O atributo [asp-route](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Route*) é usado para criar um link de URL diretamente para uma rota nomeada.</span><span class="sxs-lookup"><span data-stu-id="0df08-139">The [asp-route](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Route*) attribute is used for creating a URL linking directly to a named route.</span></span> <span data-ttu-id="0df08-140">Usando [atributos de roteamento](xref:mvc/controllers/routing#attribute-routing), uma rota pode ser nomeada como mostrado em `SpeakerController` e usada em sua ação `Evaluations`:</span><span class="sxs-lookup"><span data-stu-id="0df08-140">Using [routing attributes](xref:mvc/controllers/routing#attribute-routing), a route can be named as shown in the `SpeakerController` and used in its `Evaluations` action:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?range=22-24)]

<span data-ttu-id="0df08-141">Na seguinte marcação, o atributo `asp-route` faz referência à rota nomeada:</span><span class="sxs-lookup"><span data-stu-id="0df08-141">In the following markup, the `asp-route` attribute references the named route:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspRoute)]

<span data-ttu-id="0df08-142">O Auxiliar de Marca de Âncora gera uma rota diretamente para essa ação de controlador usando a URL */Speaker/Evaluations*.</span><span class="sxs-lookup"><span data-stu-id="0df08-142">The Anchor Tag Helper generates a route directly to that controller action using the URL */Speaker/Evaluations*.</span></span> <span data-ttu-id="0df08-143">O HTML gerado:</span><span class="sxs-lookup"><span data-stu-id="0df08-143">The generated HTML:</span></span>

```html
<a href="/Speaker/Evaluations">Speaker Evaluations</a>
```

<span data-ttu-id="0df08-144">Se `asp-controller` ou `asp-action` for especificado além de `asp-route`, a rota gerada poderá não ser a esperada.</span><span class="sxs-lookup"><span data-stu-id="0df08-144">If `asp-controller` or `asp-action` is specified in addition to `asp-route`, the route generated may not be what you expect.</span></span> <span data-ttu-id="0df08-145">Para evitar um conflito de rota, `asp-route` não deve ser usado com os atributos `asp-controller` ou `asp-action`.</span><span class="sxs-lookup"><span data-stu-id="0df08-145">To avoid a route conflict, `asp-route` shouldn't be used with the `asp-controller` and `asp-action` attributes.</span></span>

## <a name="asp-all-route-data"></a><span data-ttu-id="0df08-146">asp-all-route-data</span><span class="sxs-lookup"><span data-stu-id="0df08-146">asp-all-route-data</span></span>

<span data-ttu-id="0df08-147">O atributo [asp-all-route-data](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) oferece suporte à criação de um dicionário ou par chave-valor.</span><span class="sxs-lookup"><span data-stu-id="0df08-147">The [asp-all-route-data](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) attribute supports the creation of a dictionary of key-value pairs.</span></span> <span data-ttu-id="0df08-148">A chave é o nome do parâmetro e o valor é o valor do parâmetro.</span><span class="sxs-lookup"><span data-stu-id="0df08-148">The key is the parameter name, and the value is the parameter value.</span></span>

<span data-ttu-id="0df08-149">No exemplo a seguir, um dicionário é inicializado e passado para um modo de exibição Razor.</span><span class="sxs-lookup"><span data-stu-id="0df08-149">In the following example, a dictionary is initialized and passed to a Razor view.</span></span> <span data-ttu-id="0df08-150">Como alternativa, os dados podem ser passado com seu modelo.</span><span class="sxs-lookup"><span data-stu-id="0df08-150">Alternatively, the data could be passed in with your model.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAllRouteData)]

<span data-ttu-id="0df08-151">O código anterior gera o seguinte HTML:</span><span class="sxs-lookup"><span data-stu-id="0df08-151">The preceding code generates the following HTML:</span></span>

```html
<a href="/Speaker/EvaluationsCurrent?speakerId=11&currentYear=true">Speaker Evaluations</a>
```

<span data-ttu-id="0df08-152">O dicionário `asp-all-route-data` é simplificado para produzir um querystring que atenda aos requisitos da ação `Evaluations` sobrecarregada:</span><span class="sxs-lookup"><span data-stu-id="0df08-152">The `asp-all-route-data` dictionary is flattened to produce a querystring meeting the requirements of the overloaded `Evaluations` action:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?range=26-30)]

<span data-ttu-id="0df08-153">Se todas as chaves no dicionário corresponderem aos parâmetros, esses valores serão substituídos na rota conforme apropriado.</span><span class="sxs-lookup"><span data-stu-id="0df08-153">If any keys in the dictionary match route parameters, those values are substituted in the route as appropriate.</span></span> <span data-ttu-id="0df08-154">Os outros valores não correspondentes são gerados como parâmetros de solicitação.</span><span class="sxs-lookup"><span data-stu-id="0df08-154">The other non-matching values are generated as request parameters.</span></span>

## <a name="asp-fragment"></a><span data-ttu-id="0df08-155">asp-fragment</span><span class="sxs-lookup"><span data-stu-id="0df08-155">asp-fragment</span></span>

<span data-ttu-id="0df08-156">O atributo [asp-fragment](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Fragment*) define um fragmento de URL para anexar à URL.</span><span class="sxs-lookup"><span data-stu-id="0df08-156">The [asp-fragment](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Fragment*) attribute defines a URL fragment to append to the URL.</span></span> <span data-ttu-id="0df08-157">O Auxiliar de Marca de Âncora adiciona o caractere de hash (#).</span><span class="sxs-lookup"><span data-stu-id="0df08-157">The Anchor Tag Helper adds the hash character (#).</span></span> <span data-ttu-id="0df08-158">Considere a seguinte marcação:</span><span class="sxs-lookup"><span data-stu-id="0df08-158">Consider the following markup:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspFragment)]

<span data-ttu-id="0df08-159">O HTML gerado:</span><span class="sxs-lookup"><span data-stu-id="0df08-159">The generated HTML:</span></span>

```html
<a href="/Speaker/Evaluations#SpeakerEvaluations">Speaker Evaluations</a>
```

<span data-ttu-id="0df08-160">As marcas de hash são úteis ao criar aplicativos do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="0df08-160">Hash tags are useful when building client-side apps.</span></span> <span data-ttu-id="0df08-161">Elas podem ser usadas para marcar e pesquisar com facilidade em JavaScript, por exemplo.</span><span class="sxs-lookup"><span data-stu-id="0df08-161">They can be used for easy marking and searching in JavaScript, for example.</span></span>

## <a name="asp-area"></a><span data-ttu-id="0df08-162">asp-area</span><span class="sxs-lookup"><span data-stu-id="0df08-162">asp-area</span></span>

<span data-ttu-id="0df08-163">O atributo [asp-area](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Area*) define o nome de área usado para definir a rota apropriada.</span><span class="sxs-lookup"><span data-stu-id="0df08-163">The [asp-area](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Area*) attribute sets the area name used to set the appropriate route.</span></span> <span data-ttu-id="0df08-164">O exemplo a seguir ilustra como o atributo `asp-area` causa um remapeamento das rotas.</span><span class="sxs-lookup"><span data-stu-id="0df08-164">The following examples depict how the `asp-area` attribute causes a remapping of routes.</span></span>

### <a name="usage-in-razor-pages"></a><span data-ttu-id="0df08-165">Uso no Razor Pages</span><span class="sxs-lookup"><span data-stu-id="0df08-165">Usage in Razor Pages</span></span>

<span data-ttu-id="0df08-166">As áreas do Razor Pages têm suporte no ASP.NET Core 2.1 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="0df08-166">Razor Pages areas are supported in ASP.NET Core 2.1 or later.</span></span>

<span data-ttu-id="0df08-167">Considere a seguinte hierarquia de diretórios:</span><span class="sxs-lookup"><span data-stu-id="0df08-167">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="0df08-168">**{Nome do projeto}**</span><span class="sxs-lookup"><span data-stu-id="0df08-168">**{Project name}**</span></span>
  * <span data-ttu-id="0df08-169">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="0df08-169">**wwwroot**</span></span>
  * <span data-ttu-id="0df08-170">**Áreas**</span><span class="sxs-lookup"><span data-stu-id="0df08-170">**Areas**</span></span>
    * <span data-ttu-id="0df08-171">**Sessões**</span><span class="sxs-lookup"><span data-stu-id="0df08-171">**Sessions**</span></span>
      * <span data-ttu-id="0df08-172">**Páginas**</span><span class="sxs-lookup"><span data-stu-id="0df08-172">**Pages**</span></span>
        * <span data-ttu-id="0df08-173">*\_ViewStart.cshtml*</span><span class="sxs-lookup"><span data-stu-id="0df08-173">*\_ViewStart.cshtml*</span></span>
        * <span data-ttu-id="0df08-174">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="0df08-174">*Index.cshtml*</span></span>
        * <span data-ttu-id="0df08-175">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="0df08-175">*Index.cshtml.cs*</span></span>
  * <span data-ttu-id="0df08-176">**Páginas**</span><span class="sxs-lookup"><span data-stu-id="0df08-176">**Pages**</span></span>

<span data-ttu-id="0df08-177">A marcação para fazer referência ao Razor Page de *Índice* da área *Sessões* é:</span><span class="sxs-lookup"><span data-stu-id="0df08-177">The markup to reference the *Sessions* area *Index* Razor Page is:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAreaRazorPages)]

<span data-ttu-id="0df08-178">O HTML gerado:</span><span class="sxs-lookup"><span data-stu-id="0df08-178">The generated HTML:</span></span>

```html
<a href="/Sessions">View Sessions</a>
```

> [!TIP]
> <span data-ttu-id="0df08-179">para dar suporte a áreas em um aplicativo do Razor Pages, siga um destes procedimentos em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="0df08-179">To support areas in a Razor Pages app, do one of the following in `Startup.ConfigureServices`:</span></span>
>
> * <span data-ttu-id="0df08-180">Defina a [versão de compatibilidade](xref:mvc/compatibility-version) para 2.1 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="0df08-180">Set the [compatibility version](xref:mvc/compatibility-version) to 2.1 or later.</span></span>
> * <span data-ttu-id="0df08-181">Defina a propriedade [RazorPagesOptions.AllowAreas](xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.AllowAreas*) como `true`:</span><span class="sxs-lookup"><span data-stu-id="0df08-181">Set the [RazorPagesOptions.AllowAreas](xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.AllowAreas*) property to `true`:</span></span>
>
>   [!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_AllowAreas)]

### <a name="usage-in-mvc"></a><span data-ttu-id="0df08-182">Uso no MVC</span><span class="sxs-lookup"><span data-stu-id="0df08-182">Usage in MVC</span></span>

<span data-ttu-id="0df08-183">Considere a seguinte hierarquia de diretórios:</span><span class="sxs-lookup"><span data-stu-id="0df08-183">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="0df08-184">**{Nome do projeto}**</span><span class="sxs-lookup"><span data-stu-id="0df08-184">**{Project name}**</span></span>
  * <span data-ttu-id="0df08-185">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="0df08-185">**wwwroot**</span></span>
  * <span data-ttu-id="0df08-186">**Áreas**</span><span class="sxs-lookup"><span data-stu-id="0df08-186">**Areas**</span></span>
    * <span data-ttu-id="0df08-187">**Blogs**</span><span class="sxs-lookup"><span data-stu-id="0df08-187">**Blogs**</span></span>
      * <span data-ttu-id="0df08-188">**Controladores**</span><span class="sxs-lookup"><span data-stu-id="0df08-188">**Controllers**</span></span>
        * <span data-ttu-id="0df08-189">*HomeController.cs*</span><span class="sxs-lookup"><span data-stu-id="0df08-189">*HomeController.cs*</span></span>
      * <span data-ttu-id="0df08-190">**Exibições**</span><span class="sxs-lookup"><span data-stu-id="0df08-190">**Views**</span></span>
        * <span data-ttu-id="0df08-191">**Início**</span><span class="sxs-lookup"><span data-stu-id="0df08-191">**Home**</span></span>
          * <span data-ttu-id="0df08-192">*AboutBlog.cshtml*</span><span class="sxs-lookup"><span data-stu-id="0df08-192">*AboutBlog.cshtml*</span></span>
          * <span data-ttu-id="0df08-193">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="0df08-193">*Index.cshtml*</span></span>
        * <span data-ttu-id="0df08-194">*\_ViewStart.cshtml*</span><span class="sxs-lookup"><span data-stu-id="0df08-194">*\_ViewStart.cshtml*</span></span>
  * <span data-ttu-id="0df08-195">**Controladores**</span><span class="sxs-lookup"><span data-stu-id="0df08-195">**Controllers**</span></span>

<span data-ttu-id="0df08-196">Definir `asp-area` como "Blogs" faz com que o diretório *Áreas/Blogs* seja prefixado nas rotas dos controladores e exibições associados a essa marca de âncora.</span><span class="sxs-lookup"><span data-stu-id="0df08-196">Setting `asp-area` to "Blogs" prefixes the directory *Areas/Blogs* to the routes of the associated controllers and views for this anchor tag.</span></span> <span data-ttu-id="0df08-197">A marcação para fazer referência à exibição *AboutBlog* é:</span><span class="sxs-lookup"><span data-stu-id="0df08-197">The markup to reference the *AboutBlog* view is:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspArea)]

<span data-ttu-id="0df08-198">O HTML gerado:</span><span class="sxs-lookup"><span data-stu-id="0df08-198">The generated HTML:</span></span>

```html
<a href="/Blogs/Home/AboutBlog">About Blog</a>
```

> [!TIP]
> <span data-ttu-id="0df08-199">Para dar suporte às áreas em um aplicativo MVC, o modelo de rota deverá incluir uma referência à área, se ela existir.</span><span class="sxs-lookup"><span data-stu-id="0df08-199">To support areas in an MVC app, the route template must include a reference to the area, if it exists.</span></span> <span data-ttu-id="0df08-200">Esse modelo é representado pelo segundo parâmetro da chamada do método `routes.MapRoute` em *Startup.Configure*:</span><span class="sxs-lookup"><span data-stu-id="0df08-200">That template is represented by the second parameter of the `routes.MapRoute` method call in *Startup.Configure*:</span></span>
>
> [!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_UseMvc&highlight=5)]

## <a name="asp-protocol"></a><span data-ttu-id="0df08-201">asp-protocol</span><span class="sxs-lookup"><span data-stu-id="0df08-201">asp-protocol</span></span>

<span data-ttu-id="0df08-202">O atributo [asp-protocol](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Protocol*) é destinado a especificar um protocolo (como `https`) em sua URL.</span><span class="sxs-lookup"><span data-stu-id="0df08-202">The [asp-protocol](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Protocol*) attribute is for specifying a protocol (such as `https`) in your URL.</span></span> <span data-ttu-id="0df08-203">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="0df08-203">For example:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspProtocol)]

<span data-ttu-id="0df08-204">O HTML gerado:</span><span class="sxs-lookup"><span data-stu-id="0df08-204">The generated HTML:</span></span>

```html
<a href="https://localhost/Home/About">About</a>
```

<span data-ttu-id="0df08-205">O nome do host no exemplo é localhost.</span><span class="sxs-lookup"><span data-stu-id="0df08-205">The host name in the example is localhost.</span></span> <span data-ttu-id="0df08-206">O Auxiliar de Marca de Âncora usa o domínio público do site ao gerar a URL.</span><span class="sxs-lookup"><span data-stu-id="0df08-206">The Anchor Tag Helper uses the website's public domain when generating the URL.</span></span>

## <a name="asp-host"></a><span data-ttu-id="0df08-207">asp-host</span><span class="sxs-lookup"><span data-stu-id="0df08-207">asp-host</span></span>

<span data-ttu-id="0df08-208">O atributo [asp-host](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Host*) é para especificar um nome do host na sua URL.</span><span class="sxs-lookup"><span data-stu-id="0df08-208">The [asp-host](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Host*) attribute is for specifying a host name in your URL.</span></span> <span data-ttu-id="0df08-209">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="0df08-209">For example:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspHost)]

<span data-ttu-id="0df08-210">O HTML gerado:</span><span class="sxs-lookup"><span data-stu-id="0df08-210">The generated HTML:</span></span>

```html
<a href="https://microsoft.com/Home/About">About</a>
```

## <a name="asp-page"></a><span data-ttu-id="0df08-211">asp-page</span><span class="sxs-lookup"><span data-stu-id="0df08-211">asp-page</span></span>

<span data-ttu-id="0df08-212">O atributo [asp-page](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Page*) é usado com as Páginas Razor.</span><span class="sxs-lookup"><span data-stu-id="0df08-212">The [asp-page](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Page*) attribute is used with Razor Pages.</span></span> <span data-ttu-id="0df08-213">Use-o para definir um valor de atributo `href` da marca de âncora para uma página específica.</span><span class="sxs-lookup"><span data-stu-id="0df08-213">Use it to set an anchor tag's `href` attribute value to a specific page.</span></span> <span data-ttu-id="0df08-214">Prefixar o nome de página com uma barra ("/") cria a URL.</span><span class="sxs-lookup"><span data-stu-id="0df08-214">Prefixing the page name with a forward slash ("/") creates the URL.</span></span>

<span data-ttu-id="0df08-215">O exemplo a seguir aponta para o participante da Página Razor:</span><span class="sxs-lookup"><span data-stu-id="0df08-215">The following sample points to the attendee Razor Page:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPage)]

<span data-ttu-id="0df08-216">O HTML gerado:</span><span class="sxs-lookup"><span data-stu-id="0df08-216">The generated HTML:</span></span>

```html
<a href="/Attendee">All Attendees</a>
```

<span data-ttu-id="0df08-217">O atributo `asp-page` é mutuamente exclusivo com os atributos `asp-route`, `asp-controller` e `asp-action`.</span><span class="sxs-lookup"><span data-stu-id="0df08-217">The `asp-page` attribute is mutually exclusive with the `asp-route`, `asp-controller`, and `asp-action` attributes.</span></span> <span data-ttu-id="0df08-218">No entanto, o `asp-page` pode ser usado com o `asp-route-{value}` para controlar o roteamento, como mostra a marcação a seguir:</span><span class="sxs-lookup"><span data-stu-id="0df08-218">However, `asp-page` can be used with `asp-route-{value}` to control routing, as the following markup demonstrates:</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPageAspRouteId)]

<span data-ttu-id="0df08-219">O HTML gerado:</span><span class="sxs-lookup"><span data-stu-id="0df08-219">The generated HTML:</span></span>

```html
<a href="/Attendee?attendeeid=10">View Attendee</a>
```

## <a name="asp-page-handler"></a><span data-ttu-id="0df08-220">asp-page-handler</span><span class="sxs-lookup"><span data-stu-id="0df08-220">asp-page-handler</span></span>

<span data-ttu-id="0df08-221">O atributo [asp-page-handler](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.PageHandler*) é usado com as Páginas Razor.</span><span class="sxs-lookup"><span data-stu-id="0df08-221">The [asp-page-handler](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.PageHandler*) attribute is used with Razor Pages.</span></span> <span data-ttu-id="0df08-222">Ele se destina à vinculação a manipuladores de página específicos.</span><span class="sxs-lookup"><span data-stu-id="0df08-222">It's intended for linking to specific page handlers.</span></span>

<span data-ttu-id="0df08-223">Considere o seguinte manipulador de página:</span><span class="sxs-lookup"><span data-stu-id="0df08-223">Consider the following page handler:</span></span>

[!code-csharp[](samples/TagHelpersBuiltIn/Pages/Attendee.cshtml.cs?name=snippet_OnGetProfileHandler)]

<span data-ttu-id="0df08-224">A marcação associada do modelo de página vincula ao manipulador de página `OnGetProfile`.</span><span class="sxs-lookup"><span data-stu-id="0df08-224">The page model's associated markup links to the `OnGetProfile` page handler.</span></span> <span data-ttu-id="0df08-225">Observe que o prefixo `On<Verb>` do nome do método do manipulador de página é omitido no valor do atributo `asp-page-handler`.</span><span class="sxs-lookup"><span data-stu-id="0df08-225">Note the `On<Verb>` prefix of the page handler method name is omitted in the `asp-page-handler` attribute value.</span></span> <span data-ttu-id="0df08-226">Quando o método é assíncrono, o sufixo `Async` também é omitido.</span><span class="sxs-lookup"><span data-stu-id="0df08-226">When the method is asynchronous, the `Async` suffix is omitted, too.</span></span>

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPageHandler)]

<span data-ttu-id="0df08-227">O HTML gerado:</span><span class="sxs-lookup"><span data-stu-id="0df08-227">The generated HTML:</span></span>

```html
<a href="/Attendee?attendeeid=12&handler=Profile">Attendee Profile</a>
```

## <a name="additional-resources"></a><span data-ttu-id="0df08-228">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="0df08-228">Additional resources</span></span>

* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
