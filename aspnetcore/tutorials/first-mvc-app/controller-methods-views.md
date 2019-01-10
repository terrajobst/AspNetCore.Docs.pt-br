---
title: Os métodos e as exibições do controlador no ASP.NET Core
author: rick-anderson
description: Aprenda a trabalhar com os métodos, as exibições e as DataAnnotations do controlador no ASP.NET Core.
ms.author: riande
ms.date: 12/13/2018
uid: tutorials/first-mvc-app/controller-methods-views
ms.openlocfilehash: e6bdaec6dfe681582a54bd39ce1c76bebe6f5fb2
ms.sourcegitcommit: e1cc4c1ef6c9e07918a609d5ad7fadcb6abe3e12
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/03/2019
ms.locfileid: "53997247"
---
# <a name="controller-methods-and-views-in-aspnet-core"></a><span data-ttu-id="7ed75-103">Os métodos e as exibições do controlador no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7ed75-103">Controller methods and views in ASP.NET Core</span></span>

<span data-ttu-id="7ed75-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7ed75-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="7ed75-105">Temos um bom começo para o aplicativo de filme, mas a apresentação não é ideal.</span><span class="sxs-lookup"><span data-stu-id="7ed75-105">We have a good start to the movie app, but the presentation isn't ideal.</span></span> <span data-ttu-id="7ed75-106">Você não deseja ver a hora (12:00:00 AM na imagem abaixo) e **ReleaseDate** deve ser escrito em duas palavras.</span><span class="sxs-lookup"><span data-stu-id="7ed75-106">We don't want to see the time (12:00:00 AM in the image below) and **ReleaseDate** should be two words.</span></span>

![Exibição de índice: a Data de Lançamento é uma palavra (sem espaço) e cada data de lançamento do filme mostra o horário 0h](working-with-sql/_static/m55.png)

<span data-ttu-id="7ed75-108">Abra o arquivo *Models/Movie.cs* e adicione as linhas realçadas mostradas abaixo:</span><span class="sxs-lookup"><span data-stu-id="7ed75-108">Open the *Models/Movie.cs* file and add the highlighted lines shown below:</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=2,3,12-13,17)]

<span data-ttu-id="7ed75-109">Abordaremos [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) no próximo tutorial.</span><span class="sxs-lookup"><span data-stu-id="7ed75-109">We cover [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) in the next tutorial.</span></span> <span data-ttu-id="7ed75-110">O atributo [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) especifica o que deve ser exibido no nome de um campo (neste caso, “Release Date” em vez de “ReleaseDate”).</span><span class="sxs-lookup"><span data-stu-id="7ed75-110">The [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) attribute specifies what to display for the name of a field (in this case "Release Date" instead of "ReleaseDate").</span></span> <span data-ttu-id="7ed75-111">O atributo [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) especifica o tipo de dados (Data) e, portanto, as informações de hora armazenadas no campo não são exibidas.</span><span class="sxs-lookup"><span data-stu-id="7ed75-111">The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (Date), so the time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="7ed75-112">A anotação de dados `[Column(TypeName = "decimal(18, 2)")]` é necessária para que o Entity Framework Core possa mapear corretamente o `Price` para a moeda no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="7ed75-112">The `[Column(TypeName = "decimal(18, 2)")]` data annotation is required so Entity Framework Core can correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="7ed75-113">Para obter mais informações, veja [Tipos de Dados](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="7ed75-113">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="7ed75-114">Procure o controlador `Movies` e mantenha o ponteiro do mouse pressionado sobre um link **Editar** para ver a URL de destino.</span><span class="sxs-lookup"><span data-stu-id="7ed75-114">Browse to the `Movies` controller and hold the mouse pointer over an **Edit** link to see the target URL.</span></span>

![São mostrados uma janela do navegador com o mouse sobre o link de edição e um link da URL https://localhost:5001/Movies/Edit/5](~/tutorials/first-mvc-app/controller-methods-views/_static/edit7.png)

<span data-ttu-id="7ed75-116">Os links **Editar**, **Detalhes** e **Excluir** são gerados pelo Auxiliar de Marcação de Âncora do MVC Core no arquivo *Views/Movies/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="7ed75-116">The **Edit**, **Details**, and **Delete** links are generated by the Core MVC Anchor Tag Helper in the *Views/Movies/Index.cshtml* file.</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexOriginal.cshtml?highlight=1-3&range=46-50)]

<span data-ttu-id="7ed75-117">Os [Auxiliares de Marcação](xref:mvc/views/tag-helpers/intro) permitem que o código do servidor participe da criação e renderização de elementos HTML em arquivos do Razor.</span><span class="sxs-lookup"><span data-stu-id="7ed75-117">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="7ed75-118">No código acima, o `AnchorTagHelper` gera dinamicamente o valor do atributo `href` HTML com base na ID de rota e no método de ação do controlador. Use a opção **Exibir Código-fonte** em seu navegador favorito ou as ferramentas do desenvolvedor para examinar a marcação gerada.</span><span class="sxs-lookup"><span data-stu-id="7ed75-118">In the code above, the `AnchorTagHelper` dynamically generates the HTML `href` attribute value from the controller action method and route id. You use **View Source** from your favorite browser or use the developer tools to examine the generated markup.</span></span> <span data-ttu-id="7ed75-119">Uma parte do HTML gerado é mostrada abaixo:</span><span class="sxs-lookup"><span data-stu-id="7ed75-119">A portion of the generated HTML is shown below:</span></span>

```html
 <td>
    <a href="/Movies/Edit/4"> Edit </a> |
    <a href="/Movies/Details/4"> Details </a> |
    <a href="/Movies/Delete/4"> Delete </a>
</td>
```

<span data-ttu-id="7ed75-120">Lembre-se do formato do [roteamento](xref:mvc/controllers/routing) definido no arquivo *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="7ed75-120">Recall the format for [routing](xref:mvc/controllers/routing) set in the *Startup.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

<span data-ttu-id="7ed75-121">O ASP.NET Core converte `https://localhost:5001/Movies/Edit/4` de uma solicitação no método de ação `Edit` do controlador `Movies` com o parâmetro `Id` igual a 4.</span><span class="sxs-lookup"><span data-stu-id="7ed75-121">ASP.NET Core translates `https://localhost:5001/Movies/Edit/4` into a request to the `Edit` action method of the `Movies` controller with the parameter `Id` of 4.</span></span> <span data-ttu-id="7ed75-122">(Os métodos do controlador também são conhecidos como métodos de ação.)</span><span class="sxs-lookup"><span data-stu-id="7ed75-122">(Controller methods are also known as action methods.)</span></span>

<span data-ttu-id="7ed75-123">Os [Auxiliares de Marcação](xref:mvc/views/tag-helpers/intro) são um dos novos recursos mais populares do ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7ed75-123">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are one of the most popular new features in ASP.NET Core.</span></span> <span data-ttu-id="7ed75-124">Para obter mais informações, consulte [Recursos adicionais](#additional-resources).</span><span class="sxs-lookup"><span data-stu-id="7ed75-124">For more information, see [Additional resources](#additional-resources).</span></span>

<span data-ttu-id="7ed75-125">Abra o controlador `Movies` e examine os dois métodos de ação `Edit`.</span><span class="sxs-lookup"><span data-stu-id="7ed75-125">Open the `Movies` controller and examine the two `Edit` action methods.</span></span> <span data-ttu-id="7ed75-126">O código a seguir mostra o método `HTTP GET Edit`, que busca o filme e popula o formato de edição gerado pelo arquivo *Edit.cshtml* do Razor.</span><span class="sxs-lookup"><span data-stu-id="7ed75-126">The following code shows the `HTTP GET Edit` method, which fetches the movie and populates the edit form generated by the *Edit.cshtml* Razor file.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit1)]

<span data-ttu-id="7ed75-127">O código a seguir mostra o método `HTTP POST Edit`, que processa os valores de filmes postados:</span><span class="sxs-lookup"><span data-stu-id="7ed75-127">The following code shows the `HTTP POST Edit` method, which processes the posted movie values:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit1)]

<span data-ttu-id="7ed75-128">O código a seguir mostra o método `HTTP POST Edit`, que processa os valores de filmes postados:</span><span class="sxs-lookup"><span data-stu-id="7ed75-128">The following code shows the `HTTP POST Edit` method, which processes the posted movie values:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

<span data-ttu-id="7ed75-129">O atributo `[Bind]` é uma maneira de proteger contra o [excesso de postagem](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost).</span><span class="sxs-lookup"><span data-stu-id="7ed75-129">The `[Bind]` attribute is one way to protect against [over-posting](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost).</span></span> <span data-ttu-id="7ed75-130">Você somente deve incluir as propriedades do atributo `[Bind]` que deseja alterar.</span><span class="sxs-lookup"><span data-stu-id="7ed75-130">You should only include properties in the `[Bind]` attribute that you want to change.</span></span> <span data-ttu-id="7ed75-131">Para obter mais informações, consulte [Proteger o controlador contra o excesso de postagem](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application).</span><span class="sxs-lookup"><span data-stu-id="7ed75-131">For more information, see [Protect your controller from over-posting](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application).</span></span> <span data-ttu-id="7ed75-132">[ViewModels](http://rachelappel.com/use-viewmodels-to-manage-data-amp-organize-code-in-asp-net-mvc-applications/) fornece uma abordagem alternativa para prevenir o excesso de postagem.</span><span class="sxs-lookup"><span data-stu-id="7ed75-132">[ViewModels](http://rachelappel.com/use-viewmodels-to-manage-data-amp-organize-code-in-asp-net-mvc-applications/) provide an alternative approach to prevent over-posting.</span></span>

<span data-ttu-id="7ed75-133">Observe se o segundo método de ação `Edit` é precedido pelo atributo `[HttpPost]`.</span><span class="sxs-lookup"><span data-stu-id="7ed75-133">Notice the second `Edit` action method is preceded by the `[HttpPost]` attribute.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit2&highlight=1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2&highlight=4)]

::: moniker-end

<span data-ttu-id="7ed75-134">O atributo `HttpPost` especifica que esse método `Edit` pode ser invocado *somente* para solicitações `POST`.</span><span class="sxs-lookup"><span data-stu-id="7ed75-134">The `HttpPost` attribute specifies that this `Edit` method can be invoked *only* for `POST` requests.</span></span> <span data-ttu-id="7ed75-135">Você pode aplicar o atributo `[HttpGet]` ao primeiro método de edição, mas isso não é necessário porque `[HttpGet]` é o padrão.</span><span class="sxs-lookup"><span data-stu-id="7ed75-135">You could apply the `[HttpGet]` attribute to the first edit method, but that's not necessary because `[HttpGet]` is the default.</span></span>

<span data-ttu-id="7ed75-136">O atributo `ValidateAntiForgeryToken` é usado para [prevenir a falsificação de uma solicitação](xref:security/anti-request-forgery) e é associado a um token antifalsificação gerado no arquivo de exibição de edição (*Views/Movies/Edit.cshtml*).</span><span class="sxs-lookup"><span data-stu-id="7ed75-136">The `ValidateAntiForgeryToken` attribute is used to [prevent forgery of a request](xref:security/anti-request-forgery) and is paired up with an anti-forgery token generated in the edit view file (*Views/Movies/Edit.cshtml*).</span></span> <span data-ttu-id="7ed75-137">O arquivo de exibição de edição gera o token antifalsificação com o [Auxiliar de Marcação de Formulário](xref:mvc/views/working-with-forms).</span><span class="sxs-lookup"><span data-stu-id="7ed75-137">The edit view file generates the anti-forgery token with the [Form Tag Helper](xref:mvc/views/working-with-forms).</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/Edit.cshtml?range=9)]

<span data-ttu-id="7ed75-138">O [Auxiliar de Marcação de Formulário](xref:mvc/views/working-with-forms) gera um token antifalsificação oculto que deve corresponder ao token antifalsificação gerado `[ValidateAntiForgeryToken]` no método `Edit` do controlador Movies.</span><span class="sxs-lookup"><span data-stu-id="7ed75-138">The [Form Tag Helper](xref:mvc/views/working-with-forms) generates a hidden anti-forgery token that must match the `[ValidateAntiForgeryToken]` generated anti-forgery token in the `Edit` method of the Movies controller.</span></span> <span data-ttu-id="7ed75-139">Para obter mais informações, consulte [Falsificação de antissolicitação](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="7ed75-139">For more information, see [Anti-Request Forgery](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="7ed75-140">O método `HttpGet Edit` usa o parâmetro `ID` de filme, pesquisa o filme usando o método `SingleOrDefaultAsync` do Entity Framework e retorna o filme selecionado para a exibição de Edição.</span><span class="sxs-lookup"><span data-stu-id="7ed75-140">The `HttpGet Edit` method takes the movie `ID` parameter, looks up the movie using the Entity Framework `SingleOrDefaultAsync` method, and returns the selected movie to the Edit view.</span></span> <span data-ttu-id="7ed75-141">Se um filme não for encontrado, `NotFound` (HTTP 404) será retornado.</span><span class="sxs-lookup"><span data-stu-id="7ed75-141">If a movie cannot be found, `NotFound` (HTTP 404) is returned.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit1)]

<span data-ttu-id="7ed75-142">Quando o sistema de scaffolding criou a exibição de Edição, ele examinou a classe `Movie` e o código criado para renderizar os elementos `<label>` e `<input>` de cada propriedade da classe.</span><span class="sxs-lookup"><span data-stu-id="7ed75-142">When the scaffolding system created the Edit view, it examined the `Movie` class and created code to render `<label>` and `<input>` elements for each property of the class.</span></span> <span data-ttu-id="7ed75-143">O seguinte exemplo mostra a exibição de Edição que foi gerada pelo sistema de scaffolding do Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="7ed75-143">The following example shows the Edit view that was generated by the Visual Studio scaffolding system:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/EditOriginal.cshtml)]

<span data-ttu-id="7ed75-144">Observe como o modelo de exibição tem uma instrução `@model MvcMovie.Models.Movie` na parte superior do arquivo.</span><span class="sxs-lookup"><span data-stu-id="7ed75-144">Notice how the view template has a `@model MvcMovie.Models.Movie` statement at the top of the file.</span></span> <span data-ttu-id="7ed75-145">`@model MvcMovie.Models.Movie` especifica que a exibição espera que o modelo de exibição seja do tipo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="7ed75-145">`@model MvcMovie.Models.Movie` specifies that the view expects the model for the view template to be of type `Movie`.</span></span>

<span data-ttu-id="7ed75-146">O código com scaffolding usa vários métodos de Auxiliares de Marcação para simplificar a marcação HTML.</span><span class="sxs-lookup"><span data-stu-id="7ed75-146">The scaffolded code uses several Tag Helper methods to streamline the HTML markup.</span></span> <span data-ttu-id="7ed75-147">O – [Auxiliar de Marcação de Rótulo](xref:mvc/views/working-with-forms) exibe o nome do campo (“Title”, “ReleaseDate”, “Genre” ou “Price”).</span><span class="sxs-lookup"><span data-stu-id="7ed75-147">The - [Label Tag Helper](xref:mvc/views/working-with-forms) displays the name of the field ("Title", "ReleaseDate", "Genre", or "Price").</span></span> <span data-ttu-id="7ed75-148">O [Auxiliar de Marcação de Entrada](xref:mvc/views/working-with-forms) renderiza um elemento `<input>` HTML.</span><span class="sxs-lookup"><span data-stu-id="7ed75-148">The [Input Tag Helper](xref:mvc/views/working-with-forms) renders an HTML `<input>` element.</span></span> <span data-ttu-id="7ed75-149">O [Auxiliar de Marcação de Validação](xref:mvc/views/working-with-forms) exibe todas as mensagens de validação associadas a essa propriedade.</span><span class="sxs-lookup"><span data-stu-id="7ed75-149">The [Validation Tag Helper](xref:mvc/views/working-with-forms) displays any validation messages associated with that property.</span></span>

<span data-ttu-id="7ed75-150">Execute o aplicativo e navegue para a URL `/Movies`.</span><span class="sxs-lookup"><span data-stu-id="7ed75-150">Run the application and navigate to the `/Movies` URL.</span></span> <span data-ttu-id="7ed75-151">Clique em um link **Editar**.</span><span class="sxs-lookup"><span data-stu-id="7ed75-151">Click an **Edit** link.</span></span> <span data-ttu-id="7ed75-152">No navegador, exiba a origem da página.</span><span class="sxs-lookup"><span data-stu-id="7ed75-152">In the browser, view the source for the page.</span></span> <span data-ttu-id="7ed75-153">O HTML gerado para o elemento `<form>` é mostrado abaixo.</span><span class="sxs-lookup"><span data-stu-id="7ed75-153">The generated HTML for the `<form>` element is shown below.</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Shared/edit_view_source.html?highlight=1,6,10,17,24,28)]

<span data-ttu-id="7ed75-154">Os elementos `<input>` estão em um elemento `HTML <form>` cujo atributo `action` está definido para ser postado para a URL `/Movies/Edit/id`.</span><span class="sxs-lookup"><span data-stu-id="7ed75-154">The `<input>` elements are in an `HTML <form>` element whose `action` attribute is set to post to the `/Movies/Edit/id` URL.</span></span> <span data-ttu-id="7ed75-155">Os dados de formulário serão postados com o servidor quando o botão `Save` receber um clique.</span><span class="sxs-lookup"><span data-stu-id="7ed75-155">The form data will be posted to the server when the `Save` button is clicked.</span></span> <span data-ttu-id="7ed75-156">A última linha antes do elemento `</form>` de fechamento mostra o token [XSRF](xref:security/anti-request-forgery) oculto gerado pelo [Auxiliar de Marcação de Formulário](xref:mvc/views/working-with-forms).</span><span class="sxs-lookup"><span data-stu-id="7ed75-156">The last line before the closing `</form>` element shows the hidden [XSRF](xref:security/anti-request-forgery) token generated by the [Form Tag Helper](xref:mvc/views/working-with-forms).</span></span>

## <a name="processing-the-post-request"></a><span data-ttu-id="7ed75-157">Processando a solicitação POST</span><span class="sxs-lookup"><span data-stu-id="7ed75-157">Processing the POST Request</span></span>

<span data-ttu-id="7ed75-158">A lista a seguir mostra a versão `[HttpPost]` do método de ação `Edit`.</span><span class="sxs-lookup"><span data-stu-id="7ed75-158">The following listing shows the `[HttpPost]` version of the `Edit` action method.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

<span data-ttu-id="7ed75-159">O atributo `[ValidateAntiForgeryToken]` valida o token [XSRF](xref:security/anti-request-forgery) oculto gerado pelo gerador de tokens antifalsificação no [Auxiliar de Marcação de Formulário](xref:mvc/views/working-with-forms)</span><span class="sxs-lookup"><span data-stu-id="7ed75-159">The `[ValidateAntiForgeryToken]` attribute validates the hidden [XSRF](xref:security/anti-request-forgery) token generated by the anti-forgery token generator in the [Form Tag Helper](xref:mvc/views/working-with-forms)</span></span>

<span data-ttu-id="7ed75-160">O sistema de [model binding](xref:mvc/models/model-binding) usa os valores de formulário postados e cria um objeto `Movie` que é passado como o parâmetro `movie`.</span><span class="sxs-lookup"><span data-stu-id="7ed75-160">The [model binding](xref:mvc/models/model-binding) system takes the posted form values and creates a `Movie` object that's passed as the `movie` parameter.</span></span> <span data-ttu-id="7ed75-161">O método `ModelState.IsValid` verifica se os dados enviados no formulário podem ser usados para modificar (editar ou atualizar) um objeto `Movie`.</span><span class="sxs-lookup"><span data-stu-id="7ed75-161">The `ModelState.IsValid` method verifies that the data submitted in the form can be used to modify (edit or update) a `Movie` object.</span></span> <span data-ttu-id="7ed75-162">Se os dados forem válidos, eles serão salvos.</span><span class="sxs-lookup"><span data-stu-id="7ed75-162">If the data is valid, it's saved.</span></span> <span data-ttu-id="7ed75-163">Os dados de filmes atualizados (editados) são salvos no banco de dados chamando o método `SaveChangesAsync` do contexto de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="7ed75-163">The updated (edited) movie data is saved to the database by calling the `SaveChangesAsync` method of database context.</span></span> <span data-ttu-id="7ed75-164">Depois de salvar os dados, o código redireciona o usuário para o método de ação `Index` da classe `MoviesController`, que exibe a coleção de filmes, incluindo as alterações feitas recentemente.</span><span class="sxs-lookup"><span data-stu-id="7ed75-164">After saving the data, the code redirects the user to the `Index` action method of the `MoviesController` class, which displays the movie collection, including the changes just made.</span></span>

<span data-ttu-id="7ed75-165">Antes que o formulário seja postado no servidor, a validação do lado do cliente verifica as regras de validação nos campos.</span><span class="sxs-lookup"><span data-stu-id="7ed75-165">Before the form is posted to the server, client-side validation checks any validation rules on the fields.</span></span> <span data-ttu-id="7ed75-166">Se houver erros de validação, será exibida uma mensagem de erro e o formulário não será postado.</span><span class="sxs-lookup"><span data-stu-id="7ed75-166">If there are any validation errors, an error message is displayed and the form isn't posted.</span></span> <span data-ttu-id="7ed75-167">Se o JavaScript estiver desabilitado, você não terá a validação do lado do cliente, mas o servidor detectará os valores postados que não forem válidos e os valores de formulário serão exibidos novamente com mensagens de erro.</span><span class="sxs-lookup"><span data-stu-id="7ed75-167">If JavaScript is disabled, you won't have client-side validation but the server will detect the posted values that are not valid, and the form values will be redisplayed with error messages.</span></span> <span data-ttu-id="7ed75-168">Mais adiante no tutorial, examinamos a [Validação de Modelos](xref:mvc/models/validation) mais detalhadamente.</span><span class="sxs-lookup"><span data-stu-id="7ed75-168">Later in the tutorial we examine [Model Validation](xref:mvc/models/validation) in more detail.</span></span> <span data-ttu-id="7ed75-169">O [Auxiliar de Marcação de Validação](xref:mvc/views/working-with-forms) no modelo de exibição *Views/Movies/Edit.cshtml* é responsável por exibir as mensagens de erro apropriadas.</span><span class="sxs-lookup"><span data-stu-id="7ed75-169">The [Validation Tag Helper](xref:mvc/views/working-with-forms) in the *Views/Movies/Edit.cshtml* view template takes care of displaying appropriate error messages.</span></span>

![Exibição de edição: uma exceção para o valor de Preço incorreto abc informa que o campo Preço deve ser um número.](~/tutorials/first-mvc-app/controller-methods-views/_static/val.png)

<span data-ttu-id="7ed75-172">Todos os métodos `HttpGet` no controlador de filme seguem um padrão semelhante.</span><span class="sxs-lookup"><span data-stu-id="7ed75-172">All the `HttpGet` methods in the movie controller follow a similar pattern.</span></span> <span data-ttu-id="7ed75-173">Eles obtêm um objeto de filme (ou uma lista de objetos, no caso de `Index`) e passam o objeto (modelo) para a exibição.</span><span class="sxs-lookup"><span data-stu-id="7ed75-173">They get a movie object (or list of objects, in the case of `Index`), and pass the object (model) to the view.</span></span> <span data-ttu-id="7ed75-174">O método `Create` passa um objeto de filme vazio para a exibição `Create`.</span><span class="sxs-lookup"><span data-stu-id="7ed75-174">The `Create` method passes an empty movie object to the `Create` view.</span></span> <span data-ttu-id="7ed75-175">Todos os métodos que criam, editam, excluem ou, de outro modo, modificam dados fazem isso na sobrecarga `[HttpPost]` do método.</span><span class="sxs-lookup"><span data-stu-id="7ed75-175">All the methods that create, edit, delete, or otherwise modify data do so in the `[HttpPost]` overload of the method.</span></span> <span data-ttu-id="7ed75-176">A modificação de dados em um método `HTTP GET` é um risco de segurança.</span><span class="sxs-lookup"><span data-stu-id="7ed75-176">Modifying data in an `HTTP GET` method is a security risk.</span></span> <span data-ttu-id="7ed75-177">A modificação de dados em um método `HTTP GET` também viola as melhores práticas de HTTP e o padrão [REST](http://rest.elkstein.org/) de arquitetura, que especifica que as solicitações GET não devem alterar o estado do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="7ed75-177">Modifying data in an `HTTP GET` method also violates HTTP best practices and the architectural [REST](http://rest.elkstein.org/) pattern, which specifies that GET requests shouldn't change the state of your application.</span></span> <span data-ttu-id="7ed75-178">Em outras palavras, a execução de uma operação GET deve ser uma operação segura que não tem efeitos colaterais e não modifica os dados persistentes.</span><span class="sxs-lookup"><span data-stu-id="7ed75-178">In other words, performing a GET operation should be a safe operation that has no side effects and doesn't modify your persisted data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7ed75-179">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="7ed75-179">Additional resources</span></span>

* [<span data-ttu-id="7ed75-180">Globalização e localização</span><span class="sxs-lookup"><span data-stu-id="7ed75-180">Globalization and localization</span></span>](xref:fundamentals/localization)
* [<span data-ttu-id="7ed75-181">Introdução aos auxiliares de marcação</span><span class="sxs-lookup"><span data-stu-id="7ed75-181">Introduction to Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="7ed75-182">Auxiliares de marca de autor</span><span class="sxs-lookup"><span data-stu-id="7ed75-182">Author Tag Helpers</span></span>](xref:mvc/views/tag-helpers/authoring)
* [<span data-ttu-id="7ed75-183">Falsificação anti-solicitação</span><span class="sxs-lookup"><span data-stu-id="7ed75-183">Anti-Request Forgery</span></span>](xref:security/anti-request-forgery)
* <span data-ttu-id="7ed75-184">Proteger o controlador contra o [excesso de postagem](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application)</span><span class="sxs-lookup"><span data-stu-id="7ed75-184">Protect your controller from [over-posting](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application)</span></span>
* [<span data-ttu-id="7ed75-185">ViewModels</span><span class="sxs-lookup"><span data-stu-id="7ed75-185">ViewModels</span></span>](http://rachelappel.com/use-viewmodels-to-manage-data-amp-organize-code-in-asp-net-mvc-applications/)
* [<span data-ttu-id="7ed75-186">Auxiliar de marcação de formulário</span><span class="sxs-lookup"><span data-stu-id="7ed75-186">Form Tag Helper</span></span>](xref:mvc/views/working-with-forms)
* [<span data-ttu-id="7ed75-187">Auxiliar de marcação de entrada</span><span class="sxs-lookup"><span data-stu-id="7ed75-187">Input Tag Helper</span></span>](xref:mvc/views/working-with-forms)
* [<span data-ttu-id="7ed75-188">Auxiliar de marcação de rótulo</span><span class="sxs-lookup"><span data-stu-id="7ed75-188">Label Tag Helper</span></span>](xref:mvc/views/working-with-forms)
* [<span data-ttu-id="7ed75-189">Selecionar o auxiliar de marcação</span><span class="sxs-lookup"><span data-stu-id="7ed75-189">Select Tag Helper</span></span>](xref:mvc/views/working-with-forms)
* [<span data-ttu-id="7ed75-190">Auxiliar de marcação de validação</span><span class="sxs-lookup"><span data-stu-id="7ed75-190">Validation Tag Helper</span></span>](xref:mvc/views/working-with-forms)

> [!div class="step-by-step"]
> <span data-ttu-id="7ed75-191">[Anterior](working-with-sql.md)
> [Próximo](search.md)</span><span class="sxs-lookup"><span data-stu-id="7ed75-191">[Previous](working-with-sql.md)
[Next](search.md)</span></span>  
