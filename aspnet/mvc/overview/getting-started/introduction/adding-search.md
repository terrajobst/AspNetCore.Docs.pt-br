---
uid: mvc/overview/getting-started/introduction/adding-search
title: Pesquisa | Microsoft Docs
author: Rick-Anderson
description: ''
ms.author: riande
ms.date: 01/17/2019
ms.assetid: df001954-18bf-4550-b03d-43911a0ea186
msc.legacyurl: /mvc/overview/getting-started/introduction/adding-search
msc.type: authoredcontent
ms.openlocfilehash: ada125c917656f3a83524ff39e53b4cfc041a497
ms.sourcegitcommit: 6ba5fb1fd0b7f9a6a79085b0ef56206e462094b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56248375"
---
<a name="search"></a>Pesquisar
====================

[!INCLUDE [Tutorial Note](sample/code-location.md)]

## <a name="adding-a-search-method-and-search-view"></a>Adicionando um método de pesquisa e a exibição de pesquisa

Nesta seção, você adicionará a funcionalidade de pesquisa para o `Index` método de ação que lhe permite pesquisar filmes por gênero ou nome.

## <a name="prerequisites"></a>Pré-requisitos

Para corresponder as capturas de tela desta seção, você precisa executar o aplicativo (F5) e adicione os seguintes filmes no banco de dados.

| Título | Data de lançamento | Gênero | Preço |
| ----- | ------------ | ----- | ----- |
| Ghostbusters | 6/8/1984 | Comédia | 6.99 |
| Ghostbusters II | 6/16/1989 | Comédia | 6.99 |
| Mundial da falta de sofisticação | 3/27/1986 | Ação | 5.99 |


## <a name="updating-the-index-form"></a>Atualizando o formulário de índice

Comece Atualizando as `Index` método de ação existente `MoviesController` classe. Aqui está o código:

[!code-csharp[Main](adding-search/samples/sample1.cs?highlight=1,6-9)]

A primeira linha do `Index` método cria o seguinte [LINQ](https://msdn.microsoft.com/library/bb397926.aspx) consulta para selecionar os filmes:

[!code-csharp[Main](adding-search/samples/sample2.cs)]

A consulta é definida no momento, mas não ainda está sendo executada no banco de dados.

Se o `searchString` parâmetro contém uma cadeia de caracteres, a consulta de filmes será modificada para filtrar o valor da cadeia de pesquisa, usando o seguinte código:

[!code-csharp[Main](adding-search/samples/sample3.cs)]

O código `s => s.Title` acima é uma [Expressão Lambda](https://msdn.microsoft.com/library/bb397687.aspx). Lambdas são usados em baseada em método [LINQ](https://msdn.microsoft.com/library/bb397926.aspx) de consultas como argumentos para métodos de operador de consulta padrão como o [onde](https://msdn.microsoft.com/library/system.linq.enumerable.where.aspx) método usado no código acima. Consultas LINQ não são executadas quando são definidas ou quando eles forem modificados chamando um método como `Where` ou `OrderBy`. Em vez disso, a execução da consulta é adiada, o que significa que a avaliação de uma expressão é atrasada até que seu valor realizado seja iterado, na verdade, ou o [ `ToList` ](https://msdn.microsoft.com/library/bb342261.aspx) método é chamado. No `Search` amostra, em que a consulta é executada a *index. cshtml* exibição. Para obter mais informações sobre a execução de consulta adiada, consulte [Execução da consulta](https://msdn.microsoft.com/library/bb738633.aspx).

> [!NOTE]
> O [Contains](https://msdn.microsoft.com/library/bb155125.aspx) método é executado no banco de dados, não o código c# acima. Banco de dados, [Contains](https://msdn.microsoft.com/library/bb155125.aspx) mapeia para [SQL LIKE](https://msdn.microsoft.com/library/ms179859.aspx), que diferencia maiusculas de minúsculas.

Agora você pode atualizar o `Index` modo de exibição que exibirá o formulário ao usuário.

Execute o aplicativo e navegue até */Movies/Index*. Acrescente uma cadeia de consulta, como `?searchString=ghost`, à URL. Os filmes filtrados são exibidos.

![SearchQryStr](adding-search/_static/image1.png)

Se você alterar a assinatura do `Index` método tenha um parâmetro denominado `id`, o `id` parâmetro corresponderá a `{id}` roteia o espaço reservado para o padrão definido no *aplicativo\_Start RouteConfig.cs* arquivo.

[!code-json[Main](adding-search/samples/sample4.json)]

Original `Index` método tem esta aparência:

[!code-csharp[Main](adding-search/samples/sample5.cs)]

Modificado `Index` método seria semelhante ao seguinte:

[!code-csharp[Main](adding-search/samples/sample6.cs?highlight=1,3)]

Agora você pode passar o título de pesquisa como dados de rota (um segmento de URL), em vez de como um valor de cadeia de consulta.

![](adding-search/_static/image2.png)

No entanto, você não pode esperar que os usuários modifiquem a URL sempre que desejarem pesquisar um filme. Portanto, agora você adicionará uma interface do usuário para ajudá-los a filtrar os filmes. Se você tiver alterado a assinatura do `Index` método para testar como passar o parâmetro de ID associado à rota, alterá-la para que sua `Index` método utiliza um parâmetro de cadeia de caracteres denominado `searchString`:

[!code-csharp[Main](adding-search/samples/sample7.cs)]

Abra o *Views\Movies\Index.cshtml* do arquivo e apenas após `@Html.ActionLink("Create New", "Create")`, adicione a marcação de formulário realçada abaixo:

[!code-cshtml[Main](adding-search/samples/sample8.cshtml?highlight=12-15)]

O `Html.BeginForm` auxiliar cria uma abertura `<form>` marca. O `Html.BeginForm` auxiliar faz com que o formulário postar a mesmo quando o usuário envia o formulário clicando o **filtro** botão.

Visual Studio 2013 tem uma boa melhoria ao exibir e editar arquivos de exibição. Quando você executar o aplicativo com um arquivo de exibição aberta, o Visual Studio 2013 invoca o método de ação do controlador correto para mostrar a exibição.

![](adding-search/_static/image3.png)

Com o modo de exibição de índice aberto no Visual Studio (conforme mostrado na imagem acima), toque Ctr F5 ou F5 para executar o aplicativo e, em seguida, tente pesquisar um filme.

![](adding-search/_static/image4.png)

Não há nenhuma `HttpPost` sobrecarga da `Index` método. Você não precisa dele, porque o método não está alterando o estado do aplicativo, apenas filtrando os dados.

Você poderá adicionar o método `HttpPost Index` a seguir. Nesse caso, o chamador de ação corresponderia a `HttpPost Index` método e o `HttpPost Index` método será executado conforme mostrado na imagem abaixo.

[!code-csharp[Main](adding-search/samples/sample9.cs)]

![SearchPostGhost](adding-search/_static/image5.png)

No entanto, mesmo se você adicionar esta versão `HttpPost` do método `Index`, haverá uma limitação na forma de como isso tudo foi implementado. Imagine que você deseja adicionar uma pesquisa específica como Favoritos ou enviar um link para seus amigos para que eles possam clicar para ver a mesma lista filtrada de filmes. Observe que a URL para a solicitação HTTP POST é o mesmo que a URL para a solicitação GET (localhost: XXXXX/Movies/Index) – não há nenhuma informação de pesquisa na URL em si. Direita agora, as informações de cadeia de caracteres de pesquisa são enviadas ao servidor como um valor de campo do formulário. Isso significa que não é possível capturar essas informações de pesquisa para marcar ou enviar a amigos em uma URL.

A solução é usar uma sobrecarga `BeginForm` que especifica que a solicitação POST deve adicionar as informações de pesquisa para a URL e que deve ser roteado para o `HttpGet` versão do `Index` método. Substituir existente sem parâmetros `BeginForm` método com a seguinte marcação:

[!code-cshtml[Main](adding-search/samples/sample10.cshtml)]

![BeginFormPost_SM](adding-search/_static/image6.png)

Agora, quando você enviar uma pesquisa, a URL contém uma cadeia de caracteres de consulta de pesquisa. A pesquisa também irá para o método de ação `HttpGet Index`, mesmo se você tiver um método `HttpPost Index`.

![IndexWithGetURL](adding-search/_static/image7.png)

## <a name="adding-search-by-genre"></a>Adicionando uma pesquisa por gênero

Se você tiver adicionado a `HttpPost` versão do `Index` método, excluí-lo agora.

Em seguida, você adicionará um recurso para permitir que os usuários a pesquisar filmes por gênero. Substitua o método `Index` pelo seguinte código:

[!code-csharp[Main](adding-search/samples/sample11.cs)]

Esta versão dos `Index` método utiliza um parâmetro adicional, ou seja, `movieGenre`. As primeiras linhas de código é criar um `List` objeto para manter os gêneros de filme do banco de dados.

O código a seguir é uma consulta LINQ que recupera todos os gêneros do banco de dados.

[!code-csharp[Main](adding-search/samples/sample12.cs)]

O código usa o `AddRange` método de genérica `List` coleção para adicionar todos os gêneros distintos à lista. (Sem o `Distinct` modificador, gêneros duplicados seriam adicionados — por exemplo, o comédia seria adicionada duas vezes em nosso exemplo). O código, em seguida, armazena a lista de gêneros no `ViewBag.MovieGenre` objeto. Armazenar dados de categoria (tal um gêneros de filme) como um [SelectList](https://msdn.microsoft.cus/library/system.web.mvc.selectlist(v=vs.108).aspx) do objeto em um `ViewBag`, em seguida, acessar os dados de categoria em uma caixa de listagem suspensa é uma abordagem típica para aplicativos MVC.

O código a seguir mostra como verificar o `movieGenre` parâmetro. Se não estiver vazia, o código ainda mais restringe a consulta de filmes para limitar os filmes selecionados para o gênero especificado.

[!code-csharp[Main](adding-search/samples/sample13.cs)]

Conforme mencionado anteriormente, a consulta não é executada no banco de dados até que a lista de filmes é iterada (o que ocorre no modo de exibição, após o `Index` retorna do método de ação).

## <a name="adding-markup-to-the-index-view-to-support-search-by-genre"></a>Adicionando marcação para a exibição de índice para dar suporte à pesquisa por gênero

Adicionar um `Html.DropDownList` auxiliar para o *Views\Movies\Index.cshtml* do arquivo, antes de `TextBox` auxiliar. A marcação concluída é mostrada abaixo:

[!code-cshtml[Main](adding-search/samples/sample14.cshtml?highlight=11)]

No código a seguir:

[!code-cshtml[Main](adding-search/samples/sample15.cshtml)]

O parâmetro "MovieGenre" fornece a chave para o `DropDownList` auxiliar para localizar um `IEnumerable<SelectListItem>` no `ViewBag`. O `ViewBag` foi populada no método de ação:

[!code-csharp[Main](adding-search/samples/sample16.cs?highlight=10)]

O parâmetro "All" fornece um rótulo de opção. Se você inspecionar essa escolha no seu navegador, você verá que o atributo "value" está vazio. Uma vez que o nosso controlador apenas filtra `if` a cadeia de caracteres não é `null` ou vazio, enviando um valor vazio para `movieGenre` mostra todos os gêneros.

Você também pode definir uma opção a ser selecionado por padrão. Se você quisesse "Comédia" como a opção padrão, altere o código no controlador da seguinte forma:

[!code-cshtml[Main](adding-search/samples/sample17.cshtml)]

Execute o aplicativo e navegue até */Movies/Index*. Tente uma pesquisa por gênero, nome do filme e ambos os critérios.

![](adding-search/_static/image8.png)

Nesta seção, você criou um método de ação de pesquisa e uma exibição que permitem aos usuários a pesquisar por título do filme e gênero. Na próxima seção, você verá como adicionar uma propriedade para o `Movie` modelo e como adicionar um inicializador que criará automaticamente um banco de dados de teste.

> [!div class="step-by-step"]
> [Anterior](examining-the-edit-methods-and-edit-view.md)
> [Próximo](adding-a-new-field.md)
