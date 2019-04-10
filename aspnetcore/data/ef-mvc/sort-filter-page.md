---
title: 'Tutorial: Adicionar classificação, filtragem e paginação - ASP.NET Core MVC com EF Core'
description: Neste tutorial você adicionará as funcionalidades de classificação, filtragem e paginação à página Índice de Alunos. Você também criará uma página que faz um agrupamento simples.
author: rick-anderson
ms.author: tdykstra
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/sort-filter-page
ms.openlocfilehash: dff5a5b1ba3c8ed07ccc8d134f8cfeb25b9f6689
ms.sourcegitcommit: 3e9e1f6d572947e15347e818f769e27dea56b648
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2019
ms.locfileid: "58751045"
---
# <a name="tutorial-add-sorting-filtering-and-paging---aspnet-mvc-with-ef-core"></a>Tutorial: Adicionar classificação, filtragem e paginação - ASP.NET Core MVC com EF Core

No tutorial anterior, você implementou um conjunto de páginas da Web para operações CRUD básicas para entidades Student. Neste tutorial você adicionará as funcionalidades de classificação, filtragem e paginação à página Índice de Alunos. Você também criará uma página que faz um agrupamento simples.

A ilustração a seguir mostra a aparência da página quando você terminar. Os títulos de coluna são links que o usuário pode clicar para classificar por essa coluna. Clicar em um título de coluna alterna repetidamente entre a ordem de classificação ascendente e descendente.

![Página Índice de alunos](sort-filter-page/_static/paging.png)

Neste tutorial, você:

> [!div class="checklist"]
> * Adicionar links de classificação de coluna
> * Adicionar uma caixa Pesquisa
> * Adicionar paginação ao Índice de Alunos
> * Adicionar paginação ao método Index
> * Adicionar links de paginação
> * Criar uma página Sobre

## <a name="prerequisites"></a>Pré-requisitos

* [Implementar funcionalidade CRUD](crud.md)

## <a name="add-column-sort-links"></a>Adicionar links de classificação de coluna

Para adicionar uma classificação à página Índice de Alunos, você alterará o método `Index` do controlador Alunos e adicionará o código à exibição Índice de Alunos.

### <a name="add-sorting-functionality-to-the-index-method"></a>Adicionar a funcionalidade de classificação ao método Index

Em *StudentsController.cs*, substitua o método `Index` pelo seguinte código:

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly)]

Esse código recebe um parâmetro `sortOrder` da cadeia de caracteres de consulta na URL. O valor de cadeia de caracteres de consulta é fornecido pelo ASP.NET Core MVC como um parâmetro para o método de ação. O parâmetro será uma cadeia de caracteres "Name" ou "Date", opcionalmente, seguido de um sublinhado e a cadeia de caracteres "desc" para especificar a ordem descendente. A ordem de classificação crescente é padrão.

Na primeira vez que a página Índice é solicitada, não há nenhuma cadeia de caracteres de consulta. Os alunos são exibidos em ordem ascendente por sobrenome, que é o padrão, conforme estabelecido pelo caso fall-through na instrução `switch`. Quando o usuário clica em um hiperlink de título de coluna, o valor `sortOrder` apropriado é fornecido na cadeia de caracteres de consulta.

Os dois elementos `ViewData` (NameSortParm e DateSortParm) são usados pela exibição para configurar os hiperlinks de título de coluna com os valores de cadeia de caracteres de consulta apropriados.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly&highlight=3-4)]

Essas são instruções ternárias. A primeira delas especifica que o parâmetro `sortOrder` é nulo ou vazio, NameSortParm deve ser definido como "name_desc"; caso contrário, ele deve ser definido como uma cadeia de caracteres vazia. Essas duas instruções permitem que a exibição defina os hiperlinks de título de coluna da seguinte maneira:

|  Ordem de classificação atual  | Hiperlink do sobrenome | Hiperlink de data |
|:--------------------:|:-------------------:|:--------------:|
| Sobrenome ascendente  | descending          | ascending      |
| Sobrenome descendente | ascending           | ascending      |
| Data ascendente       | ascending           | descending     |
| Data descendente      | ascending           | ascending      |

O método usa o LINQ to Entities para especificar a coluna pela qual classificar. O código cria uma variável `IQueryable` antes da instrução switch, modifica-a na instrução switch e chama o método `ToListAsync` após a instrução `switch`. Quando você cria e modifica variáveis `IQueryable`, nenhuma consulta é enviada para o banco de dados. A consulta não é executada até que você converta o objeto `IQueryable` em uma coleção chamando um método, como `ToListAsync`. Portanto, esse código resulta em uma única consulta que não é executada até a instrução `return View`.

Este código pode ficar detalhado com um grande número de colunas. [O último tutorial desta série](advanced.md#dynamic-linq) mostra como escrever um código que permite que você passe o nome da coluna `OrderBy` em uma variável de cadeia de caracteres.

### <a name="add-column-heading-hyperlinks-to-the-student-index-view"></a>Adicionar hiperlinks de título de coluna à exibição Índice de Alunos

Substitua o código em *Views/Students/Index.cshtml* pelo código a seguir para adicionar hiperlinks de título de coluna. As linhas alteradas são realçadas.

[!code-html[](intro/samples/cu/Views/Students/Index2.cshtml?highlight=16,22)]

Esse código usa as informações nas propriedades `ViewData` para configurar hiperlinks com os valores de cadeia de caracteres de consulta apropriados.

Execute o aplicativo, selecione a guia **Alunos** e, em seguida, clique nos títulos de coluna **Sobrenome** e **Data de Registro** para verificar se a classificação funciona.

![Página Índice de Alunos na ordem do nome](sort-filter-page/_static/name-order.png)

## <a name="add-a-search-box"></a>Adicionar uma caixa Pesquisa

Para adicionar a filtragem à página Índice de Alunos, você adicionará uma caixa de texto e um botão Enviar à exibição e fará alterações correspondentes no método `Index`. A caixa de texto permitirá que você insira uma cadeia de caracteres a ser pesquisada nos campos de nome e sobrenome.

### <a name="add-filtering-functionality-to-the-index-method"></a>Adicionar a funcionalidade de filtragem a método Index

Em *StudentsController.cs*, substitua o método `Index` pelo código a seguir (as alterações são realçadas).

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilter&highlight=1,5,9-13)]

Você adicionou um parâmetro `searchString` ao método `Index`. O valor de cadeia de caracteres de pesquisa é recebido em uma caixa de texto que você adicionará à exibição Índice. Você também adicionou à instrução LINQ uma cláusula Where, que seleciona somente os alunos cujo nome ou sobrenome contém a cadeia de caracteres de pesquisa. A instrução que adiciona a cláusula Where é executada somente se há um valor a ser pesquisado.

> [!NOTE]
> Aqui você está chamando o método `Where` em um objeto `IQueryable`, e o filtro será processado no servidor. Em alguns cenários, você pode chamar o método `Where` como um método de extensão em uma coleção em memória. (Por exemplo, suponha que você altere a referência a `_context.Students`, de modo que em vez de um `DbSet` do EF, ela referencie um método de repositório que retorna uma coleção `IEnumerable`.) O resultado normalmente é o mesmo, mas em alguns casos pode ser diferente.
>
>Por exemplo, a implementação do .NET Framework do método `Contains` executa uma comparação que diferencia maiúsculas de minúsculas por padrão, mas no SQL Server, isso é determinado pela configuração de ordenação da instância do SQL Server. Por padrão, essa configuração diferencia maiúsculas de minúsculas. Você poderia chamar o método `ToUpper` para fazer com que o teste diferencie maiúsculas de minúsculas de forma explícita:  *Where(s => s.LastName.ToUpper().Contains(searchString.ToUpper())*. Isso garantirá que os resultados permaneçam os mesmos se você alterar o código mais tarde para usar um repositório que retorna uma coleção `IEnumerable` em vez de um objeto `IQueryable`. (Quando você chama o método `Contains` em uma coleção `IEnumerable`, obtém a implementação do .NET Framework; quando chama-o em um objeto `IQueryable`, obtém a implementação do provedor de banco de dados.) No entanto, há uma penalidade de desempenho para essa solução. O código `ToUpper` colocará uma função na cláusula WHERE da instrução TSQL SELECT. Isso pode impedir que o otimizador use um índice. Considerando que o SQL geralmente é instalado como não diferenciando maiúsculas e minúsculas, é melhor evitar o código `ToUpper` até você migrar para um armazenamento de dados que diferencia maiúsculas de minúsculas.

### <a name="add-a-search-box-to-the-student-index-view"></a>Adicionar uma Caixa de Pesquisa à exibição Índice de Alunos

Em *Views/Student/Index.cshtml*, adicione o código realçado imediatamente antes da marcação de tabela de abertura para criar uma legenda, uma caixa de texto e um botão **Pesquisar**.

[!code-html[](intro/samples/cu/Views/Students/Index3.cshtml?range=9-23&highlight=5-13)]

Esse código usa o [auxiliar de marcação](xref:mvc/views/tag-helpers/intro) `<form>` para adicionar o botão e a caixa de texto de pesquisa. Por padrão, o auxiliar de marcação `<form>` envia dados de formulário com um POST, o que significa que os parâmetros são passados no corpo da mensagem HTTP e não na URL como cadeias de consulta. Quando você especifica HTTP GET, os dados de formulário são passados na URL como cadeias de consulta, o que permite aos usuários marcar a URL. As diretrizes do W3C recomendam o uso de GET quando a ação não resulta em uma atualização.

Execute o aplicativo, selecione a guia **Alunos**, insira uma cadeia de caracteres de pesquisa e clique em Pesquisar para verificar se a filtragem está funcionando.

![Página Índice de Alunos com filtragem](sort-filter-page/_static/filtering.png)

Observe que a URL contém a cadeia de caracteres de pesquisa.

```html
http://localhost:5813/Students?SearchString=an
```

Se você marcar essa página, obterá a lista filtrada quando usar o indicador. A adição de `method="get"` à marcação `form` é o que fez com que a cadeia de caracteres de consulta fosse gerada.

Neste estágio, se você clicar em um link de classificação de título de coluna perderá o valor de filtro inserido na caixa **Pesquisa**. Você corrigirá isso na próxima seção.

## <a name="add-paging-to-students-index"></a>Adicionar paginação ao Índice de Alunos

Para adicionar a paginação à página Índice de alunos, você criará uma classe `PaginatedList` que usa as instruções `Skip` e `Take` para filtrar os dados no servidor, em vez de recuperar sempre todas as linhas da tabela. Em seguida, você fará outras alterações no método `Index` e adicionará botões de paginação à exibição `Index`. A ilustração a seguir mostra os botões de paginação.

![Página Índice de alunos com links de paginação](sort-filter-page/_static/paging.png)

Na pasta do projeto, crie `PaginatedList.cs` e, em seguida, substitua o código de modelo pelo código a seguir.

[!code-csharp[](intro/samples/cu/PaginatedList.cs)]

O método `CreateAsync` nesse código usa o tamanho da página e o número da página e aplica as instruções `Skip` e `Take` ao `IQueryable`. Quando `ToListAsync` for chamado no `IQueryable`, ele retornará uma Lista que contém somente a página solicitada. As propriedades `HasPreviousPage` e `HasNextPage` podem ser usadas para habilitar ou desabilitar os botões de paginação **Anterior** e **Próximo**.

Um método `CreateAsync` é usado em vez de um construtor para criar o objeto `PaginatedList<T>`, porque os construtores não podem executar um código assíncrono.

## <a name="add-paging-to-index-method"></a>Adicionar paginação ao método Index

Em *StudentsController.cs*, substitua o método `Index` pelo código a seguir.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilterPage&highlight=1-5,7,11-18,45-46)]

Esse código adiciona um parâmetro de número de página, um parâmetro de ordem de classificação atual e um parâmetro de filtro atual à assinatura do método.

```csharp
public async Task<IActionResult> Index(
    string sortOrder,
    string currentFilter,
    string searchString,
    int? pageNumber)
```

Na primeira vez que a página for exibida, ou se o usuário ainda não tiver clicado em um link de paginação ou classificação, todos os parâmetros serão nulos.  Se um link de paginação receber um clique, a variável de página conterá o número da página a ser exibido.

O elemento `ViewData` chamado CurrentSort fornece à exibição a ordem de classificação atual, pois isso precisa ser incluído nos links de paginação para manter a ordem de classificação igual durante a paginação.

O elemento `ViewData` chamado CurrentFilter fornece à exibição a cadeia de caracteres de filtro atual. Esse valor precisa ser incluído nos links de paginação para manter as configurações de filtro durante a paginação e precisa ser restaurado para a caixa de texto quando a página é exibida novamente.

Se a cadeia de caracteres de pesquisa for alterada durante a paginação, a página precisará ser redefinida como 1, porque o novo filtro pode resultar na exibição de dados diferentes. A cadeia de caracteres de pesquisa é alterada quando um valor é inserido na caixa de texto e o botão Enviar é pressionado. Nesse caso, o parâmetro `searchString` não é nulo.

```csharp
if (searchString != null)
{
    pageNumber = 1;
}
else
{
    searchString = currentFilter;
}
```

Ao final do método `Index`, o método `PaginatedList.CreateAsync` converte a consulta de alunos em uma única página de alunos de um tipo de coleção compatível com paginação. A única página de alunos é então passada para a exibição.

```csharp
return View(await PaginatedList<Student>.CreateAsync(students.AsNoTracking(), pageNumber ?? 1, pageSize));
```

O método `PaginatedList.CreateAsync` usa um número de página. Os dois pontos de interrogação representam o operador de união de nulo. O operador de união de nulo define um valor padrão para um tipo que permite valor nulo; a expressão `(pageNumber ?? 1)` significa retornar o valor de `pageNumber` se ele tiver um valor ou retornar 1 se `pageNumber` for nulo.

## <a name="add-paging-links"></a>Adicionar links de paginação

Em *Views/Students/Index.cshtml*, substitua o código existente pelo código a seguir. As alterações são realçadas.

[!code-html[](intro/samples/cu/Views/Students/Index.cshtml?highlight=1,27,30,33,61-79)]

A instrução `@model` na parte superior da página especifica que a exibição agora obtém um objeto `PaginatedList<T>`, em vez de um objeto `List<T>`.

Os links de cabeçalho de coluna usam a cadeia de caracteres de consulta para passar a cadeia de caracteres de pesquisa atual para o controlador, de modo que o usuário possa classificar nos resultados do filtro:

```html
<a asp-action="Index" asp-route-sortOrder="@ViewData["DateSortParm"]" asp-route-currentFilter ="@ViewData["CurrentFilter"]">Enrollment Date</a>
```

Os botões de paginação são exibidos por auxiliares de marcação:

```html
<a asp-action="Index"
   asp-route-sortOrder="@ViewData["CurrentSort"]"
   asp-route-pageNumber="@(Model.PageIndex - 1)"
   asp-route-currentFilter="@ViewData["CurrentFilter"]"
   class="btn btn-default @prevDisabled">
   Previous
</a>
```

Execute o aplicativo e acesse a página Alunos.

![Página Índice de alunos com links de paginação](sort-filter-page/_static/paging.png)

Clique nos links de paginação em ordens de classificação diferentes para verificar se a paginação funciona. Em seguida, insira uma cadeia de caracteres de pesquisa e tente fazer a paginação novamente para verificar se ela também funciona corretamente com a classificação e filtragem.

## <a name="create-an-about-page"></a>Criar uma página Sobre

Para a página **Sobre** do site da Contoso University, você exibirá quantos alunos se registraram para cada data de registro. Isso exige agrupamento e cálculos simples nos grupos. Para fazer isso, você fará o seguinte:

* Criar uma classe de modelo de exibição para os dados que você precisa passar para a exibição.

* Modificar o método About no controlador Home.

* Modificar a exibição Sobre.

### <a name="create-the-view-model"></a>Criar o modelo de exibição

Crie uma pasta *SchoolViewModels* na pasta *Models*.

Na nova pasta, adicione um arquivo de classe *EnrollmentDateGroup.cs* e substitua o código de modelo pelo seguinte código:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="modify-the-home-controller"></a>Modificar o controlador Home

Em *HomeController.cs*, adicione o seguinte usando as instruções na parte superior do arquivo:

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings1)]

Adicione uma variável de classe ao contexto de banco de dados imediatamente após a chave de abertura da classe e obtenha uma instância do contexto da DI do ASP.NET Core:

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_AddContext&highlight=3,5,7)]

Adicione um método `About` com o seguinte código:

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseDbSet)]

A instrução LINQ agrupa as entidades de alunos por data de registro, calcula o número de entidades em cada grupo e armazena os resultados em uma coleção de objetos de modelo de exibição `EnrollmentDateGroup`.
> [!NOTE]
> Na versão 1.0 do Entity Framework Core, todo o conjunto de resultados é retornado para o cliente e o agrupamento é feito no cliente. Em alguns cenários, isso pode criar problemas de desempenho. Teste o desempenho com volumes de dados de produção e, se necessário, use o SQL bruto para fazer o agrupamento no servidor. Para obter informações sobre como usar o SQL bruto, veja [o último tutorial desta série](advanced.md).

### <a name="modify-the-about-view"></a>Modificar a exibição Sobre

Adicione um arquivo *Views/Home/About.cshtml* com o seguinte código:

[!code-html[](intro/samples/cu/Views/Home/About.cshtml)]

Execute o aplicativo e acesse a página Sobre. A contagem de alunos para cada data de registro é exibida em uma tabela.

## <a name="get-the-code"></a>Obter o código

[Baixe ou exiba o aplicativo concluído.](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você:

> [!div class="checklist"]
> * Adicionou links de classificação de coluna
> * Adicionou uma caixa Pesquisa
> * Adicionou paginação ao Índice de Alunos
> * Adicionou paginação ao método Index
> * Adicionou links de paginação
> * Criou uma página Sobre

Vá para o próximo tutorial para aprender a manipular as alterações do modelo de dados usando migrações.

> [!div class="nextstepaction"]
> [Avançar: Manipular mudanças no modelo de dados](migrations.md)
