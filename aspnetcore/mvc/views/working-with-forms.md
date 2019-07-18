---
title: Auxiliares de marca em formulários no ASP.NET Core
author: rick-anderson
description: Descreve os Auxiliares de marca internos usados com Formulários.
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2019
uid: mvc/views/working-with-forms
ms.openlocfilehash: a07bb4f539c8bd38b08402c598924e14c748921d
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815233"
---
# <a name="tag-helpers-in-forms-in-aspnet-core"></a><span data-ttu-id="370b5-103">Auxiliares de marca em formulários no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="370b5-103">Tag Helpers in forms in ASP.NET Core</span></span>

<span data-ttu-id="370b5-104">De [Rick Anderson](https://twitter.com/RickAndMSFT), [N. Taylor Mullen](https://github.com/NTaylorMullen), [Dave Paquette](https://twitter.com/Dave_Paquette) e [Jerrie Pelser](https://github.com/jerriep)</span><span class="sxs-lookup"><span data-stu-id="370b5-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [N. Taylor Mullen](https://github.com/NTaylorMullen), [Dave Paquette](https://twitter.com/Dave_Paquette), and [Jerrie Pelser](https://github.com/jerriep)</span></span>

<span data-ttu-id="370b5-105">Este documento demonstra como é o trabalho com Formulários e os elementos HTML usados comumente em um Formulário.</span><span class="sxs-lookup"><span data-stu-id="370b5-105">This document demonstrates working with Forms and the HTML elements commonly used on a Form.</span></span> <span data-ttu-id="370b5-106">O elemento HTML [Formulário](https://www.w3.org/TR/html401/interact/forms.html) fornece o mecanismo primário que os aplicativos Web usam para postar dados para o servidor.</span><span class="sxs-lookup"><span data-stu-id="370b5-106">The HTML [Form](https://www.w3.org/TR/html401/interact/forms.html) element provides the primary mechanism web apps use to post back data to the server.</span></span> <span data-ttu-id="370b5-107">A maior parte deste documento descreve os [Auxiliares de marca](tag-helpers/intro.md) e como eles podem ajudar você a criar formulários HTML robustos de forma produtiva.</span><span class="sxs-lookup"><span data-stu-id="370b5-107">Most of this document describes [Tag Helpers](tag-helpers/intro.md) and how they can help you productively create robust HTML forms.</span></span> <span data-ttu-id="370b5-108">É recomendável que você leia [Introdução ao auxiliares de marca](tag-helpers/intro.md) antes de ler este documento.</span><span class="sxs-lookup"><span data-stu-id="370b5-108">We recommend you read [Introduction to Tag Helpers](tag-helpers/intro.md) before you read this document.</span></span>

<span data-ttu-id="370b5-109">Em muitos casos, os Auxiliares HTML fornecem uma abordagem alternativa a um Auxiliar de Marca específico, mas é importante reconhecer que os Auxiliares de Marca não substituem os Auxiliares HTML e que não há um Auxiliar de Marca para cada Auxiliar HTML.</span><span class="sxs-lookup"><span data-stu-id="370b5-109">In many cases, HTML Helpers provide an alternative approach to a specific Tag Helper, but it's important to recognize that Tag Helpers don't replace HTML Helpers and there's not a Tag Helper for each HTML Helper.</span></span> <span data-ttu-id="370b5-110">Quando existe um Auxiliar HTML alternativo, ele é mencionado.</span><span class="sxs-lookup"><span data-stu-id="370b5-110">When an HTML Helper alternative exists, it's mentioned.</span></span>

<a name="my-asp-route-param-ref-label"></a>

## <a name="the-form-tag-helper"></a><span data-ttu-id="370b5-111">O Auxiliar de marca de formulário</span><span class="sxs-lookup"><span data-stu-id="370b5-111">The Form Tag Helper</span></span>

<span data-ttu-id="370b5-112">O Auxiliar de marca de [formulário](https://www.w3.org/TR/html401/interact/forms.html):</span><span class="sxs-lookup"><span data-stu-id="370b5-112">The [Form](https://www.w3.org/TR/html401/interact/forms.html) Tag Helper:</span></span>

* <span data-ttu-id="370b5-113">Gera o valor do atributo HTML [\<FORM>](https://www.w3.org/TR/html401/interact/forms.html) `action` para uma ação do controlador MVC ou uma rota nomeada</span><span class="sxs-lookup"><span data-stu-id="370b5-113">Generates the HTML [\<FORM>](https://www.w3.org/TR/html401/interact/forms.html) `action` attribute value for a MVC controller action or named route</span></span>

* <span data-ttu-id="370b5-114">Gera um [Token de verificação de solicitação](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) oculto para evitar a falsificação de solicitações entre sites (quando usado com o atributo `[ValidateAntiForgeryToken]` no método de ação HTTP Post)</span><span class="sxs-lookup"><span data-stu-id="370b5-114">Generates a hidden [Request Verification Token](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) to prevent cross-site request forgery (when used with the `[ValidateAntiForgeryToken]` attribute in the HTTP Post action method)</span></span>

* <span data-ttu-id="370b5-115">Fornece o atributo `asp-route-<Parameter Name>`, em que `<Parameter Name>` é adicionado aos valores de rota.</span><span class="sxs-lookup"><span data-stu-id="370b5-115">Provides the `asp-route-<Parameter Name>` attribute, where `<Parameter Name>` is added to the route values.</span></span> <span data-ttu-id="370b5-116">Os parâmetros `routeValues` para `Html.BeginForm` e `Html.BeginRouteForm` fornecem funcionalidade semelhante.</span><span class="sxs-lookup"><span data-stu-id="370b5-116">The  `routeValues` parameters to `Html.BeginForm` and `Html.BeginRouteForm` provide similar functionality.</span></span>

* <span data-ttu-id="370b5-117">Tem uma alternativa de Auxiliar HTML `Html.BeginForm` e `Html.BeginRouteForm`</span><span class="sxs-lookup"><span data-stu-id="370b5-117">Has an HTML Helper alternative `Html.BeginForm` and `Html.BeginRouteForm`</span></span>

<span data-ttu-id="370b5-118">Amostra:</span><span class="sxs-lookup"><span data-stu-id="370b5-118">Sample:</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Demo/RegisterFormOnly.cshtml)]

<span data-ttu-id="370b5-119">O Auxiliar de marca de formulário acima gera o HTML a seguir:</span><span class="sxs-lookup"><span data-stu-id="370b5-119">The Form Tag Helper above generates the following HTML:</span></span>

```HTML
<form method="post" action="/Demo/Register">
    <!-- Input and Submit elements -->
    <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

<span data-ttu-id="370b5-120">O tempo de execução do MVC gera o valor do atributo `action` dos atributos `asp-controller` e `asp-action` do Auxiliar de marca de formulário.</span><span class="sxs-lookup"><span data-stu-id="370b5-120">The MVC runtime generates the `action` attribute value from the Form Tag Helper attributes `asp-controller` and `asp-action`.</span></span> <span data-ttu-id="370b5-121">O Auxiliar de marca de formulário também gera um [Token de verificação de solicitação](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) oculto para evitar a falsificação de solicitações entre sites (quando usado com o atributo `[ValidateAntiForgeryToken]` no método de ação HTTP Post).</span><span class="sxs-lookup"><span data-stu-id="370b5-121">The Form Tag Helper also generates a hidden [Request Verification Token](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) to prevent cross-site request forgery (when used with the `[ValidateAntiForgeryToken]` attribute in the HTTP Post action method).</span></span> <span data-ttu-id="370b5-122">É difícil proteger um Formulário HTML puro contra falsificação de solicitações entre sites e o Auxiliar de marca de formulário fornece este serviço para você.</span><span class="sxs-lookup"><span data-stu-id="370b5-122">Protecting a pure HTML Form from cross-site request forgery is difficult, the Form Tag Helper provides this service for you.</span></span>

### <a name="using-a-named-route"></a><span data-ttu-id="370b5-123">Usando uma rota nomeada</span><span class="sxs-lookup"><span data-stu-id="370b5-123">Using a named route</span></span>

<span data-ttu-id="370b5-124">O atributo do Auxiliar de Marca `asp-route` também pode gerar a marcação para o atributo HTML `action`.</span><span class="sxs-lookup"><span data-stu-id="370b5-124">The `asp-route` Tag Helper attribute can also generate markup for the HTML `action` attribute.</span></span> <span data-ttu-id="370b5-125">Um aplicativo com uma [rota](../../fundamentals/routing.md) chamada `register` poderia usar a seguinte marcação para a página de registro:</span><span class="sxs-lookup"><span data-stu-id="370b5-125">An app with a [route](../../fundamentals/routing.md)  named `register` could use the following markup for the registration page:</span></span>

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterRoute.cshtml)]

<span data-ttu-id="370b5-126">Muitas das exibições na pasta *Modos de Exibição/Conta* (gerada quando você cria um novo aplicativo Web com *Contas de usuário individuais*) contêm o atributo [asp-route-returnurl](xref:mvc/views/working-with-forms):</span><span class="sxs-lookup"><span data-stu-id="370b5-126">Many of the views in the *Views/Account* folder (generated when you create a new web app with *Individual User Accounts*) contain the [asp-route-returnurl](xref:mvc/views/working-with-forms) attribute:</span></span>

```cshtml
<form asp-controller="Account" asp-action="Login"
     asp-route-returnurl="@ViewData["ReturnUrl"]"
     method="post" class="form-horizontal" role="form">
```

>[!NOTE]
><span data-ttu-id="370b5-127">Com os modelos internos, `returnUrl` só é preenchido automaticamente quando você tenta acessar um recurso autorizado, mas não está autenticado ou autorizado.</span><span class="sxs-lookup"><span data-stu-id="370b5-127">With the built in templates, `returnUrl` is only populated automatically when you try to access an authorized resource but are not authenticated or authorized.</span></span> <span data-ttu-id="370b5-128">Quando você tenta fazer um acesso não autorizado, o middleware de segurança o redireciona para a página de logon com o `returnUrl` definido.</span><span class="sxs-lookup"><span data-stu-id="370b5-128">When you attempt an unauthorized access, the security middleware redirects you to the login page with the `returnUrl` set.</span></span>

## <a name="the-form-action-tag-helper"></a><span data-ttu-id="370b5-129">Auxiliar de Marcação de Ação de Formulário</span><span class="sxs-lookup"><span data-stu-id="370b5-129">The Form Action Tag Helper</span></span>

<span data-ttu-id="370b5-130">O Auxiliar de Marcação de Ação de Formulário gera o atributo `formaction` na marcação `<button ...>` ou `<input type="image" ...>` gerada.</span><span class="sxs-lookup"><span data-stu-id="370b5-130">The Form Action Tag Helper generates the `formaction` attribute on the generated `<button ...>` or `<input type="image" ...>` tag.</span></span> <span data-ttu-id="370b5-131">O atributo `formaction` controla onde um formulário envia seus dados.</span><span class="sxs-lookup"><span data-stu-id="370b5-131">The `formaction` attribute controls where a form submits its data.</span></span> <span data-ttu-id="370b5-132">Ele se associa à [\<entrada >](https://www.w3.org/wiki/HTML/Elements/input) elementos do tipo `image` e elementos [\<botão >](https://www.w3.org/wiki/HTML/Elements/button).</span><span class="sxs-lookup"><span data-stu-id="370b5-132">It binds to [\<input>](https://www.w3.org/wiki/HTML/Elements/input) elements of type `image` and [\<button>](https://www.w3.org/wiki/HTML/Elements/button) elements.</span></span> <span data-ttu-id="370b5-133">O Auxiliar de Marcação de Ação de Formulário permite o uso de vários atributos [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `asp-` para controlar qual link `formaction` será gerado para o elemento correspondente.</span><span class="sxs-lookup"><span data-stu-id="370b5-133">The Form Action Tag Helper enables the usage of several [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `asp-` attributes to control what `formaction` link is generated for the corresponding element.</span></span>

<span data-ttu-id="370b5-134">Atributos [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) com suporte para controlar o valor de `formaction`:</span><span class="sxs-lookup"><span data-stu-id="370b5-134">Supported [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) attributes to control the value of `formaction`:</span></span>

|<span data-ttu-id="370b5-135">Atributo</span><span class="sxs-lookup"><span data-stu-id="370b5-135">Attribute</span></span>|<span data-ttu-id="370b5-136">DESCRIÇÃO</span><span class="sxs-lookup"><span data-stu-id="370b5-136">Description</span></span>|
|---|---|
|[<span data-ttu-id="370b5-137">asp-controller</span><span class="sxs-lookup"><span data-stu-id="370b5-137">asp-controller</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-controller)|<span data-ttu-id="370b5-138">O nome do controlador.</span><span class="sxs-lookup"><span data-stu-id="370b5-138">The name of the controller.</span></span>|
|[<span data-ttu-id="370b5-139">asp-action</span><span class="sxs-lookup"><span data-stu-id="370b5-139">asp-action</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-action)|<span data-ttu-id="370b5-140">O nome do método da ação.</span><span class="sxs-lookup"><span data-stu-id="370b5-140">The name of the action method.</span></span>|
|[<span data-ttu-id="370b5-141">asp-area</span><span class="sxs-lookup"><span data-stu-id="370b5-141">asp-area</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-area)|<span data-ttu-id="370b5-142">O nome da área.</span><span class="sxs-lookup"><span data-stu-id="370b5-142">The name of the area.</span></span>|
|[<span data-ttu-id="370b5-143">asp-page</span><span class="sxs-lookup"><span data-stu-id="370b5-143">asp-page</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-page)|<span data-ttu-id="370b5-144">O nome da Página do Razor.</span><span class="sxs-lookup"><span data-stu-id="370b5-144">The name of the Razor page.</span></span>|
|[<span data-ttu-id="370b5-145">asp-page-handler</span><span class="sxs-lookup"><span data-stu-id="370b5-145">asp-page-handler</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-page-handler)|<span data-ttu-id="370b5-146">O nome do manipulador da Página do Razor.</span><span class="sxs-lookup"><span data-stu-id="370b5-146">The name of the Razor page handler.</span></span>|
|[<span data-ttu-id="370b5-147">asp-route</span><span class="sxs-lookup"><span data-stu-id="370b5-147">asp-route</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-route)|<span data-ttu-id="370b5-148">O nome da rota.</span><span class="sxs-lookup"><span data-stu-id="370b5-148">The name of the route.</span></span>|
|[<span data-ttu-id="370b5-149">asp-route-{value}</span><span class="sxs-lookup"><span data-stu-id="370b5-149">asp-route-{value}</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-route-value)|<span data-ttu-id="370b5-150">Um único valor de rota de URL.</span><span class="sxs-lookup"><span data-stu-id="370b5-150">A single URL route value.</span></span> <span data-ttu-id="370b5-151">Por exemplo, `asp-route-id="1234"`.</span><span class="sxs-lookup"><span data-stu-id="370b5-151">For example, `asp-route-id="1234"`.</span></span>|
|[<span data-ttu-id="370b5-152">asp-all-route-data</span><span class="sxs-lookup"><span data-stu-id="370b5-152">asp-all-route-data</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-all-route-data)|<span data-ttu-id="370b5-153">Todos os valores de rota.</span><span class="sxs-lookup"><span data-stu-id="370b5-153">All route values.</span></span>|
|[<span data-ttu-id="370b5-154">asp-fragment</span><span class="sxs-lookup"><span data-stu-id="370b5-154">asp-fragment</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-fragment)|<span data-ttu-id="370b5-155">O fragmento de URL.</span><span class="sxs-lookup"><span data-stu-id="370b5-155">The URL fragment.</span></span>|

### <a name="submit-to-controller-example"></a><span data-ttu-id="370b5-156">Exemplo de Enviar ao controlador</span><span class="sxs-lookup"><span data-stu-id="370b5-156">Submit to controller example</span></span>

<span data-ttu-id="370b5-157">A marcação a seguir envia o formulário à ação `Index` de `HomeController` quando a entrada ou botão são escolhidos:</span><span class="sxs-lookup"><span data-stu-id="370b5-157">The following markup submits the form to the `Index` action of `HomeController` when the input or button are selected:</span></span>

```cshtml
<form method="post">
    <button asp-controller="Home" asp-action="Index">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-controller="Home" 
                                asp-action="Index">
</form>
```

<span data-ttu-id="370b5-158">A marcação anterior gera o seguinte HTML:</span><span class="sxs-lookup"><span data-stu-id="370b5-158">The previous markup generates following HTML:</span></span>

```html
<form method="post">
    <button formaction="/Home">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/Home">
</form>
```

### <a name="submit-to-page-example"></a><span data-ttu-id="370b5-159">Exemplo de Enviar a uma página</span><span class="sxs-lookup"><span data-stu-id="370b5-159">Submit to page example</span></span>

<span data-ttu-id="370b5-160">A marcação a seguir envia o formulário à Página do Razor `About`:</span><span class="sxs-lookup"><span data-stu-id="370b5-160">The following markup submits the form to the `About` Razor Page:</span></span>

```cshtml
<form method="post">
    <button asp-page="About">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-page="About">
</form>
```

<span data-ttu-id="370b5-161">A marcação anterior gera o seguinte HTML:</span><span class="sxs-lookup"><span data-stu-id="370b5-161">The previous markup generates following HTML:</span></span>

```html
<form method="post">
    <button formaction="/About">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/About">
</form>
```

### <a name="submit-to-route-example"></a><span data-ttu-id="370b5-162">Exemplo de Enviar a uma rota</span><span class="sxs-lookup"><span data-stu-id="370b5-162">Submit to route example</span></span>

<span data-ttu-id="370b5-163">Considere o ponto de extremidade `/Home/Test`:</span><span class="sxs-lookup"><span data-stu-id="370b5-163">Consider the `/Home/Test` endpoint:</span></span>

```csharp
public class HomeController : Controller
{
    [Route("/Home/Test", Name = "Custom")]
    public string Test()
    {
        return "This is the test page";
    }
}
```

<span data-ttu-id="370b5-164">A marcação a seguir envia o formulário ao ponto de extremidade `/Home/Test`.</span><span class="sxs-lookup"><span data-stu-id="370b5-164">The following markup submits the form to the `/Home/Test` endpoint.</span></span>

```cshtml
<form method="post">
    <button asp-route="Custom">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-route="Custom">
</form>
```

<span data-ttu-id="370b5-165">A marcação anterior gera o seguinte HTML:</span><span class="sxs-lookup"><span data-stu-id="370b5-165">The previous markup generates following HTML:</span></span>

```html
<form method="post">
    <button formaction="/Home/Test">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/Home/Test">
</form>
```

## <a name="the-input-tag-helper"></a><span data-ttu-id="370b5-166">O auxiliar de marca de entrada</span><span class="sxs-lookup"><span data-stu-id="370b5-166">The Input Tag Helper</span></span>

<span data-ttu-id="370b5-167">O Auxiliar de marca de entrada associa um elemento HTML [\<input>](https://www.w3.org/wiki/HTML/Elements/input) a uma expressão de modelo em sua exibição do Razor.</span><span class="sxs-lookup"><span data-stu-id="370b5-167">The Input Tag Helper binds an HTML [\<input>](https://www.w3.org/wiki/HTML/Elements/input) element to a model expression in your razor view.</span></span>

<span data-ttu-id="370b5-168">Sintaxe:</span><span class="sxs-lookup"><span data-stu-id="370b5-168">Syntax:</span></span>

```HTML
<input asp-for="<Expression Name>">
```

<span data-ttu-id="370b5-169">O auxiliar de marca de entrada:</span><span class="sxs-lookup"><span data-stu-id="370b5-169">The Input Tag Helper:</span></span>

* <span data-ttu-id="370b5-170">Gera os atributos HTML `id` e `name` para o nome da expressão especificada no atributo `asp-for`.</span><span class="sxs-lookup"><span data-stu-id="370b5-170">Generates the `id` and `name` HTML attributes for the expression name specified in the `asp-for` attribute.</span></span> <span data-ttu-id="370b5-171">`asp-for="Property1.Property2"` equivale a `m => m.Property1.Property2`.</span><span class="sxs-lookup"><span data-stu-id="370b5-171">`asp-for="Property1.Property2"` is equivalent to `m => m.Property1.Property2`.</span></span> <span data-ttu-id="370b5-172">O nome da expressão é o que é usado para o valor do atributo `asp-for`.</span><span class="sxs-lookup"><span data-stu-id="370b5-172">The name of the expression is what is used for the `asp-for` attribute value.</span></span> <span data-ttu-id="370b5-173">Consulte a seção [Nomes de expressão](#expression-names) para obter informações adicionais.</span><span class="sxs-lookup"><span data-stu-id="370b5-173">See the [Expression names](#expression-names) section for additional information.</span></span>

* <span data-ttu-id="370b5-174">Define o valor do atributo HTML `type` com base nos atributos de tipo de modelo e [anotação de dados](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) aplicados à propriedade de modelo</span><span class="sxs-lookup"><span data-stu-id="370b5-174">Sets the HTML `type` attribute value based on the model type and  [data annotation](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) attributes applied to the model property</span></span>

* <span data-ttu-id="370b5-175">O valor do atributo HTML `type` não será substituído quando um for especificado</span><span class="sxs-lookup"><span data-stu-id="370b5-175">Won't overwrite the HTML `type` attribute value when one is specified</span></span>

* <span data-ttu-id="370b5-176">Gera atributos de validação [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) de atributos de [anotação de dados](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) aplicados a propriedades de modelo</span><span class="sxs-lookup"><span data-stu-id="370b5-176">Generates [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5)  validation attributes from [data annotation](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) attributes applied to model properties</span></span>

* <span data-ttu-id="370b5-177">Tem uma sobreposição de recursos de Auxiliar HTML com `Html.TextBoxFor` e `Html.EditorFor`.</span><span class="sxs-lookup"><span data-stu-id="370b5-177">Has an HTML Helper feature overlap with `Html.TextBoxFor` and `Html.EditorFor`.</span></span> <span data-ttu-id="370b5-178">Consulte a seção **Alternativas de Auxiliar HTML ao Auxiliar de marca de entrada** para obter detalhes.</span><span class="sxs-lookup"><span data-stu-id="370b5-178">See the **HTML Helper alternatives to Input Tag Helper** section for details.</span></span>

* <span data-ttu-id="370b5-179">Fornece tipagem forte.</span><span class="sxs-lookup"><span data-stu-id="370b5-179">Provides strong typing.</span></span> <span data-ttu-id="370b5-180">Se o nome da propriedade for alterado e você não atualizar o Auxiliar de marca, você verá um erro semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="370b5-180">If the name of the property changes and you don't update the Tag Helper you'll get an error similar to the following:</span></span>

```HTML
An error occurred during the compilation of a resource required to process
this request. Please review the following specific error details and modify
your source code appropriately.

Type expected
 'RegisterViewModel' does not contain a definition for 'Email' and no
 extension method 'Email' accepting a first argument of type 'RegisterViewModel'
 could be found (are you missing a using directive or an assembly reference?)
```

<span data-ttu-id="370b5-181">O Auxiliar de marca `Input` define o atributo HTML `type` com base no tipo .NET.</span><span class="sxs-lookup"><span data-stu-id="370b5-181">The `Input` Tag Helper sets the HTML `type` attribute based on the .NET type.</span></span> <span data-ttu-id="370b5-182">A tabela a seguir lista alguns tipos .NET comuns e o tipo HTML gerado (não estão listados todos os tipos .NET).</span><span class="sxs-lookup"><span data-stu-id="370b5-182">The following table lists some common .NET types and generated HTML type (not every .NET type is listed).</span></span>

|<span data-ttu-id="370b5-183">Tipo .NET</span><span class="sxs-lookup"><span data-stu-id="370b5-183">.NET type</span></span>|<span data-ttu-id="370b5-184">Tipo de entrada</span><span class="sxs-lookup"><span data-stu-id="370b5-184">Input Type</span></span>|
|---|---|
|<span data-ttu-id="370b5-185">Bool</span><span class="sxs-lookup"><span data-stu-id="370b5-185">Bool</span></span>|<span data-ttu-id="370b5-186">type="checkbox"</span><span class="sxs-lookup"><span data-stu-id="370b5-186">type="checkbox"</span></span>|
|<span data-ttu-id="370b5-187">Cadeia de Caracteres</span><span class="sxs-lookup"><span data-stu-id="370b5-187">String</span></span>|<span data-ttu-id="370b5-188">type="text"</span><span class="sxs-lookup"><span data-stu-id="370b5-188">type="text"</span></span>|
|<span data-ttu-id="370b5-189">DateTime</span><span class="sxs-lookup"><span data-stu-id="370b5-189">DateTime</span></span>|<span data-ttu-id="370b5-190">type=["datetime-local"](https://developer.mozilla.org/docs/Web/HTML/Element/input/datetime-local)</span><span class="sxs-lookup"><span data-stu-id="370b5-190">type=["datetime-local"](https://developer.mozilla.org/docs/Web/HTML/Element/input/datetime-local)</span></span>|
|<span data-ttu-id="370b5-191">Byte</span><span class="sxs-lookup"><span data-stu-id="370b5-191">Byte</span></span>|<span data-ttu-id="370b5-192">type="number"</span><span class="sxs-lookup"><span data-stu-id="370b5-192">type="number"</span></span>|
|<span data-ttu-id="370b5-193">int</span><span class="sxs-lookup"><span data-stu-id="370b5-193">Int</span></span>|<span data-ttu-id="370b5-194">type="number"</span><span class="sxs-lookup"><span data-stu-id="370b5-194">type="number"</span></span>|
|<span data-ttu-id="370b5-195">Single e Double</span><span class="sxs-lookup"><span data-stu-id="370b5-195">Single, Double</span></span>|<span data-ttu-id="370b5-196">type="number"</span><span class="sxs-lookup"><span data-stu-id="370b5-196">type="number"</span></span>|

<span data-ttu-id="370b5-197">A tabela a seguir mostra alguns atributos de [anotações de dados](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) comuns que o auxiliar de marca de entrada mapeará para tipos de entrada específicos (não são listados todos os atributos de validação):</span><span class="sxs-lookup"><span data-stu-id="370b5-197">The following table shows some common [data annotations](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) attributes that the input tag helper will map to specific input types (not every validation attribute is listed):</span></span>

|<span data-ttu-id="370b5-198">Atributo</span><span class="sxs-lookup"><span data-stu-id="370b5-198">Attribute</span></span>|<span data-ttu-id="370b5-199">Tipo de entrada</span><span class="sxs-lookup"><span data-stu-id="370b5-199">Input Type</span></span>|
|---|---|
|<span data-ttu-id="370b5-200">[EmailAddress]</span><span class="sxs-lookup"><span data-stu-id="370b5-200">[EmailAddress]</span></span>|<span data-ttu-id="370b5-201">type="email"</span><span class="sxs-lookup"><span data-stu-id="370b5-201">type="email"</span></span>|
|<span data-ttu-id="370b5-202">[Url]</span><span class="sxs-lookup"><span data-stu-id="370b5-202">[Url]</span></span>|<span data-ttu-id="370b5-203">type="url"</span><span class="sxs-lookup"><span data-stu-id="370b5-203">type="url"</span></span>|
|<span data-ttu-id="370b5-204">[HiddenInput]</span><span class="sxs-lookup"><span data-stu-id="370b5-204">[HiddenInput]</span></span>|<span data-ttu-id="370b5-205">type="hidden"</span><span class="sxs-lookup"><span data-stu-id="370b5-205">type="hidden"</span></span>|
|<span data-ttu-id="370b5-206">[Phone]</span><span class="sxs-lookup"><span data-stu-id="370b5-206">[Phone]</span></span>|<span data-ttu-id="370b5-207">type="tel"</span><span class="sxs-lookup"><span data-stu-id="370b5-207">type="tel"</span></span>|
|<span data-ttu-id="370b5-208">[DataType(DataType.Password)]</span><span class="sxs-lookup"><span data-stu-id="370b5-208">[DataType(DataType.Password)]</span></span>|<span data-ttu-id="370b5-209">type="password"</span><span class="sxs-lookup"><span data-stu-id="370b5-209">type="password"</span></span>|
|<span data-ttu-id="370b5-210">[DataType(DataType.Date)]</span><span class="sxs-lookup"><span data-stu-id="370b5-210">[DataType(DataType.Date)]</span></span>|<span data-ttu-id="370b5-211">type="date"</span><span class="sxs-lookup"><span data-stu-id="370b5-211">type="date"</span></span>|
|<span data-ttu-id="370b5-212">[DataType(DataType.Time)]</span><span class="sxs-lookup"><span data-stu-id="370b5-212">[DataType(DataType.Time)]</span></span>|<span data-ttu-id="370b5-213">type="time"</span><span class="sxs-lookup"><span data-stu-id="370b5-213">type="time"</span></span>|

<span data-ttu-id="370b5-214">Amostra:</span><span class="sxs-lookup"><span data-stu-id="370b5-214">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/RegisterViewModel.cs)]

[!code-HTML[](working-with-forms/sample/final/Views/Demo/RegisterInput.cshtml)]

<span data-ttu-id="370b5-215">O código acima gera o seguinte HTML:</span><span class="sxs-lookup"><span data-stu-id="370b5-215">The code above generates the following HTML:</span></span>

```HTML
  <form method="post" action="/Demo/RegisterInput">
      Email:
      <input type="email" data-val="true"
             data-val-email="The Email Address field is not a valid email address."
             data-val-required="The Email Address field is required."
             id="Email" name="Email" value=""><br>
      Password:
      <input type="password" data-val="true"
             data-val-required="The Password field is required."
             id="Password" name="Password"><br>
      <button type="submit">Register</button>
      <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
   </form>
```

<span data-ttu-id="370b5-216">As anotações de dados aplicadas às propriedades `Email` e `Password` geram metadados no modelo.</span><span class="sxs-lookup"><span data-stu-id="370b5-216">The data annotations applied to the `Email` and `Password` properties generate metadata on the model.</span></span> <span data-ttu-id="370b5-217">O Auxiliar de marca de entrada consome os metadados do modelo e produz atributos [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-val-*` (consulte [Validação de modelo](../models/validation.md)).</span><span class="sxs-lookup"><span data-stu-id="370b5-217">The Input Tag Helper consumes the model metadata and produces [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-val-*` attributes (see [Model Validation](../models/validation.md)).</span></span> <span data-ttu-id="370b5-218">Esses atributos descrevem os validadores a serem anexados aos campos de entrada.</span><span class="sxs-lookup"><span data-stu-id="370b5-218">These attributes describe the validators to attach to the input fields.</span></span> <span data-ttu-id="370b5-219">Isso fornece validação de [jQuery](https://jquery.com/) e HTML5 discreto.</span><span class="sxs-lookup"><span data-stu-id="370b5-219">This provides unobtrusive HTML5 and [jQuery](https://jquery.com/) validation.</span></span> <span data-ttu-id="370b5-220">Os atributos discretos têm o formato `data-val-rule="Error Message"`, em que a regra é o nome da regra de validação (como `data-val-required`, `data-val-email`, `data-val-maxlength` etc.) Se uma mensagem de erro for fornecida no atributo, ela será exibida como o valor para do atributo `data-val-rule`.</span><span class="sxs-lookup"><span data-stu-id="370b5-220">The unobtrusive attributes have the format `data-val-rule="Error Message"`, where rule is the name of the validation rule (such as `data-val-required`, `data-val-email`, `data-val-maxlength`, etc.) If an error message is provided in the attribute, it's displayed as the value for the `data-val-rule` attribute.</span></span> <span data-ttu-id="370b5-221">Também há atributos do formulário `data-val-ruleName-argumentName="argumentValue"` que fornecem detalhes adicionais sobre a regra, por exemplo, `data-val-maxlength-max="1024"`.</span><span class="sxs-lookup"><span data-stu-id="370b5-221">There are also attributes of the form `data-val-ruleName-argumentName="argumentValue"` that provide additional details about the rule, for example, `data-val-maxlength-max="1024"` .</span></span>

### <a name="html-helper-alternatives-to-input-tag-helper"></a><span data-ttu-id="370b5-222">Alternativas de Auxiliar HTML ao Auxiliar de marca de entrada</span><span class="sxs-lookup"><span data-stu-id="370b5-222">HTML Helper alternatives to Input Tag Helper</span></span>

<span data-ttu-id="370b5-223">`Html.TextBox`, `Html.TextBoxFor`, `Html.Editor` e `Html.EditorFor` têm recursos que se sobrepõem aos di Auxiliar de marca de entrada.</span><span class="sxs-lookup"><span data-stu-id="370b5-223">`Html.TextBox`, `Html.TextBoxFor`, `Html.Editor` and `Html.EditorFor` have overlapping features with the Input Tag Helper.</span></span> <span data-ttu-id="370b5-224">O Auxiliar de marca de entrada define automaticamente o atributo `type`; `Html.TextBox` e `Html.TextBoxFor` não o fazem.</span><span class="sxs-lookup"><span data-stu-id="370b5-224">The Input Tag Helper will automatically set the `type` attribute; `Html.TextBox` and `Html.TextBoxFor` won't.</span></span> <span data-ttu-id="370b5-225">`Html.Editor` e `Html.EditorFor` manipulam coleções, objetos complexos e modelos; o Auxiliar de marca de entrada não o faz.</span><span class="sxs-lookup"><span data-stu-id="370b5-225">`Html.Editor` and `Html.EditorFor` handle collections, complex objects and templates; the Input Tag Helper doesn't.</span></span> <span data-ttu-id="370b5-226">O Auxiliar de marca de entrada, `Html.EditorFor` e `Html.TextBoxFor` são fortemente tipados (eles usam expressões lambda); `Html.TextBox` e `Html.Editor` não usam (eles usam nomes de expressão).</span><span class="sxs-lookup"><span data-stu-id="370b5-226">The Input Tag Helper, `Html.EditorFor`  and  `Html.TextBoxFor` are strongly typed (they use lambda expressions); `Html.TextBox` and `Html.Editor` are not (they use expression names).</span></span>

### <a name="htmlattributes"></a><span data-ttu-id="370b5-227">HtmlAttributes</span><span class="sxs-lookup"><span data-stu-id="370b5-227">HtmlAttributes</span></span>

<span data-ttu-id="370b5-228">`@Html.Editor()` e `@Html.EditorFor()` usam uma entrada `ViewDataDictionary` especial chamada `htmlAttributes` ao executar seus modelos padrão.</span><span class="sxs-lookup"><span data-stu-id="370b5-228">`@Html.Editor()` and `@Html.EditorFor()` use a special `ViewDataDictionary` entry named `htmlAttributes` when executing their default templates.</span></span> <span data-ttu-id="370b5-229">Esse comportamento pode ser aumentado usando parâmetros `additionalViewData`.</span><span class="sxs-lookup"><span data-stu-id="370b5-229">This behavior is optionally augmented using `additionalViewData` parameters.</span></span> <span data-ttu-id="370b5-230">A chave "htmlAttributes" diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="370b5-230">The key "htmlAttributes" is case-insensitive.</span></span> <span data-ttu-id="370b5-231">A chave "htmlAttributes" é tratada de forma semelhante ao objeto `htmlAttributes` passado para auxiliares de entrada como `@Html.TextBox()`.</span><span class="sxs-lookup"><span data-stu-id="370b5-231">The key "htmlAttributes" is handled similarly to the `htmlAttributes` object passed to input helpers like `@Html.TextBox()`.</span></span>

```HTML
@Html.EditorFor(model => model.YourProperty, 
  new { htmlAttributes = new { @class="myCssClass", style="Width:100px" } })
```

### <a name="expression-names"></a><span data-ttu-id="370b5-232">Nomes de expressão</span><span class="sxs-lookup"><span data-stu-id="370b5-232">Expression names</span></span>

<span data-ttu-id="370b5-233">O valor do atributo `asp-for` é um `ModelExpression` e o lado direito de uma expressão lambda.</span><span class="sxs-lookup"><span data-stu-id="370b5-233">The `asp-for` attribute value is a `ModelExpression` and the right hand side of a lambda expression.</span></span> <span data-ttu-id="370b5-234">Portanto, `asp-for="Property1"` se torna `m => m.Property1` no código gerado e é por isso você não precisa colocar o prefixo `Model`.</span><span class="sxs-lookup"><span data-stu-id="370b5-234">Therefore, `asp-for="Property1"` becomes `m => m.Property1` in the generated code which is why you don't need to prefix with `Model`.</span></span> <span data-ttu-id="370b5-235">Você pode usar o caractere "\@" para iniciar uma expressão embutida e mover para antes de `m.`:</span><span class="sxs-lookup"><span data-stu-id="370b5-235">You can use the "\@" character to start an inline expression and move before the `m.`:</span></span>

```HTML
@{
       var joe = "Joe";
   }
   <input asp-for="@joe">
```

<span data-ttu-id="370b5-236">Gera o seguinte:</span><span class="sxs-lookup"><span data-stu-id="370b5-236">Generates the following:</span></span>

```HTML
<input type="text" id="joe" name="joe" value="Joe">
```

<span data-ttu-id="370b5-237">Com propriedades de coleção, `asp-for="CollectionProperty[23].Member"` gera o mesmo nome que `asp-for="CollectionProperty[i].Member"` quando `i` tem o valor `23`.</span><span class="sxs-lookup"><span data-stu-id="370b5-237">With collection properties, `asp-for="CollectionProperty[23].Member"` generates the same name as `asp-for="CollectionProperty[i].Member"` when `i` has the value `23`.</span></span>

<span data-ttu-id="370b5-238">Quando o ASP.NET Core MVC calcula o valor de `ModelExpression`, ele inspeciona várias fontes, inclusive o `ModelState`.</span><span class="sxs-lookup"><span data-stu-id="370b5-238">When ASP.NET Core MVC calculates the value of `ModelExpression`, it inspects several sources, including `ModelState`.</span></span> <span data-ttu-id="370b5-239">Considere o `<input type="text" asp-for="@Name">`.</span><span class="sxs-lookup"><span data-stu-id="370b5-239">Consider `<input type="text" asp-for="@Name">`.</span></span> <span data-ttu-id="370b5-240">O atributo `value` calculado é o primeiro valor não nulo:</span><span class="sxs-lookup"><span data-stu-id="370b5-240">The calculated `value` attribute is the first non-null value from:</span></span>

* <span data-ttu-id="370b5-241">Da entrada de `ModelState` com a chave "Name".</span><span class="sxs-lookup"><span data-stu-id="370b5-241">`ModelState` entry with key "Name".</span></span>
* <span data-ttu-id="370b5-242">Do resultado da expressão `Model.Name`.</span><span class="sxs-lookup"><span data-stu-id="370b5-242">Result of the expression `Model.Name`.</span></span>

### <a name="navigating-child-properties"></a><span data-ttu-id="370b5-243">Navegando para propriedades filho</span><span class="sxs-lookup"><span data-stu-id="370b5-243">Navigating child properties</span></span>

<span data-ttu-id="370b5-244">Você também pode navegar para propriedades filho usando o caminho da propriedade do modelo de exibição.</span><span class="sxs-lookup"><span data-stu-id="370b5-244">You can also navigate to child properties using the property path of the view model.</span></span> <span data-ttu-id="370b5-245">Considere uma classe de modelo mais complexa que contém uma propriedade `Address` filho.</span><span class="sxs-lookup"><span data-stu-id="370b5-245">Consider a more complex model class that contains a child `Address` property.</span></span>

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/AddressViewModel.cs?highlight=1,2,3,4&range=5-8)]

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/RegisterAddressViewModel.cs?highlight=8&range=5-13)]

<span data-ttu-id="370b5-246">Na exibição, associamos a `Address.AddressLine1`:</span><span class="sxs-lookup"><span data-stu-id="370b5-246">In the view, we bind to `Address.AddressLine1`:</span></span>

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterAddress.cshtml?highlight=6)]

<span data-ttu-id="370b5-247">O HTML a seguir é gerado para `Address.AddressLine1`:</span><span class="sxs-lookup"><span data-stu-id="370b5-247">The following HTML is generated for `Address.AddressLine1`:</span></span>

```HTML
<input type="text" id="Address_AddressLine1" name="Address.AddressLine1" value="">
```

### <a name="expression-names-and-collections"></a><span data-ttu-id="370b5-248">Nomes de expressão e coleções</span><span class="sxs-lookup"><span data-stu-id="370b5-248">Expression names and Collections</span></span>

<span data-ttu-id="370b5-249">Exemplo, um modelo que contém uma matriz de `Colors`:</span><span class="sxs-lookup"><span data-stu-id="370b5-249">Sample, a model containing an array of `Colors`:</span></span>

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/Person.cs?highlight=3&range=5-10)]

<span data-ttu-id="370b5-250">O método de ação:</span><span class="sxs-lookup"><span data-stu-id="370b5-250">The action method:</span></span>

```csharp
public IActionResult Edit(int id, int colorIndex)
   {
       ViewData["Index"] = colorIndex;
       return View(GetPerson(id));
   }
```

<span data-ttu-id="370b5-251">O Razor a seguir mostra como você acessa um elemento `Color` específico:</span><span class="sxs-lookup"><span data-stu-id="370b5-251">The following Razor shows how you access a specific `Color` element:</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Demo/EditColor.cshtml)]

<span data-ttu-id="370b5-252">O modelo *Views/Shared/EditorTemplates/String.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="370b5-252">The *Views/Shared/EditorTemplates/String.cshtml* template:</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Shared/EditorTemplates/String.cshtml)]

<span data-ttu-id="370b5-253">Exemplo usando `List<T>`:</span><span class="sxs-lookup"><span data-stu-id="370b5-253">Sample using `List<T>`:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/ToDoItem.cs?range=3-8)]

<span data-ttu-id="370b5-254">O Razor a seguir mostra como iterar em uma coleção:</span><span class="sxs-lookup"><span data-stu-id="370b5-254">The following Razor shows how to iterate over a collection:</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Demo/Edit.cshtml)]

<span data-ttu-id="370b5-255">O modelo *Views/Shared/EditorTemplates/ToDoItem.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="370b5-255">The *Views/Shared/EditorTemplates/ToDoItem.cshtml* template:</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Shared/EditorTemplates/ToDoItem.cshtml)]

<span data-ttu-id="370b5-256">`foreach` deve ser usado, se possível, quando o valor está prestes a ser usado em um contexto equivalente `asp-for` ou `Html.DisplayFor`.</span><span class="sxs-lookup"><span data-stu-id="370b5-256">`foreach` should be used if possible when the value is going to be used in an `asp-for` or `Html.DisplayFor` equivalent context.</span></span> <span data-ttu-id="370b5-257">Em geral, `for` é melhor do que `foreach` (se o cenário permitir) porque não é necessário alocar um enumerador; no entanto, avaliar um indexador em uma expressão LINQ pode ser caro, o que deve ser minimizado.</span><span class="sxs-lookup"><span data-stu-id="370b5-257">In general, `for` is better than `foreach` (if the scenario allows it) because it doesn't need to allocate an enumerator; however, evaluating an indexer in a LINQ expression can be expensive and should be minimized.</span></span>

&nbsp;

>[!NOTE]
><span data-ttu-id="370b5-258">O código de exemplo comentado acima mostra como você substituiria a expressão lambda pelo operador `@` para acessar cada `ToDoItem` na lista.</span><span class="sxs-lookup"><span data-stu-id="370b5-258">The commented sample code above shows how you would replace the lambda expression with the `@` operator to access each `ToDoItem` in the list.</span></span>

## <a name="the-textarea-tag-helper"></a><span data-ttu-id="370b5-259">Auxiliar de marca de área de texto</span><span class="sxs-lookup"><span data-stu-id="370b5-259">The Textarea Tag Helper</span></span>

<span data-ttu-id="370b5-260">O auxiliar de marca `Textarea Tag Helper` é semelhante ao Auxiliar de marca de entrada.</span><span class="sxs-lookup"><span data-stu-id="370b5-260">The `Textarea Tag Helper` tag helper is  similar to the Input Tag Helper.</span></span>

* <span data-ttu-id="370b5-261">Gera os atributos `id` e `name`, bem como os atributos de validação de dados do modelo para um elemento [\<textarea>](https://www.w3.org/wiki/HTML/Elements/textarea).</span><span class="sxs-lookup"><span data-stu-id="370b5-261">Generates the `id` and `name` attributes, and the data validation attributes from the model for a [\<textarea>](https://www.w3.org/wiki/HTML/Elements/textarea) element.</span></span>

* <span data-ttu-id="370b5-262">Fornece tipagem forte.</span><span class="sxs-lookup"><span data-stu-id="370b5-262">Provides strong typing.</span></span>

* <span data-ttu-id="370b5-263">Alternativa de Auxiliar HTML: `Html.TextAreaFor`</span><span class="sxs-lookup"><span data-stu-id="370b5-263">HTML Helper alternative: `Html.TextAreaFor`</span></span>

<span data-ttu-id="370b5-264">Amostra:</span><span class="sxs-lookup"><span data-stu-id="370b5-264">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/DescriptionViewModel.cs)]

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterTextArea.cshtml?highlight=4)]

<span data-ttu-id="370b5-265">O HTML a seguir é gerado:</span><span class="sxs-lookup"><span data-stu-id="370b5-265">The following HTML is generated:</span></span>

```HTML
<form method="post" action="/Demo/RegisterTextArea">
  <textarea data-val="true"
   data-val-maxlength="The field Description must be a string or array type with a maximum length of &#x27;1024&#x27;."
   data-val-maxlength-max="1024"
   data-val-minlength="The field Description must be a string or array type with a minimum length of &#x27;5&#x27;."
   data-val-minlength-min="5"
   id="Description" name="Description">
  </textarea>
  <button type="submit">Test</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

## <a name="the-label-tag-helper"></a><span data-ttu-id="370b5-266">O auxiliar de marca de rótulo</span><span class="sxs-lookup"><span data-stu-id="370b5-266">The Label Tag Helper</span></span>

* <span data-ttu-id="370b5-267">Gera a legenda do rótulo e o atributo `for` em um elemento [\<rótulo>](https://www.w3.org/wiki/HTML/Elements/label) para um nome de expressão</span><span class="sxs-lookup"><span data-stu-id="370b5-267">Generates the label caption and `for` attribute on a [\<label>](https://www.w3.org/wiki/HTML/Elements/label) element for an expression name</span></span>

* <span data-ttu-id="370b5-268">Alternativa de Auxiliar HTML: `Html.LabelFor`.</span><span class="sxs-lookup"><span data-stu-id="370b5-268">HTML Helper alternative: `Html.LabelFor`.</span></span>

<span data-ttu-id="370b5-269">O `Label Tag Helper` fornece os seguintes benefícios em comparação com um elemento de rótulo HTML puro:</span><span class="sxs-lookup"><span data-stu-id="370b5-269">The `Label Tag Helper`  provides the following benefits over a pure HTML label element:</span></span>

* <span data-ttu-id="370b5-270">Você obtém automaticamente o valor do rótulo descritivo do atributo `Display`.</span><span class="sxs-lookup"><span data-stu-id="370b5-270">You automatically get the descriptive label value from the `Display` attribute.</span></span> <span data-ttu-id="370b5-271">O nome de exibição desejado pode mudar com o tempo e a combinação do atributo `Display` e do Auxiliar de Marca de Rótulo aplicará `Display` em qualquer lugar em que for usado.</span><span class="sxs-lookup"><span data-stu-id="370b5-271">The intended display name might change over time, and the combination of `Display` attribute and Label Tag Helper will apply the `Display` everywhere it's used.</span></span>

* <span data-ttu-id="370b5-272">Menos marcação no código-fonte</span><span class="sxs-lookup"><span data-stu-id="370b5-272">Less markup in source code</span></span>

* <span data-ttu-id="370b5-273">Tipagem forte com a propriedade de modelo.</span><span class="sxs-lookup"><span data-stu-id="370b5-273">Strong typing with the model property.</span></span>

<span data-ttu-id="370b5-274">Amostra:</span><span class="sxs-lookup"><span data-stu-id="370b5-274">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/SimpleViewModel.cs)]

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterLabel.cshtml?highlight=4)]

<span data-ttu-id="370b5-275">O HTML a seguir é gerado para o elemento `<label>`:</span><span class="sxs-lookup"><span data-stu-id="370b5-275">The following HTML is generated for the `<label>` element:</span></span>

```HTML
<label for="Email">Email Address</label>
```

<span data-ttu-id="370b5-276">O Auxiliar de marca de rótulo gerou o valor do atributo `for` de "Email", que é a ID associada ao elemento `<input>`.</span><span class="sxs-lookup"><span data-stu-id="370b5-276">The Label Tag Helper generated the `for` attribute value of "Email", which is the ID associated with the `<input>` element.</span></span> <span data-ttu-id="370b5-277">Auxiliares de marca geram elementos `id` e `for` consistentes para que eles possam ser associados corretamente.</span><span class="sxs-lookup"><span data-stu-id="370b5-277">The Tag Helpers generate consistent `id` and `for` elements so they can be correctly associated.</span></span> <span data-ttu-id="370b5-278">A legenda neste exemplo é proveniente do atributo `Display`.</span><span class="sxs-lookup"><span data-stu-id="370b5-278">The caption in this sample comes from the `Display` attribute.</span></span> <span data-ttu-id="370b5-279">Se o modelo não contivesse um atributo `Display`, a legenda seria o nome da propriedade da expressão.</span><span class="sxs-lookup"><span data-stu-id="370b5-279">If the model didn't contain a `Display` attribute, the caption would be the property name of the expression.</span></span>

## <a name="the-validation-tag-helpers"></a><span data-ttu-id="370b5-280">Os auxiliares de marca de validação</span><span class="sxs-lookup"><span data-stu-id="370b5-280">The Validation Tag Helpers</span></span>

<span data-ttu-id="370b5-281">Há dois auxiliares de marca de validação.</span><span class="sxs-lookup"><span data-stu-id="370b5-281">There are two Validation Tag Helpers.</span></span> <span data-ttu-id="370b5-282">O `Validation Message Tag Helper` (que exibe uma mensagem de validação para uma única propriedade em seu modelo) e o `Validation Summary Tag Helper` (que exibe um resumo dos erros de validação).</span><span class="sxs-lookup"><span data-stu-id="370b5-282">The `Validation Message Tag Helper` (which displays a validation message for a single property on your model), and the `Validation Summary Tag Helper` (which displays a summary of validation errors).</span></span> <span data-ttu-id="370b5-283">O `Input Tag Helper` adiciona atributos de validação do lado do cliente HTML5 para elementos de entrada baseados em atributos de anotação de dados em suas classes de modelo.</span><span class="sxs-lookup"><span data-stu-id="370b5-283">The `Input Tag Helper` adds HTML5 client side validation attributes to input elements based on data annotation attributes on your model classes.</span></span> <span data-ttu-id="370b5-284">A validação também é executada no servidor.</span><span class="sxs-lookup"><span data-stu-id="370b5-284">Validation is also performed on the server.</span></span> <span data-ttu-id="370b5-285">O Auxiliar de marca de validação exibe essas mensagens de erro quando ocorre um erro de validação.</span><span class="sxs-lookup"><span data-stu-id="370b5-285">The Validation Tag Helper displays these error messages when a validation error occurs.</span></span>

### <a name="the-validation-message-tag-helper"></a><span data-ttu-id="370b5-286">O Auxiliar de marca de mensagem de validação</span><span class="sxs-lookup"><span data-stu-id="370b5-286">The Validation Message Tag Helper</span></span>

* <span data-ttu-id="370b5-287">Adiciona o atributo `data-valmsg-for="property"` [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) ao elemento [span](https://developer.mozilla.org/docs/Web/HTML/Element/span), que anexa as mensagens de erro de validação no campo de entrada da propriedade do modelo especificado.</span><span class="sxs-lookup"><span data-stu-id="370b5-287">Adds the [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5)  `data-valmsg-for="property"` attribute to the [span](https://developer.mozilla.org/docs/Web/HTML/Element/span) element, which attaches the validation error messages on the input field of the specified model property.</span></span> <span data-ttu-id="370b5-288">Quando ocorre um erro de validação do lado do cliente, [jQuery](https://jquery.com/) exibe a mensagem de erro no elemento `<span>`.</span><span class="sxs-lookup"><span data-stu-id="370b5-288">When a client side validation error occurs, [jQuery](https://jquery.com/) displays the error message in the `<span>` element.</span></span>

* <span data-ttu-id="370b5-289">A validação também é feita no servidor.</span><span class="sxs-lookup"><span data-stu-id="370b5-289">Validation also takes place on the server.</span></span> <span data-ttu-id="370b5-290">Os clientes poderão ter o JavaScript desabilitado e parte da validação só pode ser feita no lado do servidor.</span><span class="sxs-lookup"><span data-stu-id="370b5-290">Clients may have JavaScript disabled and some validation can only be done on the server side.</span></span>

* <span data-ttu-id="370b5-291">Alternativa de Auxiliar HTML: `Html.ValidationMessageFor`</span><span class="sxs-lookup"><span data-stu-id="370b5-291">HTML Helper alternative: `Html.ValidationMessageFor`</span></span>

<span data-ttu-id="370b5-292">O `Validation Message Tag Helper` é usado com o atributo `asp-validation-for` em um elemento HTML [span](https://developer.mozilla.org/docs/Web/HTML/Element/span).</span><span class="sxs-lookup"><span data-stu-id="370b5-292">The `Validation Message Tag Helper`  is used with the `asp-validation-for` attribute on a HTML [span](https://developer.mozilla.org/docs/Web/HTML/Element/span) element.</span></span>

```HTML
<span asp-validation-for="Email"></span>
```

<span data-ttu-id="370b5-293">O Auxiliar de marca de mensagem de validação gerará o HTML a seguir:</span><span class="sxs-lookup"><span data-stu-id="370b5-293">The Validation Message Tag Helper will generate the following HTML:</span></span>

```HTML
<span class="field-validation-valid"
  data-valmsg-for="Email"
  data-valmsg-replace="true"></span>
```

<span data-ttu-id="370b5-294">Geralmente, você usa o `Validation Message Tag Helper` após um Auxiliar de marca `Input` para a mesma propriedade.</span><span class="sxs-lookup"><span data-stu-id="370b5-294">You generally use the `Validation Message Tag Helper`  after an `Input` Tag Helper for the same property.</span></span> <span data-ttu-id="370b5-295">Fazer isso exibe as mensagens de erro de validação próximo à entrada que causou o erro.</span><span class="sxs-lookup"><span data-stu-id="370b5-295">Doing so displays any validation error messages near the input that caused the error.</span></span>

> [!NOTE]
> <span data-ttu-id="370b5-296">É necessário ter uma exibição com as referências de script [jQuery](https://jquery.com/) e JavaScript corretas em vigor para a validação do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="370b5-296">You must have a view with the correct JavaScript and [jQuery](https://jquery.com/) script references in place for client side validation.</span></span> <span data-ttu-id="370b5-297">Consulte [Validação de Modelo](../models/validation.md) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="370b5-297">See [Model Validation](../models/validation.md) for more information.</span></span>

<span data-ttu-id="370b5-298">Quando ocorre um erro de validação do lado do servidor (por exemplo, quando você tem validação do lado do servidor personalizada ou a validação do lado do cliente está desabilitada), o MVC coloca essa mensagem de erro como o corpo do elemento `<span>`.</span><span class="sxs-lookup"><span data-stu-id="370b5-298">When a server side validation error occurs (for example when you have custom server side validation or client-side validation is disabled), MVC places that error message as the body of the `<span>` element.</span></span>

```HTML
<span class="field-validation-error" data-valmsg-for="Email"
            data-valmsg-replace="true">
   The Email Address field is required.
</span>
```

### <a name="the-validation-summary-tag-helper"></a><span data-ttu-id="370b5-299">Auxiliar de marca de resumo de validação</span><span class="sxs-lookup"><span data-stu-id="370b5-299">The Validation Summary Tag Helper</span></span>

* <span data-ttu-id="370b5-300">Tem como alvo elementos `<div>` com o atributo `asp-validation-summary`</span><span class="sxs-lookup"><span data-stu-id="370b5-300">Targets `<div>` elements with the `asp-validation-summary` attribute</span></span>

* <span data-ttu-id="370b5-301">Alternativa de Auxiliar HTML: `@Html.ValidationSummary`</span><span class="sxs-lookup"><span data-stu-id="370b5-301">HTML Helper alternative: `@Html.ValidationSummary`</span></span>

<span data-ttu-id="370b5-302">O `Validation Summary Tag Helper` é usado para exibir um resumo das mensagens de validação.</span><span class="sxs-lookup"><span data-stu-id="370b5-302">The `Validation Summary Tag Helper`  is used to display a summary of validation messages.</span></span> <span data-ttu-id="370b5-303">O valor do atributo `asp-validation-summary` pode ser qualquer um dos seguintes:</span><span class="sxs-lookup"><span data-stu-id="370b5-303">The `asp-validation-summary` attribute value can be any of the following:</span></span>

|<span data-ttu-id="370b5-304">asp-validation-summary</span><span class="sxs-lookup"><span data-stu-id="370b5-304">asp-validation-summary</span></span>|<span data-ttu-id="370b5-305">Mensagens de validação exibidas</span><span class="sxs-lookup"><span data-stu-id="370b5-305">Validation messages displayed</span></span>|
|--- |--- |
|<span data-ttu-id="370b5-306">ValidationSummary.All</span><span class="sxs-lookup"><span data-stu-id="370b5-306">ValidationSummary.All</span></span>|<span data-ttu-id="370b5-307">Nível da propriedade e do modelo</span><span class="sxs-lookup"><span data-stu-id="370b5-307">Property and model level</span></span>|
|<span data-ttu-id="370b5-308">ValidationSummary.ModelOnly</span><span class="sxs-lookup"><span data-stu-id="370b5-308">ValidationSummary.ModelOnly</span></span>|<span data-ttu-id="370b5-309">Modelo</span><span class="sxs-lookup"><span data-stu-id="370b5-309">Model</span></span>|
|<span data-ttu-id="370b5-310">ValidationSummary.None</span><span class="sxs-lookup"><span data-stu-id="370b5-310">ValidationSummary.None</span></span>|<span data-ttu-id="370b5-311">Nenhum</span><span class="sxs-lookup"><span data-stu-id="370b5-311">None</span></span>|

### <a name="sample"></a><span data-ttu-id="370b5-312">Amostra</span><span class="sxs-lookup"><span data-stu-id="370b5-312">Sample</span></span>

<span data-ttu-id="370b5-313">No exemplo a seguir, o modelo de dados é decorado com atributos `DataAnnotation`, o que gera mensagens de erro de validação no elemento `<input>`.</span><span class="sxs-lookup"><span data-stu-id="370b5-313">In the following example, the data model is decorated with `DataAnnotation` attributes, which generates validation error messages on the `<input>` element.</span></span>  <span data-ttu-id="370b5-314">Quando ocorre um erro de validação, o Auxiliar de marca de validação exibe a mensagem de erro:</span><span class="sxs-lookup"><span data-stu-id="370b5-314">When a validation error occurs, the Validation Tag Helper displays the error message:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/RegisterViewModel.cs)]

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterValidation.cshtml?highlight=4,6,8&range=1-10)]

<span data-ttu-id="370b5-315">O código HTML gerado (quando o modelo é válido):</span><span class="sxs-lookup"><span data-stu-id="370b5-315">The generated HTML (when the model is valid):</span></span>

```HTML
<form action="/DemoReg/Register" method="post">
  <div class="validation-summary-valid" data-valmsg-summary="true">
  <ul><li style="display:none"></li></ul></div>
  Email:  <input name="Email" id="Email" type="email" value=""
   data-val-required="The Email field is required."
   data-val-email="The Email field is not a valid email address."
   data-val="true"><br>
  <span class="field-validation-valid" data-valmsg-replace="true"
   data-valmsg-for="Email"></span><br>
  Password: <input name="Password" id="Password" type="password"
   data-val-required="The Password field is required." data-val="true"><br>
  <span class="field-validation-valid" data-valmsg-replace="true"
   data-valmsg-for="Password"></span><br>
  <button type="submit">Register</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

## <a name="the-select-tag-helper"></a><span data-ttu-id="370b5-316">Auxiliar de Marca de Seleção</span><span class="sxs-lookup"><span data-stu-id="370b5-316">The Select Tag Helper</span></span>

* <span data-ttu-id="370b5-317">Gera [select](https://www.w3.org/wiki/HTML/Elements/select) e os elementos [option](https://www.w3.org/wiki/HTML/Elements/option) associados para as propriedades do modelo.</span><span class="sxs-lookup"><span data-stu-id="370b5-317">Generates [select](https://www.w3.org/wiki/HTML/Elements/select) and associated [option](https://www.w3.org/wiki/HTML/Elements/option) elements for properties of your model.</span></span>

* <span data-ttu-id="370b5-318">Tem uma alternativa de Auxiliar HTML `Html.DropDownListFor` e `Html.ListBoxFor`</span><span class="sxs-lookup"><span data-stu-id="370b5-318">Has an HTML Helper alternative `Html.DropDownListFor` and `Html.ListBoxFor`</span></span>

<span data-ttu-id="370b5-319">O `Select Tag Helper` `asp-for` especifica o nome da propriedade do modelo para o elemento [select](https://www.w3.org/wiki/HTML/Elements/select) e `asp-items` especifica os elementos [option](https://www.w3.org/wiki/HTML/Elements/option).</span><span class="sxs-lookup"><span data-stu-id="370b5-319">The `Select Tag Helper` `asp-for` specifies the model property  name for the [select](https://www.w3.org/wiki/HTML/Elements/select) element  and `asp-items` specifies the [option](https://www.w3.org/wiki/HTML/Elements/option) elements.</span></span>  <span data-ttu-id="370b5-320">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="370b5-320">For example:</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Home/Index.cshtml?range=4)]

<span data-ttu-id="370b5-321">Amostra:</span><span class="sxs-lookup"><span data-stu-id="370b5-321">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryViewModel.cs)]

<span data-ttu-id="370b5-322">O método `Index` inicializa o `CountryViewModel`, define o país selecionado e o transmite para a exibição `Index`.</span><span class="sxs-lookup"><span data-stu-id="370b5-322">The `Index` method initializes the `CountryViewModel`, sets the selected country and passes it to the `Index` view.</span></span>

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=8-13)]

<span data-ttu-id="370b5-323">O método `Index` HTTP POST exibe a seleção:</span><span class="sxs-lookup"><span data-stu-id="370b5-323">The HTTP POST `Index` method displays the selection:</span></span>

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=15-27)]

<span data-ttu-id="370b5-324">A exibição `Index`:</span><span class="sxs-lookup"><span data-stu-id="370b5-324">The `Index` view:</span></span>

[!code-cshtml[](working-with-forms/sample/final/Views/Home/Index.cshtml?highlight=4)]

<span data-ttu-id="370b5-325">Que gera o seguinte HTML (com "CA" selecionado):</span><span class="sxs-lookup"><span data-stu-id="370b5-325">Which generates the following HTML (with "CA" selected):</span></span>

```html
<form method="post" action="/">
     <select id="Country" name="Country">
       <option value="MX">Mexico</option>
       <option selected="selected" value="CA">Canada</option>
       <option value="US">USA</option>
     </select>
       <br /><button type="submit">Register</button>
     <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
   </form>
```

> [!NOTE]
> <span data-ttu-id="370b5-326">Não é recomendável usar `ViewBag` ou `ViewData` com o Auxiliar de Marca de Seleção.</span><span class="sxs-lookup"><span data-stu-id="370b5-326">We don't recommend using `ViewBag` or `ViewData` with the Select Tag Helper.</span></span> <span data-ttu-id="370b5-327">Um modelo de exibição é mais robusto para fornecer metadados MVC e, geralmente, menos problemático.</span><span class="sxs-lookup"><span data-stu-id="370b5-327">A view model is more robust at providing MVC metadata and generally less problematic.</span></span>

<span data-ttu-id="370b5-328">O valor do atributo `asp-for` é um caso especial e não requer um prefixo `Model`, os outros atributos do Auxiliar de marca requerem (como `asp-items`)</span><span class="sxs-lookup"><span data-stu-id="370b5-328">The `asp-for` attribute value is a special case and doesn't require a `Model` prefix, the other Tag Helper attributes do (such as `asp-items`)</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Home/Index.cshtml?range=4)]

### <a name="enum-binding"></a><span data-ttu-id="370b5-329">Associação de enumeração</span><span class="sxs-lookup"><span data-stu-id="370b5-329">Enum binding</span></span>

<span data-ttu-id="370b5-330">Geralmente, é conveniente usar `<select>` com uma propriedade `enum` e gerar os elementos `SelectListItem` dos valores `enum`.</span><span class="sxs-lookup"><span data-stu-id="370b5-330">It's often convenient to use `<select>` with an `enum` property and generate the `SelectListItem` elements from the `enum` values.</span></span>

<span data-ttu-id="370b5-331">Amostra:</span><span class="sxs-lookup"><span data-stu-id="370b5-331">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnumViewModel.cs?range=3-7)]

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnum.cs)]

<span data-ttu-id="370b5-332">O método `GetEnumSelectList` gera um objeto `SelectList` para uma enumeração.</span><span class="sxs-lookup"><span data-stu-id="370b5-332">The `GetEnumSelectList` method generates a `SelectList` object for an enum.</span></span>

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexEnum.cshtml?highlight=5)]

<span data-ttu-id="370b5-333">É possível decorar sua lista de enumeradores com o atributo `Display` para obter uma interface do usuário mais rica:</span><span class="sxs-lookup"><span data-stu-id="370b5-333">You can decorate your enumerator list with the `Display` attribute to get a richer UI:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnum.cs?highlight=5,7)]

<span data-ttu-id="370b5-334">O HTML a seguir é gerado:</span><span class="sxs-lookup"><span data-stu-id="370b5-334">The following HTML is generated:</span></span>

```HTML
  <form method="post" action="/Home/IndexEnum">
         <select data-val="true" data-val-required="The EnumCountry field is required."
                 id="EnumCountry" name="EnumCountry">
             <option value="0">United Mexican States</option>
             <option value="1">United States of America</option>
             <option value="2">Canada</option>
             <option value="3">France</option>
             <option value="4">Germany</option>
             <option selected="selected" value="5">Spain</option>
         </select>
         <br /><button type="submit">Register</button>
         <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
    </form>
```

### <a name="option-group"></a><span data-ttu-id="370b5-335">Grupo de opções</span><span class="sxs-lookup"><span data-stu-id="370b5-335">Option Group</span></span>

<span data-ttu-id="370b5-336">O elemento HTML [\<optgroup>](https://www.w3.org/wiki/HTML/Elements/optgroup) é gerado quando o modelo de exibição contém um ou mais objetos `SelectListGroup`.</span><span class="sxs-lookup"><span data-stu-id="370b5-336">The HTML  [\<optgroup>](https://www.w3.org/wiki/HTML/Elements/optgroup) element is generated when the view model contains one or more `SelectListGroup` objects.</span></span>

<span data-ttu-id="370b5-337">O `CountryViewModelGroup` agrupa os elementos `SelectListItem` nos grupos "América do Norte" e "Europa":</span><span class="sxs-lookup"><span data-stu-id="370b5-337">The `CountryViewModelGroup` groups the `SelectListItem` elements into the "North America" and "Europe" groups:</span></span>

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/CountryViewModelGroup.cs?highlight=5,6,14,20,26,32,38,44&range=6-56)]

<span data-ttu-id="370b5-338">Os dois grupos são mostrados abaixo:</span><span class="sxs-lookup"><span data-stu-id="370b5-338">The two groups are shown below:</span></span>

![exemplo de grupo de opções](working-with-forms/_static/grp.png)

<span data-ttu-id="370b5-340">O HTML gerado:</span><span class="sxs-lookup"><span data-stu-id="370b5-340">The generated HTML:</span></span>

```HTML
 <form method="post" action="/Home/IndexGroup">
      <select id="Country" name="Country">
          <optgroup label="North America">
              <option value="MEX">Mexico</option>
              <option value="CAN">Canada</option>
              <option value="US">USA</option>
          </optgroup>
          <optgroup label="Europe">
              <option value="FR">France</option>
              <option value="ES">Spain</option>
              <option value="DE">Germany</option>
          </optgroup>
      </select>
      <br /><button type="submit">Register</button>
      <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
 </form>
```

### <a name="multiple-select"></a><span data-ttu-id="370b5-341">Seleção múltipla</span><span class="sxs-lookup"><span data-stu-id="370b5-341">Multiple select</span></span>

<span data-ttu-id="370b5-342">O Auxiliar de Marca de Seleção gerará automaticamente o atributo [multiple = "multiple"](https://w3c.github.io/html-reference/select.html) se a propriedade especificada no atributo `asp-for` for um `IEnumerable`.</span><span class="sxs-lookup"><span data-stu-id="370b5-342">The Select Tag Helper  will automatically generate the [multiple = "multiple"](https://w3c.github.io/html-reference/select.html)  attribute if the property specified in the `asp-for` attribute is an `IEnumerable`.</span></span> <span data-ttu-id="370b5-343">Por exemplo, considerando o seguinte modelo:</span><span class="sxs-lookup"><span data-stu-id="370b5-343">For example, given the following model:</span></span>

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/CountryViewModelIEnumerable.cs?highlight=6)]

<span data-ttu-id="370b5-344">Com a seguinte exibição:</span><span class="sxs-lookup"><span data-stu-id="370b5-344">With the following view:</span></span>

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexMultiSelect.cshtml?highlight=4)]

<span data-ttu-id="370b5-345">Gera o seguinte HTML:</span><span class="sxs-lookup"><span data-stu-id="370b5-345">Generates the following HTML:</span></span>

```HTML
<form method="post" action="/Home/IndexMultiSelect">
    <select id="CountryCodes"
    multiple="multiple"
    name="CountryCodes"><option value="MX">Mexico</option>
<option value="CA">Canada</option>
<option value="US">USA</option>
<option value="FR">France</option>
<option value="ES">Spain</option>
<option value="DE">Germany</option>
</select>
    <br /><button type="submit">Register</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

### <a name="no-selection"></a><span data-ttu-id="370b5-346">Nenhuma seleção</span><span class="sxs-lookup"><span data-stu-id="370b5-346">No selection</span></span>

<span data-ttu-id="370b5-347">Se acabar usando a opção "não especificado" em várias páginas, você poderá criar um modelo para eliminar o HTML de repetição:</span><span class="sxs-lookup"><span data-stu-id="370b5-347">If you find yourself using the "not specified" option in multiple pages, you can create a template to eliminate repeating the HTML:</span></span>

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexEmptyTemplate.cshtml?highlight=5)]

<span data-ttu-id="370b5-348">O modelo *Views/Shared/EditorTemplates/CountryViewModel.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="370b5-348">The *Views/Shared/EditorTemplates/CountryViewModel.cshtml* template:</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Shared/EditorTemplates/CountryViewModel.cshtml)]

<span data-ttu-id="370b5-349">O acréscimo de elementos HTML [\<option>](https://www.w3.org/wiki/HTML/Elements/option) não está limitado ao caso de *Nenhuma seleção*.</span><span class="sxs-lookup"><span data-stu-id="370b5-349">Adding HTML [\<option>](https://www.w3.org/wiki/HTML/Elements/option) elements isn't limited to the *No selection* case.</span></span> <span data-ttu-id="370b5-350">Por exemplo, o seguinte método de ação e exibição gerarão HTML semelhante ao código acima:</span><span class="sxs-lookup"><span data-stu-id="370b5-350">For example, the following view and action method will generate HTML similar to the code above:</span></span>

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=114-119)]

[!code-HTML[](working-with-forms/sample/final/Views/Home/IndexOption.cshtml)]

<span data-ttu-id="370b5-351">O elemento `<option>` correto será selecionado (contém o atributo `selected="selected"`) dependendo do valor atual de `Country`.</span><span class="sxs-lookup"><span data-stu-id="370b5-351">The correct `<option>` element will be selected ( contain the `selected="selected"` attribute) depending on the current `Country` value.</span></span>

```HTML
 <form method="post" action="/Home/IndexEmpty">
      <select id="Country" name="Country">
          <option value="">&lt;none&gt;</option>
          <option value="MX">Mexico</option>
          <option value="CA" selected="selected">Canada</option>
          <option value="US">USA</option>
      </select>
      <br /><button type="submit">Register</button>
   <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
 </form>
 ```

## <a name="additional-resources"></a><span data-ttu-id="370b5-352">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="370b5-352">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/intro>
* [<span data-ttu-id="370b5-353">Elemento de formulário HTML</span><span class="sxs-lookup"><span data-stu-id="370b5-353">HTML Form element</span></span>](https://www.w3.org/TR/html401/interact/forms.html)
* [<span data-ttu-id="370b5-354">Token de verificação de solicitação</span><span class="sxs-lookup"><span data-stu-id="370b5-354">Request Verification Token</span></span>](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages)
* <xref:mvc/models/model-binding>
* <xref:mvc/models/validation>
* [<span data-ttu-id="370b5-355">Interface IAttributeAdapter</span><span class="sxs-lookup"><span data-stu-id="370b5-355">IAttributeAdapter Interface</span></span>](/dotnet/api/Microsoft.AspNetCore.Mvc.DataAnnotations.IAttributeAdapter)
* [<span data-ttu-id="370b5-356">Snippets de código para este documento</span><span class="sxs-lookup"><span data-stu-id="370b5-356">Code snippets for this document</span></span>](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/working-with-forms/sample/final)
