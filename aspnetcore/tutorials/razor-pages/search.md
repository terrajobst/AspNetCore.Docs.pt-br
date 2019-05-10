---
title: Adicionar a pesquisa às Páginas Razor do ASP.NET Core
author: rick-anderson
description: Mostra como adicionar uma pesquisa às Páginas Razor do ASP.NET Core
ms.author: riande
ms.date: 12/3/2018
uid: tutorials/razor-pages/search
ms.openlocfilehash: d0ce13e87c3f5e66008f308f6258403ea37b8847
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64884571"
---
# <a name="add-search-to-aspnet-core-razor-pages"></a>Adicionar a pesquisa às Páginas Razor do ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE[](~/includes/rp/download.md)]

Nas seções a seguir, a pesquisa de filmes por *gênero* ou *nome* é adicionada.

Adicione as seguintes propriedades realçadas em *Pages/Movies/Index.cshtml.cs*:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_newProps&highlight=11-999)]

* `SearchString`: contém o texto que os usuários inserem na caixa de texto de pesquisa. `SearchString` está decorada com o atributo [`[BindProperty]`](/dotnet/api/microsoft.aspnetcore.mvc.bindpropertyattribute). `[BindProperty]` associa valores de formulário e cadeias de consulta ao mesmo nome da propriedade. `(SupportsGet = true)` é necessário para a associação em solicitações GET.
* `Genres`: contém a lista de gêneros. `Genres` permite que o usuário selecione um gênero na lista. `SelectList` exige `using Microsoft.AspNetCore.Mvc.Rendering;`
* `MovieGenre`: contém o gênero específico que o usuário seleciona (por exemplo, "Oeste").
* `Genres` e `MovieGenre` são abordados mais adiante neste tutorial.

[!INCLUDE[](~/includes/bind-get.md)]

Atualize o método `OnGetAsync` da página de Índice pelo seguinte código:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_1stSearch)]

A primeira linha do método `OnGetAsync` cria uma consulta [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) para selecionar os filmes:

```csharp
// using System.Linq;
var movies = from m in _context.Movie
             select m;
```

A consulta é *somente* definida neste ponto; ela **não** foi executada no banco de dados.

Se a propriedade `SearchString` não é nula nem vazia, a consulta de filmes é modificada para filtrar a cadeia de pesquisa:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchNull)]

O código `s => s.Title.Contains()` é uma [Expressão Lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Lambdas são usados em consultas [LINQ](/dotnet/csharp/programming-guide/concepts/linq/) baseadas em método como argumentos para métodos de operadores de consulta padrão, como o método [Where](/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq) ou `Contains` (usado no código anterior). As consultas LINQ não são executadas quando são definidas ou quando são modificadas com uma chamada a um método (como `Where`, `Contains` ou `OrderBy`). Em vez disso, a execução da consulta é adiada. Isso significa que a avaliação de uma expressão é atrasada até que seu valor realizado seja iterado ou o método `ToListAsync` seja chamado. Consulte [Execução de consulta](/dotnet/framework/data/adonet/ef/language-reference/query-execution) para obter mais informações.

**Observação:** o método [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) é executado no banco de dados, não no código C#. A diferenciação de maiúsculas e minúsculas na consulta depende do banco de dados e da ordenação. No SQL Server, `Contains` é mapeado para [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), que não diferencia maiúsculas de minúsculas. No SQLite, com a ordenação padrão, ele diferencia maiúsculas de minúsculas.

Navegue para a página Movies e acrescente uma cadeia de consulta, como `?searchString=Ghost`, à URL (por exemplo, `https://localhost:5001/Movies?searchString=Ghost`). Os filmes filtrados são exibidos.

![Exibição de índice](search/_static/ghost.png)

Se o modelo de rota a seguir for adicionado à página de Índice, a cadeia de caracteres de pesquisa poderá ser passada como um segmento de URL (por exemplo, `https://localhost:5001/Movies/Ghost`).

```cshtml
@page "{searchString?}"
```

A restrição da rota anterior permite a pesquisa do título como dados de rota (um segmento de URL), em vez de como um valor de cadeia de caracteres de consulta.  O `?` em `"{searchString?}"` significa que esse é um parâmetro de rota opcional.

![Exibição de índice com a palavra “ghost” adicionada à URL e uma lista de filmes retornados com dois filmes, Ghostbusters e Ghostbusters 2](search/_static/g2.png)

O tempo de execução do ASP.NET Core usa o [model binding](xref:mvc/models/model-binding) para definir o valor da propriedade `SearchString` na cadeia de consulta (`?searchString=Ghost`) ou nos dados de rota (`https://localhost:5001/Movies/Ghost`). O model binding não diferencia maiúsculas de minúsculas.

No entanto, você não pode esperar que os usuários modifiquem a URL para pesquisar um filme. Nesta etapa, a interface do usuário é adicionada para filtrar filmes. Se você adicionou a restrição de rota `"{searchString?}"`, remova-a.

Abra o arquivo *Pages/Movies/Index.cshtml* e, em seguida, adicione a marcação `<form>` realçada no seguinte código:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index2.cshtml?highlight=14-19&range=1-22)]

A marca `<form>` HTML usa os seguintes [Auxiliares de Marcas](xref:mvc/views/tag-helpers/intro):

* [Auxiliar de Marcas de Formulário](xref:mvc/views/working-with-forms#the-form-tag-helper). Quando o formulário é enviado, a cadeia de caracteres de filtro é enviada para a página *Pages/Movies/Index* por meio da cadeia de consulta.
* [Auxiliar de marcação de entrada](xref:mvc/views/working-with-forms#the-input-tag-helper)

Salve as alterações e teste o filtro.

![Exibição de índice com a palavra “ghost” digitada na caixa de texto Filtro de título](search/_static/filter.png)

## <a name="search-by-genre"></a>Pesquisar por gênero

Atualize o método `OnGetAsync` pelo seguinte código:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SearchGenre)]

O código a seguir é uma consulta LINQ que recupera todos os gêneros do banco de dados.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_LINQ)]

O `SelectList` de gêneros é criado com a projeção dos gêneros distintos.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Index.cshtml.cs?name=snippet_SelectList)]

### <a name="add-search-by-genre-to-the-razor-page"></a>Adicionar pesquisa por gênero ao Razor Page

Atualize *Index.cshtml* da seguinte maneira:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexFormGenreNoRating.cshtml?highlight=16-18&range=1-26)]

Teste o aplicativo pesquisando por gênero, título do filme e por ambos.

## <a name="additional-resources"></a>Recursos adicionais

* [Versão do YouTube deste tutorial](https://youtu.be/4B6pHtdyo08)

> [!div class="step-by-step"]
> [Anterior: atualizando as páginas](xref:tutorials/razor-pages/da1)
> [Próximo: adicionando um novo campo](xref:tutorials/razor-pages/new-field)
