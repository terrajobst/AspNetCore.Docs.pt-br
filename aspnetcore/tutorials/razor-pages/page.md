---
title: Páginas do Razor geradas por scaffolding no ASP.NET Core
author: rick-anderson
description: Explica as Páginas do Razor geradas por scaffolding.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 12/4/2018
uid: tutorials/razor-pages/page
ms.openlocfilehash: 2b68523ac84596bda2914d9c348ddea8c7ea9465
ms.sourcegitcommit: cec77d5ad8a0cedb1ecbec32834111492afd0cd2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/10/2019
ms.locfileid: "54207376"
---
# <a name="scaffolded-razor-pages-in-aspnet-core"></a><span data-ttu-id="73120-103">Páginas do Razor geradas por scaffolding no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="73120-103">Scaffolded Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="73120-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="73120-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="73120-105">Este tutorial examina as Páginas do Razor criadas por scaffolding no tutorial anterior.</span><span class="sxs-lookup"><span data-stu-id="73120-105">This tutorial examines the Razor Pages created by scaffolding in the previous tutorial.</span></span>

<span data-ttu-id="73120-106">[Exiba ou baixe](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22) a amostra.</span><span class="sxs-lookup"><span data-stu-id="73120-106">[View or download](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22) sample.</span></span>

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="73120-107">As páginas Create, Delete, Details e Edit</span><span class="sxs-lookup"><span data-stu-id="73120-107">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="73120-108">Examine o Modelo de Página, *Pages/Movies/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="73120-108">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="73120-109">As Páginas do Razor são derivadas de `PageModel`.</span><span class="sxs-lookup"><span data-stu-id="73120-109">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="73120-110">Por convenção, a classe derivada de `PageModel` é chamada de `<PageName>Model`.</span><span class="sxs-lookup"><span data-stu-id="73120-110">By convention, the `PageModel`-derived class is called `<PageName>Model`.</span></span> <span data-ttu-id="73120-111">O construtor usa [injeção de dependência](xref:fundamentals/dependency-injection) para adicionar o `RazorPagesMovieContext` à página.</span><span class="sxs-lookup"><span data-stu-id="73120-111">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page.</span></span> <span data-ttu-id="73120-112">Todas as páginas geradas por scaffolding seguem esse padrão.</span><span class="sxs-lookup"><span data-stu-id="73120-112">All the scaffolded pages follow this pattern.</span></span> <span data-ttu-id="73120-113">Consulte [Código assíncrono](xref:data/ef-rp/intro#asynchronous-code) para obter mais informações sobre a programação assíncrona com o Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="73120-113">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programing with Entity Framework.</span></span>

<span data-ttu-id="73120-114">Quando uma solicitação é feita à página, o método `OnGetAsync` retorna uma lista de filmes para a Página do Razor.</span><span class="sxs-lookup"><span data-stu-id="73120-114">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="73120-115">`OnGetAsync` ou `OnGet` é chamado em uma Página do Razor para inicializar o estado da página.</span><span class="sxs-lookup"><span data-stu-id="73120-115">`OnGetAsync` or `OnGet` is called on a Razor Page to initialize the state for the page.</span></span> <span data-ttu-id="73120-116">Nesse caso, `OnGetAsync` obtém uma lista de filmes e os exibe.</span><span class="sxs-lookup"><span data-stu-id="73120-116">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="73120-117">Quando `OnGet` retorna `void` ou `OnGetAsync` retorna `Task`, então nenhum método de retorno é usado.</span><span class="sxs-lookup"><span data-stu-id="73120-117">When `OnGet` returns `void` or `OnGetAsync` returns`Task`, no return method is used.</span></span> <span data-ttu-id="73120-118">Quando o tipo de retorno for `IActionResult` ou `Task<IActionResult>`, é necessário fornecer uma instrução de retorno.</span><span class="sxs-lookup"><span data-stu-id="73120-118">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="73120-119">Por exemplo, o método `OnPostAsync` do arquivo *Pages/Movies/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="73120-119">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> <span data-ttu-id="73120-120">Examine a Página do Razor *Pages/Movies/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="73120-120">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml)]

<span data-ttu-id="73120-121">O Razor pode fazer a transição do HTML em C# ou em marcação específica do Razor.</span><span class="sxs-lookup"><span data-stu-id="73120-121">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="73120-122">Quando um símbolo `@` é seguido por uma [palavra-chave reservada do Razor](xref:mvc/views/razor#razor-reserved-keywords), ele faz a transição para marcação específica do Razor, caso contrário, ele faz a transição para C#.</span><span class="sxs-lookup"><span data-stu-id="73120-122">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

<span data-ttu-id="73120-123">A diretiva do Razor `@page` transforma o arquivo em uma ação do MVC, o que significa que ele pode manipular solicitações.</span><span class="sxs-lookup"><span data-stu-id="73120-123">The `@page` Razor directive makes the file into an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="73120-124">`@page` deve ser a primeira diretiva do Razor em uma página.</span><span class="sxs-lookup"><span data-stu-id="73120-124">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="73120-125">`@page` é um exemplo de transição para a marcação específica do Razor.</span><span class="sxs-lookup"><span data-stu-id="73120-125">`@page` is an example of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="73120-126">Consulte [Sintaxe Razor](xref:mvc/views/razor#razor-syntax) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="73120-126">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<span data-ttu-id="73120-127">Examine a expressão lambda usada no auxiliar HTML a seguir:</span><span class="sxs-lookup"><span data-stu-id="73120-127">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title))
```

<span data-ttu-id="73120-128">O auxiliar HTML `DisplayNameFor` inspeciona a propriedade `Title` referenciada na expressão lambda para determinar o nome de exibição.</span><span class="sxs-lookup"><span data-stu-id="73120-128">The `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="73120-129">A expressão lambda é inspecionada em vez de avaliada.</span><span class="sxs-lookup"><span data-stu-id="73120-129">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="73120-130">Isso significa que não há nenhuma violação de acesso quando `model`, `model.Movie` ou `model.Movie[0]` são `null` ou vazios.</span><span class="sxs-lookup"><span data-stu-id="73120-130">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` are `null` or empty.</span></span> <span data-ttu-id="73120-131">Quando a expressão lambda é avaliada (por exemplo, com `@Html.DisplayFor(modelItem => item.Title)`), os valores de propriedade do modelo são avaliados.</span><span class="sxs-lookup"><span data-stu-id="73120-131">When the lambda expression is evaluated (for example, with `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<a name="md"></a>
### <a name="the-model-directive"></a><span data-ttu-id="73120-132">A diretiva @model</span><span class="sxs-lookup"><span data-stu-id="73120-132">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="73120-133">A diretiva `@model` especifica o tipo de modelo passado para a Página do Razor.</span><span class="sxs-lookup"><span data-stu-id="73120-133">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="73120-134">No exemplo anterior, a linha `@model` torna a classe derivada de `PageModel` disponível para a Página do Razor.</span><span class="sxs-lookup"><span data-stu-id="73120-134">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="73120-135">O modelo é usado nos [auxiliares HTML](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) `@Html.DisplayNameFor` e `@Html.DisplayFor` na página.</span><span class="sxs-lookup"><span data-stu-id="73120-135">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="73120-136">A página de layout</span><span class="sxs-lookup"><span data-stu-id="73120-136">The layout page</span></span>

<span data-ttu-id="73120-137">Selecione os links de menu (**RazorPagesMovie**, **Página Inicial** e **Privacidade**).</span><span class="sxs-lookup"><span data-stu-id="73120-137">Select the menu links (**RazorPagesMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="73120-138">Cada página mostra o mesmo layout de menu.</span><span class="sxs-lookup"><span data-stu-id="73120-138">Each page shows the same menu layout.</span></span> <span data-ttu-id="73120-139">O layout de menu é implementado no arquivo *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="73120-139">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="73120-140">Abra o arquivo *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="73120-140">Open the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="73120-141">Os modelos de [layout](xref:mvc/views/layout) permitem especificar o layout de contêiner HTML do site em um lugar e, em seguida, aplicá-lo a várias páginas do site.</span><span class="sxs-lookup"><span data-stu-id="73120-141">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="73120-142">Localize a linha `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="73120-142">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="73120-143">`RenderBody` é um espaço reservado em que todas as visualizações específicas da página criadas são mostradas, *encapsuladas* na página de layout.</span><span class="sxs-lookup"><span data-stu-id="73120-143">`RenderBody` is a placeholder where all the page-specific views you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="73120-144">Por exemplo, se você selecionar o link **Privacidade**, a exibição **Pages/Privacy.cshtml** será renderizada dentro do método `RenderBody`.</span><span class="sxs-lookup"><span data-stu-id="73120-144">For example, if you select the **Privacy** link, the **Pages/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>
### <a name="viewdata-and-layout"></a><span data-ttu-id="73120-145">ViewData e layout</span><span class="sxs-lookup"><span data-stu-id="73120-145">ViewData and layout</span></span>

<span data-ttu-id="73120-146">Considere o seguinte código do arquivo *Pages/Movies/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="73120-146">Consider the following code from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="73120-147">O código realçado anterior é um exemplo de transição do Razor para C#.</span><span class="sxs-lookup"><span data-stu-id="73120-147">The preceding highlighted code is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="73120-148">Os caracteres `{` e `}` circunscrevem um bloco de código C#.</span><span class="sxs-lookup"><span data-stu-id="73120-148">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="73120-149">A classe base `PageModel` tem uma propriedade de dicionário `ViewData` que pode ser usada para adicionar os dados que você deseja passar para uma exibição.</span><span class="sxs-lookup"><span data-stu-id="73120-149">The `PageModel` base class has a `ViewData` dictionary property that can be used to add data that you want to pass to a View.</span></span> <span data-ttu-id="73120-150">Você adiciona objetos ao dicionário `ViewData` usando um padrão de chave/valor.</span><span class="sxs-lookup"><span data-stu-id="73120-150">You add objects into the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="73120-151">No exemplo anterior, a propriedade "Title" é adicionada ao dicionário `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="73120-151">In the preceding sample, the "Title" property is added to the `ViewData` dictionary.</span></span> 

<span data-ttu-id="73120-152">A propriedade "Título" é usada no arquivo *Pages/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="73120-152">The "Title" property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="73120-153">A marcação a seguir mostra as primeiras linhas do arquivo *Pages/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="73120-153">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- we need a snapshot copy of layout because we are
changing in in the next step. 
-->
[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6-99)]

<span data-ttu-id="73120-154">A linha `@*Markup removed for brevity.*@` é um comentário do Razor que não é exibida no arquivo de layout.</span><span class="sxs-lookup"><span data-stu-id="73120-154">The line `@*Markup removed for brevity.*@` is a Razor comment which doesn't appear in your layout file.</span></span> <span data-ttu-id="73120-155">Ao contrário de comentários HTML (`<!-- -->`), comentários do Razor não são enviados ao cliente.</span><span class="sxs-lookup"><span data-stu-id="73120-155">Unlike HTML comments (`<!-- -->`), Razor comments are not sent to the client.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="73120-156">Atualizar o layout</span><span class="sxs-lookup"><span data-stu-id="73120-156">Update the layout</span></span>

<span data-ttu-id="73120-157">Altere o elemento `<title>` no arquivo *Pages/Shared/_Layout.cshtml* para exibir **Movie**, em vez de **RazorPagesMovie**.</span><span class="sxs-lookup"><span data-stu-id="73120-157">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file display **Movie** rather than **RazorPagesMovie**.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]


<span data-ttu-id="73120-158">Localizar o elemento de âncora a seguir no arquivo *Pages/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="73120-158">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

<span data-ttu-id="73120-159">Substitua o elemento anterior pela marcação a seguir.</span><span class="sxs-lookup"><span data-stu-id="73120-159">Replace the preceding element with the following markup.</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="73120-160">O elemento de âncora anterior é um [Auxiliar de Marcas](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="73120-160">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="73120-161">Nesse caso, ele é o [Auxiliar de Marcas de Âncora](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="73120-161">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="73120-162">O atributo e valor do auxiliar de marcas `asp-page="/Movies/Index"` cria um link para a Página do Razor `/Movies/Index`.</span><span class="sxs-lookup"><span data-stu-id="73120-162">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="73120-163">O valor do atributo `asp-area` está vazio e, portanto, a área não é usada no link.</span><span class="sxs-lookup"><span data-stu-id="73120-163">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="73120-164">Confira [Áreas](xref:mvc/controllers/areas) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="73120-164">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="73120-165">Salve suas alterações e teste o aplicativo clicando no link **RpMovie**.</span><span class="sxs-lookup"><span data-stu-id="73120-165">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="73120-166">Confira o arquivo [_Layout.cshtml](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) no GitHub caso tenha problemas.</span><span class="sxs-lookup"><span data-stu-id="73120-166">See the [_Layout.cshtml](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="73120-167">Teste os outros links (**Home**, **RpMovie**, **Create**, **Edit** e **Delete**).</span><span class="sxs-lookup"><span data-stu-id="73120-167">Test the other links (**Home**, **RpMovie**, **Create**, **Edit**, and **Delete**).</span></span> <span data-ttu-id="73120-168">Cada página define o título, que pode ser visto na guia do navegador. Quando você coloca um indicador em uma página, o título é usado para o indicador.</span><span class="sxs-lookup"><span data-stu-id="73120-168">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span> <span data-ttu-id="73120-169">*Pages/Index.cshtml* e *Pages/Movies/Index.cshtml* atualmente têm o mesmo título, mas você pode modificá-los para terem valores diferentes.</span><span class="sxs-lookup"><span data-stu-id="73120-169">*Pages/Index.cshtml* and *Pages/Movies/Index.cshtml* currently have the same title, but you can modify them to have different values.</span></span>

> [!NOTE]
> <span data-ttu-id="73120-170">Talvez você não consiga inserir casas decimais ou vírgulas no campo `Price`.</span><span class="sxs-lookup"><span data-stu-id="73120-170">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="73120-171">Para dar suporte à [validação do jQuery](https://jqueryvalidation.org/) para localidades de idiomas diferentes do inglês que usam uma vírgula (“,”) para um ponto decimal e formatos de data diferentes do inglês dos EUA, você deve tomar medidas para globalizar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="73120-171">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize your app.</span></span> <span data-ttu-id="73120-172">Veja [Problema 4076 do GitHub](https://github.com/aspnet/Docs/issues/4076#issuecomment-326590420) para obter instruções sobre como adicionar casas decimais.</span><span class="sxs-lookup"><span data-stu-id="73120-172">This [GitHub issue 4076](https://github.com/aspnet/Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="73120-173">A propriedade `Layout` é definida no arquivo *Pages/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="73120-173">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/_ViewStart.cshtml)]

<span data-ttu-id="73120-174">A marcação anterior define o arquivo de layout como *Pages/Shared/_Layout.cshtml* para todos os arquivos do Razor na pasta *Pages*.</span><span class="sxs-lookup"><span data-stu-id="73120-174">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="73120-175">Veja [Layout](xref:razor-pages/index#layout) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="73120-175">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="73120-176">O modelo Criar página</span><span class="sxs-lookup"><span data-stu-id="73120-176">The Create page model</span></span>

<span data-ttu-id="73120-177">Examine o modelo de página *Pages/Movies/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="73120-177">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="73120-178">O método `OnGet` inicializa qualquer estado necessário para a página.</span><span class="sxs-lookup"><span data-stu-id="73120-178">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="73120-179">A página Criar não tem nenhum estado para inicializar, assim, `Page` é retornado.</span><span class="sxs-lookup"><span data-stu-id="73120-179">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="73120-180">Mais adiante no tutorial, você verá o estado de inicialização do método `OnGet`.</span><span class="sxs-lookup"><span data-stu-id="73120-180">Later in the tutorial you see `OnGet` method initialize state.</span></span> <span data-ttu-id="73120-181">O método `Page` cria um objeto `PageResult` que renderiza a página *Create.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="73120-181">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="73120-182">A propriedade `Movie` usa o atributo `[BindProperty]` para aceitar o [model binding](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="73120-182">The `Movie` property uses the `[BindProperty]` attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="73120-183">Quando o formulário Criar posta os valores de formulário, o tempo de execução do ASP.NET Core associa os valores postados ao modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="73120-183">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="73120-184">O método `OnPostAsync` é executado quando a página posta dados de formulário:</span><span class="sxs-lookup"><span data-stu-id="73120-184">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="73120-185">Se há algum erro de modelo, o formulário é reexibido juntamente com quaisquer dados de formulário postados.</span><span class="sxs-lookup"><span data-stu-id="73120-185">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="73120-186">A maioria dos erros de modelo podem ser capturados no lado do cliente antes do formulário ser enviado.</span><span class="sxs-lookup"><span data-stu-id="73120-186">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="73120-187">Um exemplo de um erro de modelo é postar, para o campo de data, um valor que não pode ser convertido em uma data.</span><span class="sxs-lookup"><span data-stu-id="73120-187">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="73120-188">A validação do lado do cliente e a validação de modelo são abordadas mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="73120-188">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="73120-189">Se não há nenhum erro de modelo, os dados são salvos e o navegador é redirecionado à página Índice.</span><span class="sxs-lookup"><span data-stu-id="73120-189">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-create-razor-page"></a><span data-ttu-id="73120-190">A Página do Razor Criar</span><span class="sxs-lookup"><span data-stu-id="73120-190">The Create Razor Page</span></span>

<span data-ttu-id="73120-191">Examine o arquivo na Página do Razor *Pages/Movies/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="73120-191">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml)]

<!-- VS -------------------------->
# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="73120-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="73120-192">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="73120-193">O Visual Studio exibe a marca `<form method="post">` em uma fonte em negrito diferente usada para Auxiliares de Marcas:</span><span class="sxs-lookup"><span data-stu-id="73120-193">Visual Studio displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers:</span></span>

<span data-ttu-id="73120-194">![Exibição do VS17 da página Create.cshtml](page/_static/th.png)
<!-- Code --------------------------></span><span class="sxs-lookup"><span data-stu-id="73120-194">![VS17 view of Create.cshtml page](page/_static/th.png)
<!-- Code --------------------------></span></span>
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="73120-195">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="73120-195">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="73120-196">Para obter mais informações sobre Auxiliares de Marcas, como `<form method="post">`, confira [Auxiliares de Marcas no ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="73120-196">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

<!-- Mac -------------------------->
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="73120-197">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="73120-197">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="73120-198">O Visual Studio para Mac exibe a marca `<form method="post">` em uma fonte em negrito diferente usada para Auxiliares de Marcas.</span><span class="sxs-lookup"><span data-stu-id="73120-198">Visual Studio for Mac displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers.</span></span>

---  
<!-- End of VS tabs -->

<span data-ttu-id="73120-199">O elemento `<form method="post">` é um [auxiliar de marcas de formulário](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="73120-199">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="73120-200">O auxiliar de marcas de formulário inclui automaticamente um [token antifalsificação](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="73120-200">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="73120-201">O mecanismo de scaffolding cria marcação do Razor para cada campo no modelo (exceto a ID) semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="73120-201">The scaffolding engine creates Razor markup for each field in the model (except the ID) similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="73120-202">Os [auxiliares de marcas de validação](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` e ` <span asp-validation-for`) exibem erros de validação.</span><span class="sxs-lookup"><span data-stu-id="73120-202">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and ` <span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="73120-203">A validação será abordada em mais detalhes posteriormente nesta série.</span><span class="sxs-lookup"><span data-stu-id="73120-203">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="73120-204">O [auxiliar de marcas de rótulo](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) gera a legenda do rótulo e o atributo `for` para a propriedade `Title`.</span><span class="sxs-lookup"><span data-stu-id="73120-204">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `for` attribute for the `Title` property.</span></span>

<span data-ttu-id="73120-205">O [auxiliar de marcas de entrada](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control" />`) usa os atributos [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) e produz os atributos HTML necessários para validação jQuery no lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="73120-205">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control" />`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="73120-206">[Anterior: Adicionar um modelo](xref:tutorials/razor-pages/model)
> [Próximo: Base de Dados](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="73120-206">[Previous: Adding a model](xref:tutorials/razor-pages/model)
[Next: Data Base](xref:tutorials/razor-pages/sql)</span></span>
