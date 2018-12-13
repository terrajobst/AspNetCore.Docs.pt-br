---
title: Adicionar a pesquisa às Páginas Razor do ASP.NET Core
author: rick-anderson
description: Mostra como adicionar uma pesquisa às Páginas Razor do ASP.NET Core
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.date: 12/3/2018
uid: tutorials/razor-pages/search
ms.openlocfilehash: 8e047024180b20e3b649085647a9136140911fee
ms.sourcegitcommit: 8a65f6c2cbe290fb2418eed58f60fb74c95392c8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2018
ms.locfileid: "52892062"
---
# <a name="add-search-to-aspnet-core-razor-pages"></a><span data-ttu-id="23854-103">Adicionar a pesquisa às Páginas Razor do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="23854-103">Add search to ASP.NET Core Razor Pages</span></span>

<span data-ttu-id="23854-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="23854-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="23854-105">Nas seções a seguir, a pesquisa de filmes por *gênero* ou *nome* é adicionada.</span><span class="sxs-lookup"><span data-stu-id="23854-105">In the following sections, searching movies by *genre* or *name* is added.</span></span>

<span data-ttu-id="23854-106">Adicione as seguintes propriedades realçadas em *Pages/Movies/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="23854-106">Add the following highlighted properties to *Pages/Movies/Index.cshtml.cs*:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* <span data-ttu-id="23854-107">`SearchString`: contém o texto que os usuários inserem na caixa de texto de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="23854-107">`SearchString`: contains the text users enter in the search text box.</span></span> <span data-ttu-id="23854-108">`SearchString` está decorada com o atributo [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute).</span><span class="sxs-lookup"><span data-stu-id="23854-108">`SearchString` is decorated with the [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute) attribute.</span></span> <span data-ttu-id="23854-109">`[BindProperty]` associa valores de formulário e cadeias de consulta ao mesmo nome da propriedade.</span><span class="sxs-lookup"><span data-stu-id="23854-109">`[BindProperty]` binds form values and query strings with the same name as the property.</span></span> <span data-ttu-id="23854-110">`(SupportsGet = true)` é necessário para a associação em solicitações GET.</span><span class="sxs-lookup"><span data-stu-id="23854-110">`(SupportsGet = true)` is required for binding on GET requests.</span></span>
* <span data-ttu-id="23854-111">`Genres`: contém a lista de gêneros.</span><span class="sxs-lookup"><span data-stu-id="23854-111">`Genres`: contains the list of genres.</span></span> <span data-ttu-id="23854-112">`Genres` permite que o usuário selecione um gênero na lista.</span><span class="sxs-lookup"><span data-stu-id="23854-112">`Genres` allows the user to select a genre from the list.</span></span> <span data-ttu-id="23854-113">`SelectList` exige `using Microsoft.AspNetCore.Mvc.Rendering;`</span><span class="sxs-lookup"><span data-stu-id="23854-113">`SelectList` requires `using Microsoft.AspNetCore.Mvc.Rendering;`</span></span>
* <span data-ttu-id="23854-114">`MovieGenre`: contém o gênero específico que o usuário seleciona (por exemplo, "Oeste").</span><span class="sxs-lookup"><span data-stu-id="23854-114">`MovieGenre`: contains the specific genre the user selects (for example, "Western").</span></span>
* <span data-ttu-id="23854-115">`Genres` e `MovieGenre` são abordados mais adiante neste tutorial.</span><span class="sxs-lookup"><span data-stu-id="23854-115">`Genres` and `MovieGenre` are used later in this tutorial.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="23854-116">Atualize o método `OnGetAsync` da página de Índice pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="23854-116">Update the Index page's `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

<span data-ttu-id="23854-117">A primeira linha do método `OnGetAsync` cria uma consulta [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) para selecionar os filmes:</span><span class="sxs-lookup"><span data-stu-id="23854-117">The first line of the `OnGetAsync` method creates a [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) query to select the movies:</span></span>

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="23854-118">A consulta é *somente* definida neste ponto; ela **não** foi executada no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="23854-118">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="23854-119">Se a propriedade `SearchString` não é nula nem vazia, a consulta de filmes é modificada para filtrar a cadeia de pesquisa:</span><span class="sxs-lookup"><span data-stu-id="23854-119">If the `SearchString` property is not null or empty, the movies query is modified to filter on the search string:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

<span data-ttu-id="23854-120">O código `s => s.Title.Contains()` é uma [Expressão Lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="23854-120">The `s => s.Title.Contains()` code is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="23854-121">Lambdas são usados em consultas [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) baseadas em método como argumentos para métodos de operadores de consulta padrão, como o método [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) ou `Contains` (usado no código anterior).</span><span class="sxs-lookup"><span data-stu-id="23854-121">Lambdas are used in method-based [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) queries as arguments to standard query operator methods such as the [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) method or `Contains` (used in the preceding code).</span></span> <span data-ttu-id="23854-122">As consultas LINQ não são executadas quando são definidas ou quando são modificadas com uma chamada a um método (como `Where`, `Contains` ou `OrderBy`).</span><span class="sxs-lookup"><span data-stu-id="23854-122">LINQ queries are not executed when they're defined or when they're modified by calling a method (such as `Where`, `Contains`  or `OrderBy`).</span></span> <span data-ttu-id="23854-123">Em vez disso, a execução da consulta é adiada.</span><span class="sxs-lookup"><span data-stu-id="23854-123">Rather, query execution is deferred.</span></span> <span data-ttu-id="23854-124">Isso significa que a avaliação de uma expressão é atrasada até que seu valor realizado seja iterado ou o método `ToListAsync` seja chamado.</span><span class="sxs-lookup"><span data-stu-id="23854-124">That means the evaluation of an expression is delayed until its realized value is iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="23854-125">Consulte [Execução de consulta](/dotnet/framework/data/adonet/ef/language-reference/query-execution) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="23854-125">See [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution) for more information.</span></span>

<span data-ttu-id="23854-126">**Observação:** o método [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) é executado no banco de dados, não no código C#.</span><span class="sxs-lookup"><span data-stu-id="23854-126">**Note:** The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the C# code.</span></span> <span data-ttu-id="23854-127">A diferenciação de maiúsculas e minúsculas na consulta depende do banco de dados e da ordenação.</span><span class="sxs-lookup"><span data-stu-id="23854-127">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="23854-128">No SQL Server, `Contains` é mapeado para [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), que não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="23854-128">On SQL Server, `Contains` maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="23854-129">No SQLite, com a ordenação padrão, ele diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="23854-129">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="23854-130">Navegue para a página Movies e acrescente uma cadeia de consulta, como `?searchString=Ghost`, à URL (por exemplo, `https://localhost:5001/Movies?searchString=Ghost`).</span><span class="sxs-lookup"><span data-stu-id="23854-130">Navigate to the Movies page and append a query string such as `?searchString=Ghost` to the URL (for example, `https://localhost:5001/Movies?searchString=Ghost`).</span></span> <span data-ttu-id="23854-131">Os filmes filtrados são exibidos.</span><span class="sxs-lookup"><span data-stu-id="23854-131">The filtered movies are displayed.</span></span>

![Exibição de índice](search/_static/ghost.png)

<span data-ttu-id="23854-133">Se o modelo de rota a seguir for adicionado à página de Índice, a cadeia de caracteres de pesquisa poderá ser passada como um segmento de URL (por exemplo, `https://localhost:5001/Movies/Ghost`).</span><span class="sxs-lookup"><span data-stu-id="23854-133">If the following route template is added to the Index page, the search string can be passed as a URL segment (for example, `https://localhost:5001/Movies/Ghost`).</span></span>

```cshtml
@page "{searchString?}"
```

<span data-ttu-id="23854-134">A restrição da rota anterior permite a pesquisa do título como dados de rota (um segmento de URL), em vez de como um valor de cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="23854-134">The preceding route constraint allows searching the title as route data (a URL segment) instead of as a query string value.</span></span>  <span data-ttu-id="23854-135">O `?` em `"{searchString?}"` significa que esse é um parâmetro de rota opcional.</span><span class="sxs-lookup"><span data-stu-id="23854-135">The `?` in `"{searchString?}"` means this is an optional route parameter.</span></span>

![Exibição de índice com a palavra “ghost” adicionada à URL e uma lista de filmes retornados com dois filmes, Ghostbusters e Ghostbusters 2](search/_static/g2.png)

<span data-ttu-id="23854-137">O tempo de execução do ASP.NET Core usa o [model binding](xref:mvc/models/model-binding) para definir o valor da propriedade `SearchString` na cadeia de consulta (`?searchString=Ghost`) ou nos dados de rota (`https://localhost:5001/Movies/Ghost`).</span><span class="sxs-lookup"><span data-stu-id="23854-137">The ASP.NET Core runtime uses [model binding](xref:mvc/models/model-binding) to set the value of the `SearchString` property from the query string (`?searchString=Ghost`) or route data (`https://localhost:5001/Movies/Ghost`).</span></span> <span data-ttu-id="23854-138">O model binding não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="23854-138">Model binding is not case sensitive.</span></span>

<span data-ttu-id="23854-139">No entanto, você não pode esperar que os usuários modifiquem a URL para pesquisar um filme.</span><span class="sxs-lookup"><span data-stu-id="23854-139">However, you can't expect users to modify the URL to search for a movie.</span></span> <span data-ttu-id="23854-140">Nesta etapa, a interface do usuário é adicionada para filtrar filmes.</span><span class="sxs-lookup"><span data-stu-id="23854-140">In this step, UI is added to filter movies.</span></span> <span data-ttu-id="23854-141">Se você adicionou a restrição de rota `"{searchString?}"`, remova-a.</span><span class="sxs-lookup"><span data-stu-id="23854-141">If you added the route constraint `"{searchString?}"`, remove it.</span></span>

<span data-ttu-id="23854-142">Abra o arquivo *Pages/Movies/Index.cshtml* e, em seguida, adicione a marcação `<form>` realçada no seguinte código:</span><span class="sxs-lookup"><span data-stu-id="23854-142">Open the *Pages/Movies/Index.cshtml* file, and add the `<form>` markup highlighted in the following code:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index2.cshtml?highlight=14-19&range=1-22)]

<span data-ttu-id="23854-143">A marca `<form>` HTML usa os seguintes [Auxiliares de Marcas](xref:mvc/views/tag-helpers/intro):</span><span class="sxs-lookup"><span data-stu-id="23854-143">The HTML `<form>` tag uses the following [Tag Helpers](xref:mvc/views/tag-helpers/intro):</span></span>

* <span data-ttu-id="23854-144">[Auxiliar de Marcas de Formulário](xref:mvc/views/working-with-forms#the-form-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="23854-144">[Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="23854-145">Quando o formulário é enviado, a cadeia de caracteres de filtro é enviada para a página *Pages/Movies/Index* por meio da cadeia de consulta.</span><span class="sxs-lookup"><span data-stu-id="23854-145">When the form is submitted, the filter string is sent to the *Pages/Movies/Index* page via query string.</span></span>
* [<span data-ttu-id="23854-146">Auxiliar de marcação de entrada</span><span class="sxs-lookup"><span data-stu-id="23854-146">Input Tag Helper</span></span>](xref:mvc/views/working-with-forms#the-input-tag-helper)

<span data-ttu-id="23854-147">Salve as alterações e teste o filtro.</span><span class="sxs-lookup"><span data-stu-id="23854-147">Save the changes and test the filter.</span></span>

![Exibição de índice com a palavra “ghost” digitada na caixa de texto Filtro de título](search/_static/filter.png)

## <a name="search-by-genre"></a><span data-ttu-id="23854-149">Pesquisar por gênero</span><span class="sxs-lookup"><span data-stu-id="23854-149">Search by genre</span></span>

<span data-ttu-id="23854-150">Atualize o método `OnGetAsync` pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="23854-150">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

<span data-ttu-id="23854-151">O código a seguir é uma consulta LINQ que recupera todos os gêneros do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="23854-151">The following code is a LINQ query that retrieves all the genres from the database.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

<span data-ttu-id="23854-152">O `SelectList` de gêneros é criado com a projeção dos gêneros distintos.</span><span class="sxs-lookup"><span data-stu-id="23854-152">The `SelectList` of genres is created by projecting the distinct genres.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-razor-page"></a><span data-ttu-id="23854-153">Adicionar pesquisa por gênero ao Razor Page</span><span class="sxs-lookup"><span data-stu-id="23854-153">Add search by genre to the Razor Page</span></span>

<span data-ttu-id="23854-154">Atualize *Index.cshtml* da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="23854-154">Update *Index.cshtml* as follows:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

<span data-ttu-id="23854-155">Teste o aplicativo pesquisando por gênero, título do filme e por ambos.</span><span class="sxs-lookup"><span data-stu-id="23854-155">Test the app by searching by genre, by movie title, and by both.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="23854-156">[Anterior: Atualizando as páginas](xref:tutorials/razor-pages/da1)
> [Próximo: Adicionando um novo campo](xref:tutorials/razor-pages/new-field)</span><span class="sxs-lookup"><span data-stu-id="23854-156">[Previous: Updating the pages](xref:tutorials/razor-pages/da1)
[Next: Adding a new field](xref:tutorials/razor-pages/new-field)</span></span>
