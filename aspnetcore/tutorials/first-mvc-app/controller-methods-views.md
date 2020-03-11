---
title: Os métodos e as exibições do controlador no ASP.NET Core
author: rick-anderson
description: Aprenda a trabalhar com os métodos, as exibições e as DataAnnotations do controlador no ASP.NET Core.
ms.author: riande
ms.date: 12/13/2018
uid: tutorials/first-mvc-app/controller-methods-views
ms.openlocfilehash: 2c442060872ab1d2d79a2e355ae257fdf1005914
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78663502"
---
# <a name="controller-methods-and-views-in-aspnet-core"></a>Os métodos e as exibições do controlador no ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

Temos um bom começo para o aplicativo de filme, mas a apresentação não é ideal. Por exemplo, **ReleaseDate** deveria ser separado em duas palavras.

![Exibição de índice: a Data de Lançamento é uma palavra (sem espaço) e cada data de lançamento do filme mostra o horário 12h](working-with-sql/_static/m55.png)

Abra o arquivo *Models/Movie.cs* e adicione as linhas realçadas mostradas abaixo:

[!code-csharp[](start-mvc/sample/MvcMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=2,3,12-13,17)]

Abordaremos [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) no próximo tutorial. O atributo [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) especifica o que deve ser exibido no nome de um campo (neste caso, “Release Date” em vez de “ReleaseDate”). O atributo [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) especifica o tipo de dados (Data) e, portanto, as informações de hora armazenadas no campo não são exibidas.

A anotação de dados `[Column(TypeName = "decimal(18, 2)")]` é necessária para que o Entity Framework Core possa mapear corretamente o `Price` para a moeda no banco de dados. Para obter mais informações, veja [Tipos de Dados](/ef/core/modeling/relational/data-types).

Procure o controlador `Movies` e mantenha o ponteiro do mouse pressionado sobre um link **Editar** para ver a URL de destino.

![São mostrados uma janela do navegador com o mouse sobre o link de edição e um link da URL https://localhost:5001/Movies/Edit/5](~/tutorials/first-mvc-app/controller-methods-views/_static/edit7.png)

Os links **Editar**, **Detalhes** e **Excluir** são gerados pelo Auxiliar de Marcação de Âncora do MVC Core no arquivo *Views/Movies/Index.cshtml*.

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexOriginal.cshtml?highlight=1-3&range=46-50)]

Os [Auxiliares de Marcação](xref:mvc/views/tag-helpers/intro) permitem que o código do servidor participe da criação e renderização de elementos HTML em arquivos do Razor. No código acima, o `AnchorTagHelper` gera dinamicamente o valor do atributo HTML `href` do método de ação do controlador e da ID da rota. Use o **modo de exibição de origem** do seu navegador favorito ou use as ferramentas de desenvolvedor para examinar a marcação gerada. Uma parte do HTML gerado é mostrada abaixo:

```html
 <td>
    <a href="/Movies/Edit/4"> Edit </a> |
    <a href="/Movies/Details/4"> Details </a> |
    <a href="/Movies/Delete/4"> Delete </a>
</td>
```

Lembre-se do formato do [roteamento](xref:mvc/controllers/routing) definido no arquivo *Startup.cs*:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

O ASP.NET Core converte `https://localhost:5001/Movies/Edit/4` de uma solicitação no método de ação `Edit` do controlador `Movies` com o parâmetro `Id` igual a 4. (Os métodos do controlador também são conhecidos como métodos de ação.)

Os [Auxiliares de Marcação](xref:mvc/views/tag-helpers/intro) são um dos novos recursos mais populares do ASP.NET Core. Para obter mais informações, consulte [Recursos adicionais](#additional-resources).

Abra o controlador `Movies` e examine os dois métodos de ação `Edit`. O código a seguir mostra o método `HTTP GET Edit`, que busca o filme e popula o formato de edição gerado pelo arquivo *Edit.cshtml* do Razor.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit1)]

O código a seguir mostra o método `HTTP POST Edit`, que processa os valores de filmes postados:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit1)]

O código a seguir mostra o método `HTTP POST Edit`, que processa os valores de filmes postados:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

O atributo `[Bind]` é uma maneira de proteger contra o [excesso de postagem](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost). Você somente deve incluir as propriedades do atributo `[Bind]` que deseja alterar. Para obter mais informações, consulte [Proteger o controlador contra o excesso de postagem](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application). [ViewModels](https://rachelappel.com/use-viewmodels-to-manage-data-amp-organize-code-in-asp-net-mvc-applications/) fornece uma abordagem alternativa para prevenir o excesso de postagem.

Observe se o segundo método de ação `Edit` é precedido pelo atributo `[HttpPost]`.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit2&highlight=1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2&highlight=4)]

::: moniker-end

O atributo `HttpPost` especifica que esse método `Edit` pode ser invocado *somente* para solicitações `POST`. Você pode aplicar o atributo `[HttpGet]` ao primeiro método de edição, mas isso não é necessário porque `[HttpGet]` é o padrão.

O atributo `ValidateAntiForgeryToken` é usado para [prevenir a falsificação de uma solicitação](xref:security/anti-request-forgery) e é associado a um token antifalsificação gerado no arquivo de exibição de edição (*Views/Movies/Edit.cshtml*). O arquivo de exibição de edição gera o token antifalsificação com o [Auxiliar de Marcação de Formulário](xref:mvc/views/working-with-forms).

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/Edit.cshtml?range=9)]

O [Auxiliar de Marcação de Formulário](xref:mvc/views/working-with-forms) gera um token antifalsificação oculto que deve corresponder ao token antifalsificação gerado `[ValidateAntiForgeryToken]` no método `Edit` do controlador Movies. Para obter mais informações, consulte [Falsificação de antissolicitação](xref:security/anti-request-forgery).

O método `HttpGet Edit` usa o parâmetro `ID` de filme, pesquisa o filme usando o método `FindAsync` do Entity Framework e retorna o filme selecionado para a exibição de Edição. Se um filme não for encontrado, `NotFound` (HTTP 404) será retornado.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit1)]

Quando o sistema de scaffolding criou a exibição de Edição, ele examinou a classe `Movie` e o código criado para renderizar os elementos `<label>` e `<input>` de cada propriedade da classe. O seguinte exemplo mostra a exibição de Edição que foi gerada pelo sistema de scaffolding do Visual Studio:

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/EditOriginal.cshtml)]

Observe como o modelo de exibição tem uma instrução `@model MvcMovie.Models.Movie` na parte superior do arquivo. `@model MvcMovie.Models.Movie` especifica que a exibição espera que o modelo de exibição seja do tipo `Movie`.

O código com scaffolding usa vários métodos de Auxiliares de Marcação para simplificar a marcação HTML. O – [Auxiliar de Marcação de Rótulo](xref:mvc/views/working-with-forms) exibe o nome do campo (“Title”, “ReleaseDate”, “Genre” ou “Price”). O [Auxiliar de Marcação de Entrada](xref:mvc/views/working-with-forms) renderiza um elemento `<input>` HTML. O [Auxiliar de Marcação de Validação](xref:mvc/views/working-with-forms) exibe todas as mensagens de validação associadas a essa propriedade.

Execute o aplicativo e navegue para a URL `/Movies`. Clique em um link **Editar**. No navegador, exiba a origem da página. O HTML gerado para o elemento `<form>` é mostrado abaixo.

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Shared/edit_view_source.html?highlight=1,6,10,17,24,28)]

Os elementos `<input>` estão em um elemento `HTML <form>` cujo atributo `action` está definido para ser postado para a URL `/Movies/Edit/id`. Os dados de formulário serão postados com o servidor quando o botão `Save` receber um clique. A última linha antes do elemento `</form>` de fechamento mostra o token [XSRF](xref:security/anti-request-forgery) oculto gerado pelo [Auxiliar de Marcação de Formulário](xref:mvc/views/working-with-forms).

## <a name="processing-the-post-request"></a>Processando a solicitação POST

A lista a seguir mostra a versão `[HttpPost]` do método de ação `Edit`.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

O atributo `[ValidateAntiForgeryToken]` valida o token [XSRF](xref:security/anti-request-forgery) oculto gerado pelo gerador de tokens antifalsificação no [Auxiliar de Marcação de Formulário](xref:mvc/views/working-with-forms)

O sistema de [model binding](xref:mvc/models/model-binding) usa os valores de formulário postados e cria um objeto `Movie` que é passado como o parâmetro `movie`. O método `ModelState.IsValid` verifica se os dados enviados no formulário podem ser usados para modificar (editar ou atualizar) um objeto `Movie`. Se os dados forem válidos, eles serão salvos. Os dados de filmes atualizados (editados) são salvos no banco de dados chamando o método `SaveChangesAsync` do contexto de banco de dados. Depois de salvar os dados, o código redireciona o usuário para o método de ação `Index` da classe `MoviesController`, que exibe a coleção de filmes, incluindo as alterações feitas recentemente.

Antes que o formulário seja postado no servidor, a validação do lado do cliente verifica as regras de validação nos campos. Se houver erros de validação, será exibida uma mensagem de erro e o formulário não será postado. Se o JavaScript estiver desabilitado, você não terá a validação do lado do cliente, mas o servidor detectará os valores postados que não forem válidos e os valores de formulário serão exibidos novamente com mensagens de erro. Mais adiante no tutorial, examinamos a [Validação de Modelos](xref:mvc/models/validation) mais detalhadamente. O [Auxiliar de Marcação de Validação](xref:mvc/views/working-with-forms) no modelo de exibição *Views/Movies/Edit.cshtml* é responsável por exibir as mensagens de erro apropriadas.

![Exibição de edição: uma exceção para o valor de Preço incorreto abc informa que o campo Preço deve ser um número. Uma exceção para um valor incorreto de Data de Lançamento dos estados xyz; insira uma data válida.](~/tutorials/first-mvc-app/controller-methods-views/_static/val.png)

Todos os métodos `HttpGet` no controlador de filme seguem um padrão semelhante. Eles obtêm um objeto de filme (ou uma lista de objetos, no caso de `Index`) e passam o objeto (modelo) para a exibição. O método `Create` passa um objeto de filme vazio para a exibição `Create`. Todos os métodos que criam, editam, excluem ou, de outro modo, modificam dados fazem isso na sobrecarga `[HttpPost]` do método. A modificação de dados em um método `HTTP GET` é um risco de segurança. A modificação de dados em um método `HTTP GET` também viola as melhores práticas de HTTP e o padrão [REST](http://rest.elkstein.org/) de arquitetura, que especifica que as solicitações GET não devem alterar o estado do aplicativo. Em outras palavras, a execução de uma operação GET deve ser uma operação segura que não tem efeitos colaterais e não modifica os dados persistentes.

## <a name="additional-resources"></a>Recursos adicionais

* [Globalização e localização](xref:fundamentals/localization)
* [Introdução aos auxiliares de marcação](xref:mvc/views/tag-helpers/intro)
* [Auxiliares de marca de autor](xref:mvc/views/tag-helpers/authoring)
* [Falsificação anti-solicitação](xref:security/anti-request-forgery)
* Proteger o controlador contra o [excesso de postagem](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application)
* [ViewModels](https://rachelappel.com/use-viewmodels-to-manage-data-amp-organize-code-in-asp-net-mvc-applications/)
* [Auxiliar de marcação de formulário](xref:mvc/views/working-with-forms)
* [Auxiliar de marcação de entrada](xref:mvc/views/working-with-forms)
* [Auxiliar de marcação de rótulo](xref:mvc/views/working-with-forms)
* [Selecionar o auxiliar de marcação](xref:mvc/views/working-with-forms)
* [Auxiliar de marcação de validação](xref:mvc/views/working-with-forms)

> [!div class="step-by-step"]
> [Anterior](working-with-sql.md)
> [Próximo](search.md)  
