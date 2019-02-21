---
title: Atualizar as páginas geradas em um aplicativo ASP.NET Core
author: rick-anderson
description: Saiba como atualizar as páginas geradas em um aplicativo ASP.NET Core.
ms.author: riande
ms.date: 12/20/2018
uid: tutorials/razor-pages/da1
ms.openlocfilehash: 62385f33dc86609726305728fbc19dd9ff27dc87
ms.sourcegitcommit: d75d8eb26c2cce19876c8d5b65ac8a4b21f625ef
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/19/2019
ms.locfileid: "56410187"
---
# <a name="update-the-generated-pages-in-an-aspnet-core-app"></a><span data-ttu-id="41054-103">Atualizar as páginas geradas em um aplicativo ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="41054-103">Update the generated pages in an ASP.NET Core app</span></span>

<span data-ttu-id="41054-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="41054-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="41054-105">O aplicativo de filme gerado por scaffolding tem um bom começo, mas a apresentação não é ideal.</span><span class="sxs-lookup"><span data-stu-id="41054-105">The scaffolded movie app has a good start, but the presentation isn't ideal.</span></span> <span data-ttu-id="41054-106">**ReleaseDate** deveria ser **Data de Lançamento** (duas palavras).</span><span class="sxs-lookup"><span data-stu-id="41054-106">**ReleaseDate** should be **Release Date** (two words).</span></span>

![Aplicativo de filme aberto no Chrome](sql/_static/m55.png)

## <a name="update-the-generated-code"></a><span data-ttu-id="41054-108">Atualize o código gerado</span><span class="sxs-lookup"><span data-stu-id="41054-108">Update the generated code</span></span>

<span data-ttu-id="41054-109">Abra o arquivo *Models/Movie.cs* e adicione as linhas realçadas mostradas no seguinte código:</span><span class="sxs-lookup"><span data-stu-id="41054-109">Open the *Models/Movie.cs* file and add the highlighted lines shown in the following code:</span></span>

[!code-csharp[Main](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=12,17)]

<span data-ttu-id="41054-110">A anotação de dados `[Column(TypeName = "decimal(18, 2)")]` permite que o Entity Framework Core mapeie o `Price` corretamente para a moeda no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="41054-110">The `[Column(TypeName = "decimal(18, 2)")]` data annotation enables Entity Framework Core to correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="41054-111">Para obter mais informações, veja [Tipos de Dados](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="41054-111">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="41054-112">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) é abordado no próximo tutorial.</span><span class="sxs-lookup"><span data-stu-id="41054-112">[DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) is covered in the next tutorial.</span></span> <span data-ttu-id="41054-113">O atributo [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) especifica o que deve ser exibido no nome de um campo (neste caso, “Release Date” em vez de “ReleaseDate”).</span><span class="sxs-lookup"><span data-stu-id="41054-113">The [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) attribute specifies what to display for the name of a field (in this case "Release Date" instead of "ReleaseDate").</span></span> <span data-ttu-id="41054-114">O atributo [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) especifica o tipo de dados (Data) e, portanto, as informações de hora armazenadas no campo não são exibidas.</span><span class="sxs-lookup"><span data-stu-id="41054-114">The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (Date), so the time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="41054-115">Procure Pages/Movies e focalize um link **Editar** para ver a URL de destino.</span><span class="sxs-lookup"><span data-stu-id="41054-115">Browse to Pages/Movies and  hover over an **Edit** link to see the target URL.</span></span>

![São mostrados uma janela do navegador com o mouse sobre o link de edição e um link da URL http://localhost:1234/Movies/Edit/5](~/tutorials/razor-pages/da1/edit7.png)

<span data-ttu-id="41054-117">Os links **Editar**, **Detalhes** e **Excluir** são gerados pelo [Auxiliar de Marcação de Âncora](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) no arquivo *Pages/Movies/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="41054-117">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?highlight=16-18&range=32-)]

<span data-ttu-id="41054-118">Os [Auxiliares de Marcação](xref:mvc/views/tag-helpers/intro) permitem que o código do servidor participe da criação e renderização de elementos HTML em arquivos do Razor.</span><span class="sxs-lookup"><span data-stu-id="41054-118">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="41054-119">No código anterior, o `AnchorTagHelper` gera dinamicamente o valor do atributo `href` HTML da página Razor (a rota é relativa), o `asp-page` e a ID da rota (`asp-route-id`).</span><span class="sxs-lookup"><span data-stu-id="41054-119">In the preceding code, the `AnchorTagHelper` dynamically generates the HTML `href` attribute value from the Razor Page (the route is relative), the `asp-page`,  and the route id (`asp-route-id`).</span></span> <span data-ttu-id="41054-120">Consulte [Geração de URL para Páginas](xref:razor-pages/index#url-generation-for-pages) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="41054-120">See [URL generation for Pages](xref:razor-pages/index#url-generation-for-pages) for more information.</span></span>

<span data-ttu-id="41054-121">Use **Exibir Código-fonte** em seu navegador favorito para examinar a marcação gerada.</span><span class="sxs-lookup"><span data-stu-id="41054-121">Use **View Source** from your favorite browser to examine the generated markup.</span></span> <span data-ttu-id="41054-122">Uma parte do HTML gerado é mostrada abaixo:</span><span class="sxs-lookup"><span data-stu-id="41054-122">A portion of the generated HTML is shown below:</span></span>

```html
<td>
  <a href="/Movies/Edit?id=1">Edit</a> |
  <a href="/Movies/Details?id=1">Details</a> |
  <a href="/Movies/Delete?id=1">Delete</a>
</td>
```

<span data-ttu-id="41054-123">Os links gerados dinamicamente passam a ID de filme com uma cadeia de consulta (por exemplo, o `?id=1` em `https://localhost:5001/Movies/Details?id=1`).</span><span class="sxs-lookup"><span data-stu-id="41054-123">The dynamically-generated links pass the movie ID with a query string (for example, the `?id=1` in  `https://localhost:5001/Movies/Details?id=1`).</span></span>

<span data-ttu-id="41054-124">Atualize as Páginas Editar, Detalhes e Excluir do Razor para que elas usem o modelo de rota “{id:int}”.</span><span class="sxs-lookup"><span data-stu-id="41054-124">Update the Edit, Details, and Delete Razor Pages to use the "{id:int}" route template.</span></span> <span data-ttu-id="41054-125">Altere a diretiva de página de cada uma dessas páginas de `@page` para `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="41054-125">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span> <span data-ttu-id="41054-126">Execute o aplicativo e, em seguida, exiba o código-fonte.</span><span class="sxs-lookup"><span data-stu-id="41054-126">Run the app and then view source.</span></span> <span data-ttu-id="41054-127">O HTML gerado adiciona a ID à parte do caminho da URL:</span><span class="sxs-lookup"><span data-stu-id="41054-127">The generated HTML adds the ID to the path portion of the URL:</span></span>

```html
<td>
  <a href="/Movies/Edit/1">Edit</a> |
  <a href="/Movies/Details/1">Details</a> |
  <a href="/Movies/Delete/1">Delete</a>
</td>
```

<span data-ttu-id="41054-128">Uma solicitação para a página com o modelo de rota “{id:int}” que **não** inclui o inteiro retornará um erro HTTP 404 (não encontrado).</span><span class="sxs-lookup"><span data-stu-id="41054-128">A request to the page with the "{id:int}" route template that does **not** include the integer will return an HTTP 404 (not found) error.</span></span> <span data-ttu-id="41054-129">Por exemplo, `http://localhost:5000/Movies/Details` retornará um erro 404.</span><span class="sxs-lookup"><span data-stu-id="41054-129">For example, `http://localhost:5000/Movies/Details` will return a 404 error.</span></span> <span data-ttu-id="41054-130">Para tornar a ID opcional, acrescente `?` à restrição de rota:</span><span class="sxs-lookup"><span data-stu-id="41054-130">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="41054-131">Para testar o comportamento de `@page "{id:int?}"`:</span><span class="sxs-lookup"><span data-stu-id="41054-131">To test the behavior of `@page "{id:int?}"`:</span></span>

* <span data-ttu-id="41054-132">defina a diretiva de página em *Pages/Movies/Details.cshtml* como `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="41054-132">Set the page directive in *Pages/Movies/Details.cshtml* to `@page "{id:int?}"`.</span></span>
* <span data-ttu-id="41054-133">Defina um ponto de interrupção em `public async Task<IActionResult> OnGetAsync(int? id)` (em *Pages/Movies/Details.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="41054-133">Set a break point in `public async Task<IActionResult> OnGetAsync(int? id)` (in *Pages/Movies/Details.cshtml.cs*).</span></span>
* <span data-ttu-id="41054-134">Navegue para `https://localhost:5001/Movies/Details/`.</span><span class="sxs-lookup"><span data-stu-id="41054-134">Navigate to `https://localhost:5001/Movies/Details/`.</span></span>

<span data-ttu-id="41054-135">Com a diretiva `@page "{id:int}"`, o ponto de interrupção nunca é atingido.</span><span class="sxs-lookup"><span data-stu-id="41054-135">With the `@page "{id:int}"` directive, the break point is never hit.</span></span> <span data-ttu-id="41054-136">O mecanismo de roteamento retorna HTTP 404.</span><span class="sxs-lookup"><span data-stu-id="41054-136">The routing engine returns HTTP 404.</span></span> <span data-ttu-id="41054-137">Usando `@page "{id:int?}"`, o método `OnGetAsync` retorna `NotFound` (HTTP 404).</span><span class="sxs-lookup"><span data-stu-id="41054-137">Using `@page "{id:int?}"`, the `OnGetAsync` method returns `NotFound` (HTTP 404).</span></span>

<span data-ttu-id="41054-138">Embora não seja recomendado, você pode escrever o método `OnGetAsync` (em *Pages/Movies/Delete.cshtml.cs*) como:</span><span class="sxs-lookup"><span data-stu-id="41054-138">Although not recommended, you could write the `OnGetAsync` method (in *Pages/Movies/Delete.cshtml.cs*) as:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Delete.cshtml.cs?name=snippet)]

<span data-ttu-id="41054-139">Teste o código anterior:</span><span class="sxs-lookup"><span data-stu-id="41054-139">Test the preceding code:</span></span>

* <span data-ttu-id="41054-140">Selecione um link de **exclusão**.</span><span class="sxs-lookup"><span data-stu-id="41054-140">Select a **Delete** link.</span></span>
* <span data-ttu-id="41054-141">Remova a ID da URL.</span><span class="sxs-lookup"><span data-stu-id="41054-141">Remove the ID from the URL.</span></span> <span data-ttu-id="41054-142">Por exemplo, altere `https://localhost:5001/Movies/Delete/8` para `https://localhost:5001/Movies/Delete`.</span><span class="sxs-lookup"><span data-stu-id="41054-142">For example, change `https://localhost:5001/Movies/Delete/8` to `https://localhost:5001/Movies/Delete`.</span></span>
* <span data-ttu-id="41054-143">Execute o código em etapas no depurador.</span><span class="sxs-lookup"><span data-stu-id="41054-143">Step through the code in the debugger.</span></span>

### <a name="review-concurrency-exception-handling"></a><span data-ttu-id="41054-144">Examinar o tratamento de exceção de simultaneidade</span><span class="sxs-lookup"><span data-stu-id="41054-144">Review concurrency exception handling</span></span>

<span data-ttu-id="41054-145">Examine o método `OnPostAsync` no arquivo *Pages/Movies/Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="41054-145">Review the `OnPostAsync` method in the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="41054-146">O código anterior detecta exceções de simultaneidade quando um cliente exclui o filme e o outro cliente posta alterações no filme.</span><span class="sxs-lookup"><span data-stu-id="41054-146">The previous code detects concurrency exceptions when the one client deletes the movie and the other client posts changes to the movie.</span></span>

<span data-ttu-id="41054-147">Para testar o bloco `catch`:</span><span class="sxs-lookup"><span data-stu-id="41054-147">To test the `catch` block:</span></span>

* <span data-ttu-id="41054-148">Definir um ponto de interrupção em `catch (DbUpdateConcurrencyException)`</span><span class="sxs-lookup"><span data-stu-id="41054-148">Set a breakpoint on `catch (DbUpdateConcurrencyException)`</span></span>
* <span data-ttu-id="41054-149">Selecione **Editar** para um filme, faça alterações, mas não insira **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="41054-149">Select **Edit** for a movie, make changes, but don't enter **Save**.</span></span>
* <span data-ttu-id="41054-150">Em outra janela do navegador, selecione o link **Excluir** do mesmo filme e, em seguida, exclua o filme.</span><span class="sxs-lookup"><span data-stu-id="41054-150">In another browser window, select the **Delete** link for the same movie, and then delete the movie.</span></span>
* <span data-ttu-id="41054-151">Na janela do navegador anterior, poste as alterações no filme.</span><span class="sxs-lookup"><span data-stu-id="41054-151">In the previous browser window, post changes to the movie.</span></span>

<span data-ttu-id="41054-152">O código de produção talvez deseje detectar conflitos de simultaneidade.</span><span class="sxs-lookup"><span data-stu-id="41054-152">Production code may want to detect concurrency conflicts.</span></span> <span data-ttu-id="41054-153">Confira [Lidar com conflitos de simultaneidade](xref:data/ef-rp/concurrency) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="41054-153">See [Handle concurrency conflicts](xref:data/ef-rp/concurrency) for more information.</span></span>

### <a name="posting-and-binding-review"></a><span data-ttu-id="41054-154">Análise de postagem e associação</span><span class="sxs-lookup"><span data-stu-id="41054-154">Posting and binding review</span></span>

<span data-ttu-id="41054-155">Examine o arquivo *Pages/Movies/Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="41054-155">Examine the *Pages/Movies/Edit.cshtml.cs* file:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Edit21.cshtml.cs?name=snippet2)]

<span data-ttu-id="41054-156">Quando uma solicitação HTTP GET é feita para a página Movies/Edit (por exemplo, `http://localhost:5000/Movies/Edit/2`):</span><span class="sxs-lookup"><span data-stu-id="41054-156">When an HTTP GET request is made to the Movies/Edit page (for example, `http://localhost:5000/Movies/Edit/2`):</span></span>

* <span data-ttu-id="41054-157">O método `OnGetAsync` busca o filme do banco de dados e retorna o método `Page`.</span><span class="sxs-lookup"><span data-stu-id="41054-157">The `OnGetAsync` method fetches the movie from the database and returns the `Page` method.</span></span> 
* <span data-ttu-id="41054-158">O método `Page` renderiza a página Razor *Pages/Movies/Edit.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="41054-158">The `Page` method renders the *Pages/Movies/Edit.cshtml* Razor Page.</span></span> <span data-ttu-id="41054-159">O arquivo *Pages/Movies/Edit.cshtml* contém a diretiva de modelo (`@model RazorPagesMovie.Pages.Movies.EditModel`), que disponibiliza o modelo de filme na página.</span><span class="sxs-lookup"><span data-stu-id="41054-159">The *Pages/Movies/Edit.cshtml* file contains the model directive (`@model RazorPagesMovie.Pages.Movies.EditModel`), which makes the movie model available on the page.</span></span>
* <span data-ttu-id="41054-160">O formulário Editar é exibido com os valores do filme.</span><span class="sxs-lookup"><span data-stu-id="41054-160">The Edit form is displayed with the values from the movie.</span></span>

<span data-ttu-id="41054-161">Quando a página Movies/Edit é postada:</span><span class="sxs-lookup"><span data-stu-id="41054-161">When the Movies/Edit page is posted:</span></span>

* <span data-ttu-id="41054-162">Os valores de formulário na página são associados à propriedade `Movie`.</span><span class="sxs-lookup"><span data-stu-id="41054-162">The form values on the page are bound to the `Movie` property.</span></span> <span data-ttu-id="41054-163">O atributo `[BindProperty]` habilita o [Model binding](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="41054-163">The `[BindProperty]` attribute enables [Model binding](xref:mvc/models/model-binding).</span></span>

  ```csharp
  [BindProperty]
  public Movie Movie { get; set; }
  ```

* <span data-ttu-id="41054-164">Se houver erros no estado do modelo (por exemplo, `ReleaseDate` não pode ser convertido em uma data), o formulário será mostrado com os valores enviados.</span><span class="sxs-lookup"><span data-stu-id="41054-164">If there are errors in the model state (for example, `ReleaseDate` cannot be converted to a date), the form is displayed with the submitted values.</span></span>
* <span data-ttu-id="41054-165">Se não houver erros do modelo, o filme será salvo.</span><span class="sxs-lookup"><span data-stu-id="41054-165">If there are no model errors, the movie is saved.</span></span>

<span data-ttu-id="41054-166">Os métodos HTTP GET nas páginas Índice, Criar e Excluir do Razor seguem um padrão semelhante.</span><span class="sxs-lookup"><span data-stu-id="41054-166">The HTTP GET methods in the Index, Create, and Delete Razor pages follow a similar pattern.</span></span> <span data-ttu-id="41054-167">O método `OnPostAsync` HTTP POST na página Criar do Razor segue um padrão semelhante ao método `OnPostAsync` na página Editar do Razor.</span><span class="sxs-lookup"><span data-stu-id="41054-167">The HTTP POST `OnPostAsync` method in the Create Razor Page follows a similar pattern to the `OnPostAsync` method in the Edit Razor Page.</span></span>

<span data-ttu-id="41054-168">A pesquisa é adicionada no próximo tutorial.</span><span class="sxs-lookup"><span data-stu-id="41054-168">Search is added in the next tutorial.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="41054-169">[Anterior: trabalhando com um banco de dados](xref:tutorials/razor-pages/sql)
> [Próximo: adicionar pesquisa](xref:tutorials/razor-pages/search)</span><span class="sxs-lookup"><span data-stu-id="41054-169">[Previous: Working with a database](xref:tutorials/razor-pages/sql)
[Next: Add search](xref:tutorials/razor-pages/search)</span></span>
