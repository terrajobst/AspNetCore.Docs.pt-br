---
title: Adicionar a pesquisa a um aplicativo ASP.NET Core MVC
author: rick-anderson
description: Mostra como adicionar uma pesquisa a um aplicativo ASP.NET Core MVC básico
ms.author: riande
ms.date: 12/13/2018
uid: tutorials/first-mvc-app/search
ms.openlocfilehash: e5dce35b60080ef752f8e6c6004158219015cbf5
ms.sourcegitcommit: d75d8eb26c2cce19876c8d5b65ac8a4b21f625ef
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/19/2019
ms.locfileid: "56410632"
---
# <a name="add-search-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="9bf91-103">Adicionar a pesquisa a um aplicativo ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="9bf91-103">Add search to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="9bf91-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9bf91-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="9bf91-105">Nesta seção, você adiciona a funcionalidade de pesquisa ao método de ação `Index` que permite pesquisar filmes por *gênero* ou *nome*.</span><span class="sxs-lookup"><span data-stu-id="9bf91-105">In this section, you add search capability to the `Index` action method that lets you search movies by *genre* or *name*.</span></span>

<span data-ttu-id="9bf91-106">Atualize o método `Index` pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="9bf91-106">Update the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_1stSearch)]

<span data-ttu-id="9bf91-107">A primeira linha do método de ação `Index` cria uma consulta [LINQ](/dotnet/standard/using-linq) para selecionar os filmes:</span><span class="sxs-lookup"><span data-stu-id="9bf91-107">The first line of the `Index` action method creates a [LINQ](/dotnet/standard/using-linq) query to select the movies:</span></span>

```csharp
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="9bf91-108">A consulta é *somente* definida neste ponto; ela **não** foi executada no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="9bf91-108">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="9bf91-109">Se o parâmetro `searchString` contiver uma cadeia de caracteres, a consulta de filmes será modificada para filtrar o valor da cadeia de caracteres de pesquisa:</span><span class="sxs-lookup"><span data-stu-id="9bf91-109">If the `searchString` parameter contains a string, the movies query is modified to filter on the value of the search string:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchNull2)]

<span data-ttu-id="9bf91-110">O código `s => s.Title.Contains()` acima é uma [Expressão Lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="9bf91-110">The `s => s.Title.Contains()` code above is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="9bf91-111">Lambdas são usados em consultas [LINQ](/dotnet/standard/using-linq) baseadas em método como argumentos para métodos de operadores de consulta padrão, como o método [Where](/dotnet/api/system.linq.enumerable.where) ou `Contains` (usado no código acima).</span><span class="sxs-lookup"><span data-stu-id="9bf91-111">Lambdas are used in method-based [LINQ](/dotnet/standard/using-linq) queries as arguments to standard query operator methods such as the [Where](/dotnet/api/system.linq.enumerable.where) method or `Contains` (used in the code above).</span></span> <span data-ttu-id="9bf91-112">As consultas LINQ não são executadas quando são definidas ou no momento em que são modificadas com uma chamada a um método, como `Where`, `Contains` ou `OrderBy`.</span><span class="sxs-lookup"><span data-stu-id="9bf91-112">LINQ queries are not executed when they're defined or when they're modified by calling a method such as `Where`, `Contains`, or `OrderBy`.</span></span> <span data-ttu-id="9bf91-113">Em vez disso, a execução da consulta é adiada.</span><span class="sxs-lookup"><span data-stu-id="9bf91-113">Rather, query execution is deferred.</span></span>  <span data-ttu-id="9bf91-114">Isso significa que a avaliação de uma expressão é atrasada até que seu valor realizado seja, de fato, iterado ou o método `ToListAsync` seja chamado.</span><span class="sxs-lookup"><span data-stu-id="9bf91-114">That means that the evaluation of an expression is delayed until its realized value is actually iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="9bf91-115">Para obter mais informações sobre a execução de consulta adiada, consulte [Execução da consulta](/dotnet/framework/data/adonet/ef/language-reference/query-execution).</span><span class="sxs-lookup"><span data-stu-id="9bf91-115">For more information about deferred query execution, see [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution).</span></span>

<span data-ttu-id="9bf91-116">Observação: o método [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) é executado no banco de dados, não no código C# mostrado acima.</span><span class="sxs-lookup"><span data-stu-id="9bf91-116">Note: The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the c# code shown above.</span></span> <span data-ttu-id="9bf91-117">A diferenciação de maiúsculas e minúsculas na consulta depende do banco de dados e da ordenação.</span><span class="sxs-lookup"><span data-stu-id="9bf91-117">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="9bf91-118">No SQL Server, [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) é mapeado para [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), que não diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="9bf91-118">On SQL Server, [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="9bf91-119">No SQLite, com a ordenação padrão, ele diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="9bf91-119">In SQLlite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="9bf91-120">Navegue para `/Movies/Index`.</span><span class="sxs-lookup"><span data-stu-id="9bf91-120">Navigate to `/Movies/Index`.</span></span> <span data-ttu-id="9bf91-121">Acrescente uma cadeia de consulta, como `?searchString=Ghost`, à URL.</span><span class="sxs-lookup"><span data-stu-id="9bf91-121">Append a query string such as `?searchString=Ghost` to the URL.</span></span> <span data-ttu-id="9bf91-122">Os filmes filtrados são exibidos.</span><span class="sxs-lookup"><span data-stu-id="9bf91-122">The filtered movies are displayed.</span></span>

![Exibição de índice](~/tutorials/first-mvc-app/search/_static/ghost.png)

<span data-ttu-id="9bf91-124">Se você alterar a assinatura do método `Index` para que ele tenha um parâmetro chamado `id`, o parâmetro `id` corresponderá o espaço reservado `{id}` opcional com as rotas padrão definidas em *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="9bf91-124">If you change the signature of the `Index` method to have a parameter named `id`, the `id` parameter will match the optional `{id}` placeholder for the default routes set in *Startup.cs*.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]

<span data-ttu-id="9bf91-125">Altere o parâmetro para `id` e todas as ocorrências de `searchString` altere para `id`.</span><span class="sxs-lookup"><span data-stu-id="9bf91-125">Change the parameter to `id` and all occurrences of `searchString` change to `id`.</span></span>

<span data-ttu-id="9bf91-126">O método `Index` anterior:</span><span class="sxs-lookup"><span data-stu-id="9bf91-126">The previous `Index` method:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

<span data-ttu-id="9bf91-127">O método `Index` atualizado com o parâmetro `id`:</span><span class="sxs-lookup"><span data-stu-id="9bf91-127">The updated `Index` method with `id` parameter:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_SearchID)]

<span data-ttu-id="9bf91-128">Agora você pode passar o título de pesquisa como dados de rota (um segmento de URL), em vez de como um valor de cadeia de consulta.</span><span class="sxs-lookup"><span data-stu-id="9bf91-128">You can now pass the search title as route data (a URL segment) instead of as a query string value.</span></span>

![Exibição de índice com a palavra “ghost” adicionada à URL e uma lista de filmes retornados com dois filmes, Ghostbusters e Ghostbusters 2](~/tutorials/first-mvc-app/search/_static/g2.png)

<span data-ttu-id="9bf91-130">No entanto, você não pode esperar que os usuários modifiquem a URL sempre que desejarem pesquisar um filme.</span><span class="sxs-lookup"><span data-stu-id="9bf91-130">However, you can't expect users to modify the URL every time they want to search for a movie.</span></span> <span data-ttu-id="9bf91-131">Agora você adicionará os elementos da interface do usuário para ajudá-los a filtrar filmes.</span><span class="sxs-lookup"><span data-stu-id="9bf91-131">So now you'll add UI elements to help them filter movies.</span></span> <span data-ttu-id="9bf91-132">Se você tiver alterado a assinatura do método `Index` para testar como passar o parâmetro `ID` associado à rota, altere-o novamente para que ele use um parâmetro chamado `searchString`:</span><span class="sxs-lookup"><span data-stu-id="9bf91-132">If you changed the signature of the `Index` method to test how to pass the route-bound `ID` parameter, change it back so that it takes a parameter named `searchString`:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

<span data-ttu-id="9bf91-133">Abra o arquivo *Views/Movies/Index.cshtml* e, em seguida, adicione a marcação `<form>` realçada abaixo:</span><span class="sxs-lookup"><span data-stu-id="9bf91-133">Open the *Views/Movies/Index.cshtml* file, and add the `<form>` markup highlighted below:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexForm1.cshtml?highlight=10-16&range=4-21)]

<span data-ttu-id="9bf91-134">A marcação `<form>` HTML usa o [Auxiliar de Marcação de Formulário](xref:mvc/views/working-with-forms). Portanto, quando você enviar o formulário, a cadeia de caracteres de filtro será enviada para a ação `Index` do controlador de filmes.</span><span class="sxs-lookup"><span data-stu-id="9bf91-134">The HTML `<form>` tag uses the [Form Tag Helper](xref:mvc/views/working-with-forms), so when you submit the form, the filter string is posted to the `Index` action of the movies controller.</span></span> <span data-ttu-id="9bf91-135">Salve as alterações e, em seguida, teste o filtro.</span><span class="sxs-lookup"><span data-stu-id="9bf91-135">Save your changes and then test the filter.</span></span>

![Exibição de índice com a palavra “ghost” digitada na caixa de texto Filtro de título](~/tutorials/first-mvc-app/search/_static/filter.png)

<span data-ttu-id="9bf91-137">Não há nenhuma sobrecarga `[HttpPost]` do método `Index` que poderia ser esperada.</span><span class="sxs-lookup"><span data-stu-id="9bf91-137">There's no `[HttpPost]` overload of the `Index` method as you might expect.</span></span> <span data-ttu-id="9bf91-138">Isso não é necessário, porque o método não está alterando o estado do aplicativo, apenas filtrando os dados.</span><span class="sxs-lookup"><span data-stu-id="9bf91-138">You don't need it, because the method isn't changing the state of the app, just filtering data.</span></span>

<span data-ttu-id="9bf91-139">Você poderá adicionar o método `[HttpPost] Index` a seguir.</span><span class="sxs-lookup"><span data-stu-id="9bf91-139">You could add the following `[HttpPost] Index` method.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1&name=snippet_SearchPost)]

<span data-ttu-id="9bf91-140">O parâmetro `notUsed` é usado para criar uma sobrecarga para o método `Index`.</span><span class="sxs-lookup"><span data-stu-id="9bf91-140">The `notUsed` parameter is used to create an overload for the `Index` method.</span></span> <span data-ttu-id="9bf91-141">Falaremos sobre isso mais adiante no tutorial.</span><span class="sxs-lookup"><span data-stu-id="9bf91-141">We'll talk about that later in the tutorial.</span></span>

<span data-ttu-id="9bf91-142">Se você adicionar esse método, o chamador de ação fará uma correspondência com o método `[HttpPost] Index` e o método `[HttpPost] Index` será executado conforme mostrado na imagem abaixo.</span><span class="sxs-lookup"><span data-stu-id="9bf91-142">If you add this method, the action invoker would match the `[HttpPost] Index` method, and the `[HttpPost] Index` method would run as shown in the image below.</span></span>

![Janela do navegador com a resposta do aplicativo Do Índice HttpPost: filtrar ghost](~/tutorials/first-mvc-app/search/_static/fo.png)

<span data-ttu-id="9bf91-144">No entanto, mesmo se você adicionar esta versão `[HttpPost]` do método `Index`, haverá uma limitação na forma de como isso tudo foi implementado.</span><span class="sxs-lookup"><span data-stu-id="9bf91-144">However, even if you add this `[HttpPost]` version of the `Index` method, there's a limitation in how this has all been implemented.</span></span> <span data-ttu-id="9bf91-145">Imagine que você deseja adicionar uma pesquisa específica como Favoritos ou enviar um link para seus amigos para que eles possam clicar para ver a mesma lista filtrada de filmes.</span><span class="sxs-lookup"><span data-stu-id="9bf91-145">Imagine that you want to bookmark a particular search or you want to send a link to friends that they can click in order to see the same filtered list of movies.</span></span> <span data-ttu-id="9bf91-146">Observe que a URL da solicitação HTTP POST é a mesma que a URL da solicitação GET (localhost:xxxxx/Movies/Index) – não há nenhuma informação de pesquisa na URL.</span><span class="sxs-lookup"><span data-stu-id="9bf91-146">Notice that the URL for the HTTP POST request is the same as the URL for the GET request (localhost:xxxxx/Movies/Index) -- there's no search information in the URL.</span></span> <span data-ttu-id="9bf91-147">As informações de cadeia de caracteres de pesquisa são enviadas ao servidor como um [valor de campo de formulário](https://developer.mozilla.org/docs/Learn/HTML/Forms/Sending_and_retrieving_form_data).</span><span class="sxs-lookup"><span data-stu-id="9bf91-147">The search string information is sent to the server as a [form field value](https://developer.mozilla.org/docs/Learn/HTML/Forms/Sending_and_retrieving_form_data).</span></span> <span data-ttu-id="9bf91-148">Verifique isso com as ferramentas do Desenvolvedor do navegador ou a excelente [ferramenta Fiddler](http://www.telerik.com/fiddler).</span><span class="sxs-lookup"><span data-stu-id="9bf91-148">You can verify that with the browser Developer tools or the excellent [Fiddler tool](http://www.telerik.com/fiddler).</span></span> <span data-ttu-id="9bf91-149">A imagem abaixo mostra as ferramentas do Desenvolvedor do navegador Chrome:</span><span class="sxs-lookup"><span data-stu-id="9bf91-149">The image below shows the Chrome browser Developer tools:</span></span>

![Guia Rede das Ferramentas do Desenvolvedor no Microsoft Edge mostrando o corpo de uma solicitação com o valor de searchString “ghost”](~/tutorials/first-mvc-app/search/_static/f12_rb.png)

<span data-ttu-id="9bf91-151">Veja o parâmetro de pesquisa e o token [XSRF](xref:security/anti-request-forgery) no corpo da solicitação.</span><span class="sxs-lookup"><span data-stu-id="9bf91-151">You can see the search parameter and [XSRF](xref:security/anti-request-forgery) token in the request body.</span></span> <span data-ttu-id="9bf91-152">Observe, conforme mencionado no tutorial anterior, que o [Auxiliar de Marcação de Formulário](xref:mvc/views/working-with-forms) gera um token antifalsificação [XSRF](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="9bf91-152">Note, as mentioned in the previous tutorial, the [Form Tag Helper](xref:mvc/views/working-with-forms) generates an [XSRF](xref:security/anti-request-forgery) anti-forgery token.</span></span> <span data-ttu-id="9bf91-153">Não modificaremos os dados e, portanto, não precisamos validar o token no método do controlador.</span><span class="sxs-lookup"><span data-stu-id="9bf91-153">We're not modifying data, so we don't need to validate the token in the controller method.</span></span>

<span data-ttu-id="9bf91-154">Como o parâmetro de pesquisa está no corpo da solicitação e não na URL, não é possível capturar essas informações de pesquisa para adicionar como Favoritos ou compartilhar com outras pessoas.</span><span class="sxs-lookup"><span data-stu-id="9bf91-154">Because the search parameter is in the request body and not the URL, you can't capture that search information to bookmark or share with others.</span></span> <span data-ttu-id="9bf91-155">Corrigimos isso especificando que a solicitação deve ser `HTTP GET`:</span><span class="sxs-lookup"><span data-stu-id="9bf91-155">Fix this by specifying the request should be `HTTP GET`:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGet.cshtml?highlight=12&range=1-23)]

<span data-ttu-id="9bf91-156">Agora, quando você enviar uma pesquisa, a URL conterá a cadeia de consulta da pesquisa.</span><span class="sxs-lookup"><span data-stu-id="9bf91-156">Now when you submit a search, the URL contains the search query string.</span></span> <span data-ttu-id="9bf91-157">A pesquisa também irá para o método de ação `HttpGet Index`, mesmo se você tiver um método `HttpPost Index`.</span><span class="sxs-lookup"><span data-stu-id="9bf91-157">Searching will also go to the `HttpGet Index` action method, even if you have a `HttpPost Index` method.</span></span>

![Janela do navegador mostrando a searchString=ghost= na URL e os filmes retornados, Ghostbusters e Ghostbusters 2, que contêm a palavra “ghost”](~/tutorials/first-mvc-app/search/_static/search_get.png)

<span data-ttu-id="9bf91-159">A seguinte marcação mostra a alteração para a marcação `form`:</span><span class="sxs-lookup"><span data-stu-id="9bf91-159">The following markup shows the change to the `form` tag:</span></span>

```html
<form asp-controller="Movies" asp-action="Index" method="get">
   ```

## <a name="add-search-by-genre"></a><span data-ttu-id="9bf91-160">Adicionar pesquisa por gênero</span><span class="sxs-lookup"><span data-stu-id="9bf91-160">Add Search by genre</span></span>

<span data-ttu-id="9bf91-161">Adicione a seguinte classe `MovieGenreViewModel` à pasta *Models*:</span><span class="sxs-lookup"><span data-stu-id="9bf91-161">Add the following `MovieGenreViewModel` class to the *Models* folder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieGenreViewModel.cs)]

<span data-ttu-id="9bf91-162">O modelo de exibição do gênero de filme conterá:</span><span class="sxs-lookup"><span data-stu-id="9bf91-162">The movie-genre view model will contain:</span></span>

   * <span data-ttu-id="9bf91-163">Uma lista de filmes.</span><span class="sxs-lookup"><span data-stu-id="9bf91-163">A list of movies.</span></span>
   * <span data-ttu-id="9bf91-164">Uma `SelectList` que contém a lista de gêneros.</span><span class="sxs-lookup"><span data-stu-id="9bf91-164">A `SelectList` containing the list of genres.</span></span> <span data-ttu-id="9bf91-165">Isso permite que o usuário selecione um gênero na lista.</span><span class="sxs-lookup"><span data-stu-id="9bf91-165">This allows the user to select a genre from the list.</span></span>
   * <span data-ttu-id="9bf91-166">`MovieGenre`, que contém o gênero selecionado.</span><span class="sxs-lookup"><span data-stu-id="9bf91-166">`MovieGenre`, which contains the selected genre.</span></span>
   * <span data-ttu-id="9bf91-167">`SearchString`, que contém o texto que os usuários inserem na caixa de texto de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="9bf91-167">`SearchString`, which contains the text users enter in the search text box.</span></span>

<span data-ttu-id="9bf91-168">Substitua o método `Index` em `MoviesController.cs` pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="9bf91-168">Replace the `Index` method in `MoviesController.cs` with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_SearchGenre)]

<span data-ttu-id="9bf91-169">O código a seguir é uma consulta `LINQ` que recupera todos os gêneros do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="9bf91-169">The following code is a `LINQ` query that retrieves all the genres from the database.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_LINQ)]

<span data-ttu-id="9bf91-170">A `SelectList` de gêneros é criada com a projeção dos gêneros distintos (não desejamos que nossa lista de seleção tenha gêneros duplicados).</span><span class="sxs-lookup"><span data-stu-id="9bf91-170">The `SelectList` of genres is created by projecting the distinct genres (we don't want our select list to have duplicate genres).</span></span>

<span data-ttu-id="9bf91-171">Quando o usuário pesquisa o item, o valor de pesquisa é mantido na caixa de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="9bf91-171">When the user searches for the item, the search value is retained in the search box.</span></span>

## <a name="add-search-by-genre-to-the-index-view"></a><span data-ttu-id="9bf91-172">Adicionar pesquisa por gênero à exibição Índice</span><span class="sxs-lookup"><span data-stu-id="9bf91-172">Add search by genre to the Index view</span></span>

<span data-ttu-id="9bf91-173">Atualize `Index.cshtml` da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="9bf91-173">Update `Index.cshtml` as follows:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexFormGenreNoRating.cshtml?highlight=1,15,16,17,28,31,34,37,43)]

<span data-ttu-id="9bf91-174">Examine a expressão lambda usada no seguinte Auxiliar de HTML:</span><span class="sxs-lookup"><span data-stu-id="9bf91-174">Examine the lambda expression used in the following HTML Helper:</span></span>

`@Html.DisplayNameFor(model => model.Movies[0].Title)`

<span data-ttu-id="9bf91-175">No código anterior, o Auxiliar de HTML `DisplayNameFor` inspeciona a propriedade `Title` referenciada na expressão lambda para determinar o nome de exibição.</span><span class="sxs-lookup"><span data-stu-id="9bf91-175">In the preceding code, the `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="9bf91-176">Como a expressão lambda é inspecionada em vez de avaliada, você não recebe uma violação de acesso quando `model`, `model.Movies` ou `model.Movies[0]` é `null` ou vazio.</span><span class="sxs-lookup"><span data-stu-id="9bf91-176">Since the lambda expression is inspected rather than evaluated, you don't receive an access violation when `model`, `model.Movies`, or `model.Movies[0]` are `null` or empty.</span></span> <span data-ttu-id="9bf91-177">Quando a expressão lambda é avaliada (por exemplo, `@Html.DisplayFor(modelItem => item.Title)`), os valores da propriedade do modelo são avaliados.</span><span class="sxs-lookup"><span data-stu-id="9bf91-177">When the lambda expression is evaluated (for example, `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<span data-ttu-id="9bf91-178">Teste o aplicativo pesquisando por gênero, título do filme e por ambos:</span><span class="sxs-lookup"><span data-stu-id="9bf91-178">Test the app by searching by genre, by movie title, and by both:</span></span>

![Janela do navegador mostrando resultados de https://localhost:5001/Movies?MovieGenre=Comedy&SearchString=2](~/tutorials/first-mvc-app/search/_static/s2.png)

> [!div class="step-by-step"]
> <span data-ttu-id="9bf91-180">[Anterior](controller-methods-views.md)
> [Próximo](new-field.md)</span><span class="sxs-lookup"><span data-stu-id="9bf91-180">[Previous](controller-methods-views.md)
[Next](new-field.md)</span></span>  
