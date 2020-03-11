---
title: Adicionar a pesquisa a um aplicativo ASP.NET Core MVC
author: rick-anderson
description: Mostra como adicionar uma pesquisa a um aplicativo ASP.NET Core MVC básico
ms.author: riande
ms.date: 12/13/2018
uid: tutorials/first-mvc-app/search
ms.openlocfilehash: 89f1fa84783430f160ca0b840bf7ae9699520cb7
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78662865"
---
# <a name="add-search-to-an-aspnet-core-mvc-app"></a>Adicionar a pesquisa a um aplicativo ASP.NET Core MVC

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

Nesta seção, você adiciona a funcionalidade de pesquisa ao método de ação `Index` que permite pesquisar filmes por *gênero* ou *nome*.

Atualize o método `Index` encontrado dentro de *Controllers/MoviesController.cs* com o seguinte código:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_1stSearch)]

A primeira linha do método de ação `Index` cria uma consulta [LINQ](/dotnet/standard/using-linq) para selecionar os filmes:

```csharp
var movies = from m in _context.Movie
             select m;
```

A consulta é *somente* definida neste ponto; ela **não** foi executada no banco de dados.

Se o parâmetro `searchString` contiver uma cadeia de caracteres, a consulta de filmes será modificada para filtrar o valor da cadeia de caracteres de pesquisa:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchNull2)]

O código `s => s.Title.Contains()` acima é uma [Expressão Lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Lambdas são usados em consultas [LINQ](/dotnet/standard/using-linq) baseadas em método como argumentos para métodos de operadores de consulta padrão, como o método [Where](/dotnet/api/system.linq.enumerable.where) ou `Contains` (usado no código acima). As consultas LINQ não são executadas quando são definidas ou no momento em que são modificadas com uma chamada a um método, como `Where`, `Contains` ou `OrderBy`. Em vez disso, a execução da consulta é adiada.  Isso significa que a avaliação de uma expressão é atrasada até que seu valor realizado seja, de fato, iterado ou o método `ToListAsync` seja chamado. Para obter mais informações sobre a execução de consulta adiada, consulte [Execução da consulta](/dotnet/framework/data/adonet/ef/language-reference/query-execution).

Observação: o método [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) é executado no banco de dados, não no código C# mostrado acima. A diferenciação de maiúsculas e minúsculas na consulta depende do banco de dados e da ordenação. No SQL Server, [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) é mapeado para [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), que não diferencia maiúsculas de minúsculas. No SQLite, com a ordenação padrão, ele diferencia maiúsculas de minúsculas.

Navegue até `/Movies/Index`. Acrescente uma cadeia de consulta, como `?searchString=Ghost`, à URL. Os filmes filtrados são exibidos.

![Exibição de índice](~/tutorials/first-mvc-app/search/_static/ghost.png)

Se você alterar a assinatura do método `Index` para que ele tenha um parâmetro chamado `id`, o parâmetro `id` corresponderá o espaço reservado `{id}` opcional com as rotas padrão definidas em *Startup.cs*.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]

Altere o parâmetro para `id` e todas as ocorrências de `searchString` altere para `id`.

O método `Index` anterior:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

O método `Index` atualizado com o parâmetro `id`:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_SearchID)]

Agora você pode passar o título de pesquisa como dados de rota (um segmento de URL), em vez de como um valor de cadeia de consulta.

![Exibição de índice com a palavra “ghost” adicionada à URL e uma lista de filmes retornados com dois filmes, Ghostbusters e Ghostbusters 2](~/tutorials/first-mvc-app/search/_static/g2.png)

No entanto, você não pode esperar que os usuários modifiquem a URL sempre que desejarem pesquisar um filme. Agora você adicionará os elementos da interface do usuário para ajudá-los a filtrar filmes. Se você tiver alterado a assinatura do método `Index` para testar como passar o parâmetro `ID` associado à rota, altere-o novamente para que ele use um parâmetro chamado `searchString`:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

Abra o arquivo *Views/Movies/Index.cshtml* e, em seguida, adicione a marcação `<form>` realçada abaixo:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexForm1.cshtml?highlight=10-16&range=4-21)]

A marcação `<form>` HTML usa o [Auxiliar de Marcação de Formulário](xref:mvc/views/working-with-forms). Portanto, quando você enviar o formulário, a cadeia de caracteres de filtro será enviada para a ação `Index` do controlador de filmes. Salve as alterações e, em seguida, teste o filtro.

![Exibição de índice com a palavra “ghost” digitada na caixa de texto Filtro de título](~/tutorials/first-mvc-app/search/_static/filter.png)

Não há nenhuma sobrecarga `[HttpPost]` do método `Index` que poderia ser esperada. Isso não é necessário, porque o método não está alterando o estado do aplicativo, apenas filtrando os dados.

Você poderá adicionar o método `[HttpPost] Index` a seguir.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1&name=snippet_SearchPost)]

O parâmetro `notUsed` é usado para criar uma sobrecarga para o método `Index`. Falaremos sobre isso mais adiante no tutorial.

Se você adicionar esse método, o chamador de ação fará uma correspondência com o método `[HttpPost] Index` e o método `[HttpPost] Index` será executado conforme mostrado na imagem abaixo.

![Janela do navegador com a resposta do aplicativo Do Índice HttpPost: filtrar ghost](~/tutorials/first-mvc-app/search/_static/fo.png)

No entanto, mesmo se você adicionar esta versão `[HttpPost]` do método `Index`, haverá uma limitação na forma de como isso tudo foi implementado. Imagine que você deseja adicionar uma pesquisa específica como Favoritos ou enviar um link para seus amigos para que eles possam clicar para ver a mesma lista filtrada de filmes. Observe que a URL da solicitação HTTP POST é a mesma que a URL da solicitação GET (localhost:{PORT}/Movies/Index) – não há nenhuma informação de pesquisa na URL. As informações de cadeia de caracteres de pesquisa são enviadas ao servidor como um [valor de campo de formulário](https://developer.mozilla.org/docs/Learn/HTML/Forms/Sending_and_retrieving_form_data). Verifique isso com as ferramentas do Desenvolvedor do navegador ou a excelente [ferramenta Fiddler](https://www.telerik.com/fiddler). A imagem abaixo mostra as ferramentas do Desenvolvedor do navegador Chrome:

![Guia Rede das Ferramentas do Desenvolvedor no Microsoft Edge mostrando o corpo de uma solicitação com o valor de searchString “ghost”](~/tutorials/first-mvc-app/search/_static/f12_rb.png)

Veja o parâmetro de pesquisa e o token [XSRF](xref:security/anti-request-forgery) no corpo da solicitação. Observe, conforme mencionado no tutorial anterior, que o [Auxiliar de Marcação de Formulário](xref:mvc/views/working-with-forms) gera um token antifalsificação [XSRF](xref:security/anti-request-forgery). Não modificaremos os dados e, portanto, não precisamos validar o token no método do controlador.

Como o parâmetro de pesquisa está no corpo da solicitação e não na URL, não é possível capturar essas informações de pesquisa para adicionar como Favoritos ou compartilhar com outras pessoas. Corrija isso especificando que a solicitação deve ser `HTTP GET`, encontrada no arquivo *Views/Movies/Index.cshtml*.

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGet.cshtml?highlight=12&range=1-23)]

Agora, quando você enviar uma pesquisa, a URL conterá a cadeia de consulta da pesquisa. A pesquisa também irá para o método de ação `HttpGet Index`, mesmo se você tiver um método `HttpPost Index`.

![Janela do navegador mostrando a searchString=ghost= na URL e os filmes retornados, Ghostbusters e Ghostbusters 2, que contêm a palavra “ghost”](~/tutorials/first-mvc-app/search/_static/search_get.png)

A seguinte marcação mostra a alteração para a marcação `form`:

```cshtml
<form asp-controller="Movies" asp-action="Index" method="get">
```

## <a name="add-search-by-genre"></a>Adicionar pesquisa por gênero

Adicione a seguinte classe `MovieGenreViewModel` à pasta *Models*:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieGenreViewModel.cs)]

O modelo de exibição do gênero de filme conterá:

* Uma lista de filmes.
* Uma `SelectList` que contém a lista de gêneros. Isso permite que o usuário selecione um gênero na lista.
* `MovieGenre`, que contém o gênero selecionado.
* `SearchString`, que contém o texto que os usuários inserem na caixa de texto de pesquisa.

Substitua o método `Index` em `MoviesController.cs` pelo seguinte código:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_SearchGenre)]

O código a seguir é uma consulta `LINQ` que recupera todos os gêneros do banco de dados.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_LINQ)]

A `SelectList` de gêneros é criada com a projeção dos gêneros distintos (não desejamos que nossa lista de seleção tenha gêneros duplicados).

Quando o usuário pesquisa o item, o valor de pesquisa é mantido na caixa de pesquisa.

## <a name="add-search-by-genre-to-the-index-view"></a>Adicionar pesquisa por gênero à exibição Índice

Atualize `Index.cshtml`, encontrado em *Views/Movies/* , da seguinte maneira:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexFormGenreNoRating.cshtml?highlight=1,15,16,17,19,28,31,34,37,43)]

Examine a expressão lambda usada no auxiliar HTML a seguir:

`@Html.DisplayNameFor(model => model.Movies[0].Title)`

No código anterior, o Auxiliar de HTML `DisplayNameFor` inspeciona a propriedade `Title` referenciada na expressão lambda para determinar o nome de exibição. Como a expressão lambda é inspecionada em vez de avaliada, você não recebe uma violação de acesso quando `model`, `model.Movies` ou `model.Movies[0]` é `null` ou vazio. Quando a expressão lambda é avaliada (por exemplo, `@Html.DisplayFor(modelItem => item.Title)`), os valores da propriedade do modelo são avaliados.

Teste o aplicativo pesquisando por gênero, título do filme e por ambos:

![Janela do navegador mostrando resultados de https://localhost:5001/Movies?MovieGenre=Comedy&SearchString=2](~/tutorials/first-mvc-app/search/_static/s2.png)

> [!div class="step-by-step"]
> [Anterior](controller-methods-views.md)
> [Próximo](new-field.md)
