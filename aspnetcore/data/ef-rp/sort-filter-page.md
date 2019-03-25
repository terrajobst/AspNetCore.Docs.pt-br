---
title: Páginas Razor com o EF Core no ASP.NET Core – Classificação, filtro, paginação – 3 de 8
author: rick-anderson
description: Neste tutorial, você adicionará funcionalidades de classificação, filtragem e paginação à página usando o ASP.NET Core e o Entity Framework Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: data/ef-rp/sort-filter-page
ms.openlocfilehash: 162586059e3115bc15efaa63a9a0652e09872f1b
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58209959"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---sort-filter-paging---3-of-8"></a>Páginas Razor com o EF Core no ASP.NET Core – Classificação, filtro, paginação – 3 de 8

[!INCLUDE[2.0 version](~/includes/RP-EF/20-pdf.md)]

::: moniker range=">= aspnetcore-2.1"

Por [Tom Dykstra](https://github.com/tdykstra), [Rick Anderson](https://twitter.com/RickAndMSFT) e [Jon P Smith](https://twitter.com/thereformedprog)

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

Neste tutorial, as funcionalidades de classificação, filtragem, agrupamento e paginação são adicionadas.

A ilustração a seguir mostra uma página concluída. Os títulos de coluna são links clicáveis para classificar a coluna. Clicar em um título de coluna alterna repetidamente entre a ordem de classificação ascendente e descendente.

![Página Índice de alunos](sort-filter-page/_static/paging.png)

Caso tenha problemas que não consiga resolver, baixe o [aplicativo concluído](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).

## <a name="add-sorting-to-the-index-page"></a>Adicionar uma classificação à página Índice

Adicione cadeias de caracteres ao `PageModel` de *Students/Index.cshtml.cs* para que ele contenha os parâmetros de classificação:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet1&highlight=10-13)]

Atualize `OnGetAsync` de *Students/Index.cshtml.cs* com o seguinte código:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnly)]

O código anterior recebe um parâmetro `sortOrder` da cadeia de caracteres de consulta na URL. A URL (incluindo a cadeia de caracteres de consulta) é gerada pelo [Auxiliar de Marcação de Âncora](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper
)

O parâmetro `sortOrder` é "Name" ou "Data". O parâmetro `sortOrder` é opcionalmente seguido de "_desc" para especificar a ordem descendente. A ordem de classificação crescente é padrão.

Quando a página Índice é solicitada do link **Alunos**, não há nenhuma cadeia de caracteres de consulta. Os alunos são exibidos em ordem ascendente por sobrenome. A ordem ascendente por sobrenome é o padrão (caso fall-through) na instrução `switch`. Quando o usuário clica em um link de título de coluna, o valor `sortOrder` apropriado é fornecido no valor de cadeia de caracteres de consulta.

`NameSort` e `DateSort` são usados pela Página do Razor para configurar os hiperlinks de título de coluna com os valores de cadeia de caracteres de consulta apropriados:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnly&highlight=3-4)]

O seguinte código contém o [operador ?:](/dotnet/csharp/language-reference/operators/conditional-operator) condicional do C#:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_Ternary)]

A primeira linha especifica que, quando `sortOrder` é nulo ou vazio, `NameSort` é definido como "name_desc". Se `sortOrder` **não** é nulo nem vazio, `NameSort` é definido como uma cadeia de caracteres vazia.

O `?: operator` também é conhecido como o operador ternário.

Essas duas instruções permitem que a página defina os hiperlinks de título de coluna da seguinte maneira:

| Ordem de classificação atual | Hiperlink do sobrenome | Hiperlink de data |
|:--------------------:|:-------------------:|:--------------:|
| Sobrenome ascendente | descending        | ascending      |
| Sobrenome descendente | ascending           | ascending      |
| Data ascendente       | ascending           | descending     |
| Data descendente      | ascending           | ascending      |

O método usa o LINQ to Entities para especificar a coluna pela qual classificar. O código inicializa um `IQueryable<Student>` antes da instrução switch e modifica-o na instrução switch:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnly&highlight=6-999)]

 Quando um `IQueryable` é criado ou modificado, nenhuma consulta é enviada ao banco de dados. A consulta não é executada até que o objeto `IQueryable` seja convertido em uma coleção. `IQueryable` são convertidos em uma coleção com uma chamada a um método como `ToListAsync`. Portanto, o código `IQueryable` resulta em uma única consulta que não é executada até que a seguinte instrução:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnlyRtn)]

`OnGetAsync` pode ficar detalhado com um grande número de colunas classificáveis.

### <a name="add-column-heading-hyperlinks-to-the-student-index-page"></a>Adicionar hiperlinks de título de coluna à página Student Index

Substitua o código em *Students/Index.cshtml*, pelo seguinte código realçado:

[!code-html[](intro/samples/cu21/Pages/Students/Index2.cshtml?highlight=17-19,25-27)]

O código anterior:

* Adiciona hiperlinks aos títulos de coluna `LastName` e `EnrollmentDate`.
* Usa as informações em `NameSort` e `DateSort` para configurar hiperlinks com os valores de ordem de classificação atuais.

Para verificar se a classificação funciona:

* Execute o aplicativo e selecione a guia **Alunos**.
* Clique em **Sobrenome**.
* Clique em **Data de Registro**.

Para obter um melhor entendimento do código:

* Em *Students/Index.cshtml.cs*, defina um ponto de interrupção em `switch (sortOrder)`.
* Adicione uma inspeção para `NameSort` e `DateSort`.
* Em *Students/Index.cshtml*, defina um ponto de interrupção em `@Html.DisplayNameFor(model => model.Student[0].LastName)`.

Execute o depurador em etapas.

## <a name="add-a-search-box-to-the-students-index-page"></a>Adicionar uma Caixa de Pesquisa à página Índice de Alunos

Para adicionar a filtragem à página Índice de Alunos:

* Uma caixa de texto e um botão Enviar são adicionados à Página do Razor. A caixa de texto fornece uma cadeia de caracteres de pesquisa no nome ou sobrenome.
* O modelo de página é atualizado para usar o valor da caixa de texto.

### <a name="add-filtering-functionality-to-the-index-method"></a>Adicionar a funcionalidade de filtragem a método Index

Atualize `OnGetAsync` de *Students/Index.cshtml.cs* com o seguinte código:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilter&highlight=1,5,9-13)]

O código anterior:

* Adiciona o parâmetro `searchString` ao método `OnGetAsync`. O valor de cadeia de caracteres de pesquisa é recebido de uma caixa de texto que é adicionada na próxima seção.
* Adicionou uma cláusula `Where` à instrução LINQ. A cláusula `Where` seleciona somente os alunos cujo nome ou sobrenome contém a cadeia de caracteres de pesquisa. A instrução LINQ é executada somente se há um valor a ser pesquisado.

Observação: o código anterior chama o método `Where` em um objeto `IQueryable`, e o filtro é processado no servidor. Em alguns cenários, o aplicativo pode chamar o método `Where` como um método de extensão em uma coleção em memória. Por exemplo, suponha que `_context.Students` seja alterado do `DbSet` do EF Core para um método de repositório que retorna uma coleção `IEnumerable`. O resultado normalmente é o mesmo, mas em alguns casos pode ser diferente.

Por exemplo, a implementação do .NET Framework do `Contains` executa uma comparação diferencia maiúsculas de minúsculas por padrão. No SQL Server, a diferenciação de maiúsculas e minúsculas de `Contains` é determinada pela configuração de ordenação da instância do SQL Server. O SQL Server usa como padrão a não diferenciação de maiúsculas e minúsculas. `ToUpper` pode ser chamado para fazer com que o teste diferencie maiúsculas de minúsculas de forma explícita:

`Where(s => s.LastName.ToUpper().Contains(searchString.ToUpper())`

O código anterior garantirá que os resultados diferenciem maiúsculas de minúsculas se o código for alterado para usar `IEnumerable`. Quando `Contains` é chamado em uma coleção `IEnumerable`, a implementação do .NET Core é usada. Quando `Contains` é chamado em um objeto `IQueryable`, a implementação do banco de dados é usada. O retorno de um `IEnumerable` de um repositório pode ter uma penalidade significativa de desempenho:

1. Todas as linhas são retornadas do servidor de BD.
1. O filtro é aplicado a todas as linhas retornadas no aplicativo.

Há uma penalidade de desempenho por chamar `ToUpper`. O código `ToUpper` adiciona uma função à cláusula WHERE da instrução TSQL SELECT. A função adicionada impede que o otimizador use um índice. Considerando que o SQL é instalado como diferenciando maiúsculas de minúsculas, é melhor evitar a chamada `ToUpper` quando ela não for necessária.

### <a name="add-a-search-box-to-the-student-index-page"></a>Adicionar uma Caixa de Pesquisa à página Student Index

Em *Pages/Students/Index.cshtml*, adicione o código realçado a seguir para criar um botão **Pesquisar** e o cromado variado.

[!code-html[](intro/samples/cu21/Pages/Students/Index3.cshtml?highlight=14-23&range=1-25)]

O código anterior usa o [auxiliar de marcação](xref:mvc/views/tag-helpers/intro) `<form>` para adicionar o botão e a caixa de texto de pesquisa. Por padrão, o auxiliar de marcação `<form>` envia dados de formulário com um POST. Com o POST, os parâmetros são passados no corpo da mensagem HTTP e não na URL. Quando o HTTP GET é usado, os dados de formulário são passados na URL como cadeias de consulta. Passar os dados com cadeias de consulta permite aos usuários marcar a URL. As [diretrizes do W3C](https://www.w3.org/2001/tag/doc/whenToUseGet.html) recomendam o uso de GET quando a ação não resulta em uma atualização.

Teste o aplicativo:

* Selecione a guia **Alunos** e insira uma cadeia de caracteres de pesquisa.
* Selecione **Pesquisar**.

Observe que a URL contém a cadeia de caracteres de pesquisa.

```html
http://localhost:5000/Students?SearchString=an
```

Se a página estiver marcada, o indicador conterá a URL para a página e a cadeia de caracteres de consulta `SearchString`. O `method="get"` na marcação `form` é o que fez com que a cadeia de caracteres de consulta fosse gerada.

Atualmente, quando um link de classificação de título de coluna é selecionado, o valor de filtro da caixa **Pesquisa** é perdido. O valor de filtro perdido é corrigido na próxima seção.

## <a name="add-paging-functionality-to-the-students-index-page"></a>Adicionar a funcionalidade de paginação à página Índice de Alunos

Nesta seção, uma classe `PaginatedList` é criada para dar suporte à paginação. A classe `PaginatedList` usa as instruções `Skip` e `Take` para filtrar dados no servidor em vez de recuperar todas as linhas da tabela. A ilustração a seguir mostra os botões de paginação.

![Página Índice de alunos com links de paginação](sort-filter-page/_static/paging.png)

Na pasta do projeto, crie `PaginatedList.cs` com o seguinte código:

[!code-csharp[](intro/samples/cu21/PaginatedList.cs)]

O método `CreateAsync` no código anterior usa o tamanho da página e o número da página e aplica as instruções `Skip` e `Take` ao `IQueryable`. Quando `ToListAsync` é chamado no `IQueryable`, ele retorna uma Lista que contém somente a página solicitada. As propriedades `HasPreviousPage` e `HasNextPage` são usadas para habilitar ou desabilitar os botões de paginação **Anterior** e **Próximo**.

O método `CreateAsync` é usado para criar o `PaginatedList<T>`. Um construtor não pode criar o objeto `PaginatedList<T>`; construtores não podem executar um código assíncrono.

## <a name="add-paging-functionality-to-the-index-method"></a>Adicionar a funcionalidade de paginação ao método Index

Em *Students/Index.cshtml.cs*, atualize o tipo de `Student` em `IList<Student>` para `PaginatedList<Student>`:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPageType)]

Atualize `OnGetAsync` de *Students/Index.cshtml.cs* com o seguinte código:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage&highlight=1-4,7-14,41-999)]

O código anterior adiciona o índice de página, o `sortOrder` atual e o `currentFilter` à assinatura do método.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage2)]

Todos os parâmetros são nulos quando:

* A página é chamada no link **Alunos**.
* O usuário ainda não clicou em um link de paginação ou classificação.

Quando um link de paginação recebe um clique, a variável de índice de páginas contém o número da página a ser exibido.

`CurrentSort` fornece à Página do Razor a ordem de classificação atual. A ordem de classificação atual precisa ser incluída nos links de paginação para que a ordem de classificação seja mantida durante a paginação.

`CurrentFilter` fornece à Página do Razor a cadeia de caracteres de filtro atual. O valor `CurrentFilter`:

* Deve ser incluído nos links de paginação para que as configurações de filtro sejam mantidas durante a paginação.
* Deve ser restaurado para a caixa de texto quando a página é exibida novamente.

Se a cadeia de caracteres de pesquisa é alterada durante a paginação, a página é redefinida como 1. A página precisa ser redefinida como 1, porque o novo filtro pode resultar na exibição de dados diferentes. Quando um valor de pesquisa é inserido e **Enviar** é selecionado:

* A cadeia de caracteres de pesquisa foi alterada.
* O parâmetro `searchString` não é nulo.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage3)]

O método `PaginatedList.CreateAsync` converte a consulta de alunos em uma única página de alunos de um tipo de coleção compatível com paginação. Essa única página de alunos é passada para a Página do Razor.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortFilterPage4)]

Os dois pontos de interrogação em `PaginatedList.CreateAsync` representam o [operador de união de nulo](/dotnet/csharp/language-reference/operators/null-conditional-operator). O operador de união de nulo define um valor padrão para um tipo que permite valor nulo. A expressão `(pageIndex ?? 1)` significará retornar o valor de `pageIndex` se ele tiver um valor. Se `pageIndex` não tiver um valor, 1 será retornado.

## <a name="add-paging-links-to-the-student-razor-page"></a>Adicionar links de paginação à Página do Razor do aluno

Atualize a marcação em *Students/Index.cshtml*. As alterações são realçadas:

[!code-html[](intro/samples/cu21/Pages/Students/Index.cshtml?highlight=28-31,37-40,68-999)]

Os links de cabeçalho de coluna usam a cadeia de caracteres de consulta para passar a cadeia de caracteres de pesquisa atual para o método `OnGetAsync`, de modo que o usuário possa classificar nos resultados do filtro:

[!code-html[](intro/samples/cu21/Pages/Students/Index.cshtml?range=28-31)]

Os botões de paginação são exibidos por auxiliares de marcação:

[!code-html[](intro/samples/cu21/Pages/Students/Index.cshtml?range=72-)]

Execute o aplicativo e navegue para a página de alunos.

* Para verificar se a paginação funciona, clique nos links de paginação em ordens de classificação diferentes.
* Para verificar se a paginação funciona corretamente com a classificação e filtragem, insira uma cadeia de caracteres de pesquisa e tente fazer a paginação.

![Página Índice de Alunos com links de paginação](sort-filter-page/_static/paging.png)

Para obter um melhor entendimento do código:

* Em *Students/Index.cshtml.cs*, defina um ponto de interrupção em `switch (sortOrder)`.
* Adicione uma inspeção para `NameSort`, `DateSort`, `CurrentSort` e `Model.Student.PageIndex`.
* Em *Students/Index.cshtml*, defina um ponto de interrupção em `@Html.DisplayNameFor(model => model.Student[0].LastName)`.

Execute o depurador em etapas.

## <a name="update-the-about-page-to-show-student-statistics"></a>Atualizar a página Sobre para mostras estatísticas de alunos

Nesta etapa, *Pages/About.cshtml* é atualizada para exibir quantos alunos se registraram para cada data de registro. A atualização usa o agrupamento e inclui as seguintes etapas:

* Criar um modelo de exibição para os dados usados pela página **Sobre**.
* Atualizar a página Sobre para usar o modelo de exibição.

### <a name="create-the-view-model"></a>Criar o modelo de exibição

Crie uma pasta *SchoolViewModels* na pasta *Models*.

Na pasta *SchoolViewModels*, adicione um *EnrollmentDateGroup.cs* com o seguinte código:

[!code-csharp[](intro/samples/cu21/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="update-the-about-page-model"></a>Atualizar o modelo da página Sobre

Os modelos da Web no ASP.NET Core 2.2 não incluem a página Sobre. Se estiver usando o ASP.NET Core 2.2, crie a página Sobre o Razor.

Atualize o arquivo *Pages/About.cshtml.cs* com o seguinte código:

[!code-csharp[](intro/samples/cu21/Pages/About.cshtml.cs)]

A instrução LINQ agrupa as entidades de alunos por data de registro, calcula o número de entidades em cada grupo e armazena os resultados em uma coleção de objetos de modelo de exibição `EnrollmentDateGroup`.

### <a name="modify-the-about-razor-page"></a>Modificar a Página Sobre do Razor

Substitua o código no arquivo *Pages/About.cshtml* pelo seguinte código:

[!code-html[](intro/samples/cu21/Pages/About.cshtml)]

Execute o aplicativo e navegue para a página Sobre. A contagem de alunos para cada data de registro é exibida em uma tabela.

Caso tenha problemas que não consiga resolver, baixe o [aplicativo concluído para este estágio](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part3-sorting).

![Página Sobre](sort-filter-page/_static/about.png)

## <a name="additional-resources"></a>Recursos adicionais

* [Depuração de origem do ASP.NET Core 2.x](https://github.com/aspnet/Docs/issues/4155)
* [Versão do YouTube deste tutorial](https://www.youtube.com/watch?v=MDs7PFpoMqI)

No próximo tutorial, o aplicativo usa migrações para atualizar o modelo de dados.

::: moniker-end

> [!div class="step-by-step"]
> [Anterior](xref:data/ef-rp/crud)
> [Próximo](xref:data/ef-rp/migrations)
