---
title: Páginas Razor com o EF Core no ASP.NET Core – Classificação, filtro, paginação – 3 de 8
author: rick-anderson
description: Neste tutorial, você adicionará funcionalidades de classificação, filtragem e paginação à página do Razor usando o ASP.NET Core e o Entity Framework Core.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
uid: data/ef-rp/sort-filter-page
ms.openlocfilehash: 9563f3ef52ce429eb0a58b468acb8e9cd7b276e2
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78656460"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---sort-filter-paging---3-of-8"></a>Páginas Razor com o EF Core no ASP.NET Core – Classificação, filtro, paginação – 3 de 8

Por [Tom Dykstra](https://github.com/tdykstra), [Rick Anderson](https://twitter.com/RickAndMSFT) e [Jon P Smith](https://twitter.com/thereformedprog)

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

Este tutorial adiciona as funcionalidades de classificação, filtragem e paginação à página do Aluno.

A ilustração a seguir mostra uma página concluída. Os títulos de coluna são links clicáveis para classificar a coluna. Clique no título de coluna para alternar repetidamente entre a ordem de classificação ascendente e descendente.

![Página Índice de alunos](sort-filter-page/_static/paging30.png)

## <a name="add-sorting"></a>Adicionar classificação

Substitua o código em *Pages/Students/Index.cshtml.cs* pelo código a seguir para adicionar classificação.

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml.cs?name=snippet_All&highlight=21-24,26,28-52)]

O código anterior:

* Adiciona propriedades para conter os parâmetros de classificação.
* Altera o nome da propriedade `Student` para `Students`.
* Substitui o código no método `OnGetAsync`.

O método `OnGetAsync` recebe um parâmetro `sortOrder` da cadeia de caracteres de consulta na URL. A URL (incluindo a cadeia de caracteres de consulta) é gerada pelo [Auxiliar de Tag de Âncora](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).

O parâmetro `sortOrder` é "Name" ou "Data". O parâmetro `sortOrder` é opcionalmente seguido de "_desc" para especificar a ordem descendente. A ordem de classificação padrão é crescente.

Quando a página Índice é solicitada do link **Alunos**, não há nenhuma cadeia de caracteres de consulta. Os alunos são exibidos em ordem ascendente por sobrenome. A ordem ascendente por sobrenome é o padrão (caso fall-through) na instrução `switch`. Quando o usuário clica em um link de título de coluna, o valor `sortOrder` apropriado é fornecido no valor de cadeia de caracteres de consulta.

`NameSort` e `DateSort` são usados pela Página do Razor para configurar os hiperlinks de título de coluna com os valores de cadeia de caracteres de consulta apropriados:

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml.cs?name=snippet_Ternary)]

O código usa o operador condicional C# [?:](/dotnet/csharp/language-reference/operators/conditional-operator). O operador `?:` é ternário (utiliza três operandos). A primeira linha especifica que, quando `sortOrder` é nulo ou vazio, `NameSort` é definido como "name_desc". Se `sortOrder`**não** é nulo nem vazio, `NameSort` é definido como uma cadeia de caracteres vazia.

Essas duas instruções permitem que a página defina os hiperlinks de título de coluna da seguinte maneira:

| Ordem de classificação atual   | Hiperlink do sobrenome | Hiperlink de data |
|:--------------------:|:-------------------:|:--------------:|
| Sobrenome ascendente  | descending          | ascending      |
| Sobrenome descendente | ascending           | ascending      |
| Data ascendente       | ascending           | descending     |
| Data descendente      | ascending           | ascending      |

O método usa o LINQ to Entities para especificar a coluna pela qual classificar. O código inicializa um `IQueryable<Student>` antes da instrução switch e modifica-o na instrução switch:

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml.cs?name=snippet_IQueryable)]

Quando um `IQueryable` é criado ou modificado, nenhuma consulta é enviada ao banco de dados. A consulta não é executada até que o objeto `IQueryable` seja convertido em uma coleção. `IQueryable` são convertidos em uma coleção com uma chamada a um método como `ToListAsync`. Portanto, o código `IQueryable` resulta em uma única consulta que não é executada até que a seguinte instrução:

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml.cs?name=snippet_SortOnlyRtn)]

`OnGetAsync` pode ficar detalhado com um grande número de colunas classificáveis. Para obter informações sobre uma maneira alternativa de codificar essa funcionalidade, confira [Usar o LINQ dinâmico para simplificar o código](xref:data/ef-mvc/advanced#dynamic-linq) na versão MVC desta série de tutoriais.

### <a name="add-column-heading-hyperlinks-to-the-student-index-page"></a>Adicionar hiperlinks de título de coluna à página Student Index

Substitua o código em *Students/Index.cshtml* pelo código a seguir. As alterações são realçadas.

[!code-cshtml[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index1.cshtml?highlight=5,8,17-19,22,25-27,33)]

O código anterior:

* Adiciona hiperlinks aos títulos de coluna `LastName` e `EnrollmentDate`.
* Usa as informações em `NameSort` e `DateSort` para configurar hiperlinks com os valores de ordem de classificação atuais.
* Altera o título da página de Índice para Alunos.
* Altera `Model.Student` para `Model.Students`.

Para verificar se a classificação funciona:

* Execute o aplicativo e selecione a guia **Alunos**.
* Clique nos títulos de coluna.

## <a name="add-filtering"></a>Adicionar filtragem

Para adicionar a filtragem à página Índice de Alunos:

* Uma caixa de texto e um botão Enviar são adicionados à Página do Razor. A caixa de texto fornece uma cadeia de caracteres de pesquisa no nome ou sobrenome.
* O modelo de página é atualizado para usar o valor da caixa de texto.

### <a name="update-the-ongetasync-method"></a>Atualizar o método OnGetAsync

Substitua o código em *Students/Index.cshtml.cs* pelo código a seguir para adicionar filtragem:

[!code-csharp[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index2.cshtml.cs?name=snippet_All&highlight=28,33,37-41)]

O código anterior:

* Adiciona o parâmetro `searchString` ao método `OnGetAsync` e salva o valor do parâmetro na propriedade `CurrentFilter`. O valor de cadeia de caracteres de pesquisa é recebido de uma caixa de texto que é adicionada na próxima seção.
* Adiciona uma cláusula `Where` à instrução LINQ. A cláusula `Where` seleciona somente os alunos cujo nome ou sobrenome contém a cadeia de caracteres de pesquisa. A instrução LINQ é executada somente se há um valor a ser pesquisado.

### <a name="iqueryable-vs-ienumerable"></a>IQueryable versus IEnumerable

O código chama o método `Where` em um objeto `IQueryable`, e o filtro é processado no servidor. Em alguns cenários, o aplicativo pode chamar o método `Where` como um método de extensão em uma coleção em memória. Por exemplo, suponha que `_context.Students` seja alterado do `DbSet` do EF Core para um método de repositório que retorna uma coleção `IEnumerable`. O resultado normalmente é o mesmo, mas em alguns casos pode ser diferente.

Por exemplo, a implementação do .NET Framework do `Contains` executa uma comparação diferencia maiúsculas de minúsculas por padrão. No SQL Server, a diferenciação de maiúsculas e minúsculas de `Contains` é determinada pela configuração de ordenação da instância do SQL Server. O SQL Server usa como padrão a não diferenciação de maiúsculas e minúsculas. O SQLite assume como padrão diferenciar maiúsculas de minúsculas. `ToUpper` pode ser chamado para fazer com que o teste diferencie maiúsculas de minúsculas de forma explícita:

```csharp
Where(s => s.LastName.ToUpper().Contains(searchString.ToUpper())`
```

O código anterior garantiria que o filtro não diferenciasse maiúsculas de minúsculas mesmo que o método `Where` fosse chamado em um `IEnumerable` ou fosse executado no SQLite.

Quando `Contains` é chamado em uma coleção `IEnumerable`, a implementação do .NET Core é usada. Quando `Contains` é chamado em um objeto `IQueryable`, a implementação do banco de dados é usada.

Chamar `Contains` em um `IQueryable` é geralmente preferível por motivos de desempenho. Com `IQueryable`, a filtragem é feita pelo servidor de banco de dados. Se um `IEnumerable` for criado primeiro, todas as linhas precisarão ser retornadas do servidor de banco de dados.

Há uma penalidade de desempenho por chamar `ToUpper`. O código `ToUpper` adiciona uma função à cláusula WHERE da instrução TSQL SELECT. A função adicionada impede que o otimizador use um índice. Considerando que o SQL é instalado como diferenciando maiúsculas de minúsculas, é melhor evitar a chamada `ToUpper` quando ela não for necessária.

Para obter mais informações, confira [Como usar consulta que não diferencia maiúsculas de minúsculas com o provedor SQLite](https://github.com/aspnet/EntityFrameworkCore/issues/11414).

### <a name="update-the-razor-page"></a>Atualizar a páginas do Razor

Substitua o código em *Pages/Students/index.cshtml* para criar um botão de **Pesquisa** e um cromado classificado.

[!code-cshtml[Main](intro/samples/cu30snapshots/3-sorting/Pages/Students/Index2.cshtml?highlight=14-23)]

O código anterior usa o [auxiliar de marca](xref:mvc/views/tag-helpers/intro) de `<form>` para adicionar a caixa de texto de pesquisa e o botão. Por padrão, o auxiliar de marcação `<form>` envia dados de formulário com um POST. Com o POST, os parâmetros são passados no corpo da mensagem HTTP e não na URL. Quando o HTTP GET é usado, os dados de formulário são passados na URL como cadeias de consulta. Passar os dados com cadeias de consulta permite aos usuários marcar a URL. As [diretrizes do W3C](https://www.w3.org/2001/tag/doc/whenToUseGet.html) recomendam o uso de GET quando a ação não resulta em uma atualização.

Teste o aplicativo:

* Selecione a guia **Alunos** e insira uma cadeia de caracteres de pesquisa. Se você estiver usando o SQLite, o filtro não diferenciará maiúsculas de minúsculas apenas se você tiver implementado o código opcional `ToUpper` mostrado anteriormente.

* Selecione **Pesquisar**.

Observe que a URL contém a cadeia de caracteres de pesquisa. Por exemplo:

```
https://localhost:<port>/Students?SearchString=an
```

Se a página estiver marcada, o indicador conterá a URL para a página e a cadeia de caracteres de consulta `SearchString`. O `method="get"` na marcação `form` é o que fez com que a cadeia de caracteres de consulta fosse gerada.

Atualmente, quando um link de classificação de título de coluna é selecionado, o valor de filtro da caixa **Pesquisa** é perdido. O valor de filtro perdido é corrigido na próxima seção.

## <a name="add-paging"></a>Adicionar paginação

Nesta seção, uma classe `PaginatedList` é criada para dar suporte à paginação. A classe `PaginatedList` usa as instruções `Skip` e `Take` para filtrar dados no servidor em vez de recuperar todas as linhas da tabela. A ilustração a seguir mostra os botões de paginação.

![Página Índice de alunos com links de paginação](sort-filter-page/_static/paging30.png)

### <a name="create-the-paginatedlist-class"></a>Criar a classe PaginatedList

Na pasta do projeto, crie `PaginatedList.cs` com o seguinte código:

[!code-csharp[Main](intro/samples/cu30/PaginatedList.cs)]

O método `CreateAsync` no código anterior usa o tamanho da página e o número da página e aplica as instruções `Skip` e `Take` ao `IQueryable`. Quando `ToListAsync` é chamado no `IQueryable`, ele retorna uma Lista que contém somente a página solicitada. As propriedades `HasPreviousPage` e `HasNextPage` são usadas para habilitar ou desabilitar os botões de paginação **Anterior** e **Próximo**.

O método `CreateAsync` é usado para criar o `PaginatedList<T>`. Um construtor não pode criar o objeto `PaginatedList<T>`; construtores não podem executar um código assíncrono.

### <a name="add-paging-to-the-pagemodel-class"></a>Adicionar paginação à classe PageModel

Substitua o código em *Students/index.cshtml.cs* para adicionar paginação.

[!code-csharp[Main](intro/samples/cu30/Pages/Students/Index.cshtml.cs?name=snippet_All&highlight=26,28-29,31,34-41,68-70)]

O código anterior:

* Altera o tipo da propriedade `Students` de `IList<Student>` para `PaginatedList<Student>`.
* Adiciona o índice de página, o `sortOrder` atual e o `currentFilter` à assinatura do método `OnGetAsync`.
* Salva a ordem de classificação na propriedade CurrentSort.
* Redefine o índice de página como 1 quando há uma nova cadeia de caracteres de pesquisa.
* Usa a classe `PaginatedList` para obter entidades de Aluno.

Todos os parâmetros que `OnGetAsync` recebe são nulos quando:

* A página é chamada no link **Alunos**.
* O usuário ainda não clicou em um link de paginação ou classificação.

Quando um link de paginação recebe um clique, a variável de índice de páginas contém o número da página a ser exibido.

A propriedade `CurrentSort` fornece à Página do Razor a ordem de classificação atual. A ordem de classificação atual precisa ser incluída nos links de paginação para que a ordem de classificação seja mantida durante a paginação.

A propriedade `CurrentFilter` fornece à Página do Razor a cadeia de caracteres de filtro atual. O valor `CurrentFilter`:

* Deve ser incluído nos links de paginação para que as configurações de filtro sejam mantidas durante a paginação.
* Deve ser restaurado para a caixa de texto quando a página é exibida novamente.

Se a cadeia de caracteres de pesquisa é alterada durante a paginação, a página é redefinida como 1. A página precisa ser redefinida como 1, porque o novo filtro pode resultar na exibição de dados diferentes. Quando um valor de pesquisa é inserido e **Enviar** é selecionado:

  * A cadeia de caracteres de pesquisa foi alterada.
  * O parâmetro `searchString` não é nulo.

  O método `PaginatedList.CreateAsync` converte a consulta de alunos em uma única página de alunos de um tipo de coleção compatível com paginação. Essa única página de alunos é passada para a Página do Razor.

  Os dois pontos de interrogação em `pageIndex` na chamada `PaginatedList.CreateAsync` representam o [operador de união de nulo](/dotnet/csharp/language-reference/operators/null-conditional-operator). O operador de união de nulo define um valor padrão para um tipo que permite valor nulo. A expressão `(pageIndex ?? 1)` significará retornar o valor de `pageIndex` se ele tiver um valor. Se `pageIndex` não tiver um valor, 1 será retornado.

### <a name="add-paging-links-to-the-razor-page"></a>Adicionar links de paginação à Página do Razor

Substitua o código em *Students/Index.cshtml* pelo código a seguir. As alterações são realçadas:

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Index.cshtml?highlight=29-32,38-41,69-87)]

Os links de cabeçalho de coluna usam a cadeia de caracteres de consulta para passar a cadeia de caracteres de pesquisa atual para o método `OnGetAsync`:

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Index.cshtml?range=29-32)]

Os botões de paginação são exibidos por auxiliares de marcação:

[!code-cshtml[Main](intro/samples/cu30/Pages/Students/Index.cshtml?range=73-87)]

Execute o aplicativo e navegue para a página de alunos.

* Para verificar se a paginação funciona, clique nos links de paginação em ordens de classificação diferentes.
* Para verificar se a paginação funciona corretamente com a classificação e filtragem, insira uma cadeia de caracteres de pesquisa e tente fazer a paginação.

![Página Índice de Alunos com links de paginação](sort-filter-page/_static/paging30.png)

## <a name="add-grouping"></a>Adicionar agrupamento

Esta seção cria uma página Sobre que exibe quantos alunos se inscreveram para cada data de registro. A atualização usa o agrupamento e inclui as seguintes etapas:

* Criar um modelo de exibição para os dados usados pela página **Sobre**.
* Atualizar a página Sobre para usar o modelo de exibição.

### <a name="create-the-view-model"></a>Criar o modelo de exibição

Crie uma pasta *Models/SchoolViewModels*.

Crie *SchoolViewModels/EnrollmentDateGroup.cs* com o seguinte código:

[!code-csharp[Main](intro/samples/cu30/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="create-the-razor-page"></a>Criar a página do Razor

Crie um arquivo *Pages/About.cshtml* com o seguinte código:

[!code-cshtml[Main](intro/samples/cu30/Pages/About.cshtml)]

### <a name="create-the-page-model"></a>Criar o modelo de página

Crie um arquivo *Pages/About.cshtml.cs* com o seguinte código:

[!code-csharp[Main](intro/samples/cu30/Pages/About.cshtml.cs)]

A instrução LINQ agrupa as entidades de alunos por data de registro, calcula o número de entidades em cada grupo e armazena os resultados em uma coleção de objetos de modelo de exibição `EnrollmentDateGroup`.

Execute o aplicativo e navegue para a página Sobre. A contagem de alunos para cada data de registro é exibida em uma tabela.

![Página Sobre](sort-filter-page/_static/about30.png)

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

No próximo tutorial, o aplicativo usa migrações para atualizar o modelo de dados.

> [!div class="step-by-step"]
> [Tutorial anterior](xref:data/ef-rp/crud)
> [Próximo tutorial](xref:data/ef-rp/migrations)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Neste tutorial, as funcionalidades de classificação, filtragem, agrupamento e paginação são adicionadas.

A ilustração a seguir mostra uma página concluída. Os títulos de coluna são links clicáveis para classificar a coluna. Clicar em um título de coluna alterna repetidamente entre a ordem de classificação ascendente e descendente.

![Página Índice de alunos](sort-filter-page/_static/paging.png)

Caso tenha problemas que não consiga resolver, baixe o [aplicativo concluído](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).

## <a name="add-sorting-to-the-index-page"></a>Adicionar uma classificação à página Índice

Adicione cadeias de caracteres à `PageModel` *Students/index. cshtml. cs* para conter os parâmetros de classificação:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet1&highlight=10-13)]

Atualize os `OnGetAsync` *estudantes/index. cshtml. cs* com o seguinte código:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnly)]

O código anterior recebe um parâmetro `sortOrder` da cadeia de caracteres de consulta na URL. A URL (incluindo a cadeia de caracteres de consulta) é gerada pelo [Auxiliar de Marcação de Âncora](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper
)

O parâmetro `sortOrder` é "Name" ou "Data". O parâmetro `sortOrder` é opcionalmente seguido de "_desc" para especificar a ordem descendente. A ordem de classificação padrão é crescente.

Quando a página Índice é solicitada do link **Alunos**, não há nenhuma cadeia de caracteres de consulta. Os alunos são exibidos em ordem ascendente por sobrenome. A ordem ascendente por sobrenome é o padrão (caso fall-through) na instrução `switch`. Quando o usuário clica em um link de título de coluna, o valor `sortOrder` apropriado é fornecido no valor de cadeia de caracteres de consulta.

`NameSort` e `DateSort` são usados pela Página do Razor para configurar os hiperlinks de título de coluna com os valores de cadeia de caracteres de consulta apropriados:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_SortOnly&highlight=3-4)]

O seguinte código contém o [operador ?:](/dotnet/csharp/language-reference/operators/conditional-operator) condicional do C#:

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_Ternary)]

A primeira linha especifica que, quando `sortOrder` é nulo ou vazio, `NameSort` é definido como "name_desc". Se `sortOrder`**não** é nulo nem vazio, `NameSort` é definido como uma cadeia de caracteres vazia.

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

Atualize os `OnGetAsync` *estudantes/index. cshtml. cs* com o seguinte código:

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

O código anterior usa o [auxiliar de marca](xref:mvc/views/tag-helpers/intro) de `<form>` para adicionar a caixa de texto de pesquisa e o botão. Por padrão, o auxiliar de marcação `<form>` envia dados de formulário com um POST. Com o POST, os parâmetros são passados no corpo da mensagem HTTP e não na URL. Quando o HTTP GET é usado, os dados de formulário são passados na URL como cadeias de consulta. Passar os dados com cadeias de consulta permite aos usuários marcar a URL. As [diretrizes do W3C](https://www.w3.org/2001/tag/doc/whenToUseGet.html) recomendam o uso de GET quando a ação não resulta em uma atualização.

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

Atualize os `OnGetAsync` *estudantes/index. cshtml. cs* com o seguinte código:

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

Caso tenha problemas que não consiga resolver, baixe o [aplicativo concluído para este estágio](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part3-sorting).

![Página Sobre](sort-filter-page/_static/about.png)

## <a name="additional-resources"></a>Recursos adicionais

* [Depuração de origem do ASP.NET Core 2.x](https://github.com/dotnet/AspNetCore.Docs/issues/4155)
* [Versão do YouTube deste tutorial](https://www.youtube.com/watch?v=MDs7PFpoMqI)

No próximo tutorial, o aplicativo usa migrações para atualizar o modelo de dados.

> [!div class="step-by-step"]
> [Anterior](xref:data/ef-rp/crud)
> [Próximo](xref:data/ef-rp/migrations)

::: moniker-end

