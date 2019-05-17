---
title: 'Tutorial: Adicionar classificação, filtragem e paginação - ASP.NET Core MVC com EF Core'
description: Neste tutorial você adicionará as funcionalidades de classificação, filtragem e paginação à página Índice de Alunos. Você também criará uma página que faz um agrupamento simples.
author: rick-anderson
ms.author: tdykstra
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/sort-filter-page
ms.openlocfilehash: 921e27bf56587813f835357c9090c91a155c087b
ms.sourcegitcommit: b508b115107e0f8d7f62b25cfcc8ad45e1373459
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65212554"
---
# <a name="tutorial-add-sorting-filtering-and-paging---aspnet-mvc-with-ef-core"></a><span data-ttu-id="f5267-104">Tutorial: Adicionar classificação, filtragem e paginação - ASP.NET Core MVC com EF Core</span><span class="sxs-lookup"><span data-stu-id="f5267-104">Tutorial: Add sorting, filtering, and paging - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="f5267-105">No tutorial anterior, você implementou um conjunto de páginas da Web para operações CRUD básicas para entidades Student.</span><span class="sxs-lookup"><span data-stu-id="f5267-105">In the previous tutorial, you implemented a set of web pages for basic CRUD operations for Student entities.</span></span> <span data-ttu-id="f5267-106">Neste tutorial você adicionará as funcionalidades de classificação, filtragem e paginação à página Índice de Alunos.</span><span class="sxs-lookup"><span data-stu-id="f5267-106">In this tutorial you'll add sorting, filtering, and paging functionality to the Students Index page.</span></span> <span data-ttu-id="f5267-107">Você também criará uma página que faz um agrupamento simples.</span><span class="sxs-lookup"><span data-stu-id="f5267-107">You'll also create a page that does simple grouping.</span></span>

<span data-ttu-id="f5267-108">A ilustração a seguir mostra a aparência da página quando você terminar.</span><span class="sxs-lookup"><span data-stu-id="f5267-108">The following illustration shows what the page will look like when you're done.</span></span> <span data-ttu-id="f5267-109">Os títulos de coluna são links que o usuário pode clicar para classificar por essa coluna.</span><span class="sxs-lookup"><span data-stu-id="f5267-109">The column headings are links that the user can click to sort by that column.</span></span> <span data-ttu-id="f5267-110">Clicar em um título de coluna alterna repetidamente entre a ordem de classificação ascendente e descendente.</span><span class="sxs-lookup"><span data-stu-id="f5267-110">Clicking a column heading repeatedly toggles between ascending and descending sort order.</span></span>

![Página Índice de alunos](sort-filter-page/_static/paging.png)

<span data-ttu-id="f5267-112">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="f5267-112">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f5267-113">Adicionar links de classificação de coluna</span><span class="sxs-lookup"><span data-stu-id="f5267-113">Add column sort links</span></span>
> * <span data-ttu-id="f5267-114">Adicionar uma caixa Pesquisa</span><span class="sxs-lookup"><span data-stu-id="f5267-114">Add a Search box</span></span>
> * <span data-ttu-id="f5267-115">Adicionar paginação ao Índice de Alunos</span><span class="sxs-lookup"><span data-stu-id="f5267-115">Add paging to Students Index</span></span>
> * <span data-ttu-id="f5267-116">Adicionar paginação ao método Index</span><span class="sxs-lookup"><span data-stu-id="f5267-116">Add paging to Index method</span></span>
> * <span data-ttu-id="f5267-117">Adicionar links de paginação</span><span class="sxs-lookup"><span data-stu-id="f5267-117">Add paging links</span></span>
> * <span data-ttu-id="f5267-118">Criar uma página Sobre</span><span class="sxs-lookup"><span data-stu-id="f5267-118">Create an About page</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f5267-119">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="f5267-119">Prerequisites</span></span>

* [<span data-ttu-id="f5267-120">Implementar funcionalidade CRUD</span><span class="sxs-lookup"><span data-stu-id="f5267-120">Implement CRUD Functionality</span></span>](crud.md)

## <a name="add-column-sort-links"></a><span data-ttu-id="f5267-121">Adicionar links de classificação de coluna</span><span class="sxs-lookup"><span data-stu-id="f5267-121">Add column sort links</span></span>

<span data-ttu-id="f5267-122">Para adicionar uma classificação à página Índice de Alunos, você alterará o método `Index` do controlador Alunos e adicionará o código à exibição Índice de Alunos.</span><span class="sxs-lookup"><span data-stu-id="f5267-122">To add sorting to the Student Index page, you'll change the `Index` method of the Students controller and add code to the Student Index view.</span></span>

### <a name="add-sorting-functionality-to-the-index-method"></a><span data-ttu-id="f5267-123">Adicionar a funcionalidade de classificação ao método Index</span><span class="sxs-lookup"><span data-stu-id="f5267-123">Add sorting Functionality to the Index method</span></span>

<span data-ttu-id="f5267-124">Em *StudentsController.cs*, substitua o método `Index` pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="f5267-124">In *StudentsController.cs*, replace the `Index` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly)]

<span data-ttu-id="f5267-125">Esse código recebe um parâmetro `sortOrder` da cadeia de caracteres de consulta na URL.</span><span class="sxs-lookup"><span data-stu-id="f5267-125">This code receives a `sortOrder` parameter from the query string in the URL.</span></span> <span data-ttu-id="f5267-126">O valor de cadeia de caracteres de consulta é fornecido pelo ASP.NET Core MVC como um parâmetro para o método de ação.</span><span class="sxs-lookup"><span data-stu-id="f5267-126">The query string value is provided by ASP.NET Core MVC as a parameter to the action method.</span></span> <span data-ttu-id="f5267-127">O parâmetro será uma cadeia de caracteres "Name" ou "Date", opcionalmente, seguido de um sublinhado e a cadeia de caracteres "desc" para especificar a ordem descendente.</span><span class="sxs-lookup"><span data-stu-id="f5267-127">The parameter will be a string that's either "Name" or "Date", optionally followed by an underscore and the string "desc" to specify descending order.</span></span> <span data-ttu-id="f5267-128">A ordem de classificação crescente é padrão.</span><span class="sxs-lookup"><span data-stu-id="f5267-128">The default sort order is ascending.</span></span>

<span data-ttu-id="f5267-129">Na primeira vez que a página Índice é solicitada, não há nenhuma cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="f5267-129">The first time the Index page is requested, there's no query string.</span></span> <span data-ttu-id="f5267-130">Os alunos são exibidos em ordem ascendente por sobrenome, que é o padrão, conforme estabelecido pelo caso fall-through na instrução `switch`.</span><span class="sxs-lookup"><span data-stu-id="f5267-130">The students are displayed in ascending order by last name, which is the default as established by the fall-through case in the `switch` statement.</span></span> <span data-ttu-id="f5267-131">Quando o usuário clica em um hiperlink de título de coluna, o valor `sortOrder` apropriado é fornecido na cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="f5267-131">When the user clicks a column heading hyperlink, the appropriate `sortOrder` value is provided in the query string.</span></span>

<span data-ttu-id="f5267-132">Os dois elementos `ViewData` (NameSortParm e DateSortParm) são usados pela exibição para configurar os hiperlinks de título de coluna com os valores de cadeia de caracteres de consulta apropriados.</span><span class="sxs-lookup"><span data-stu-id="f5267-132">The two `ViewData` elements (NameSortParm and DateSortParm) are used by the view to configure the column heading hyperlinks with the appropriate query string values.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortOnly&highlight=3-4)]

<span data-ttu-id="f5267-133">Essas são instruções ternárias.</span><span class="sxs-lookup"><span data-stu-id="f5267-133">These are ternary statements.</span></span> <span data-ttu-id="f5267-134">A primeira delas especifica que o parâmetro `sortOrder` é nulo ou vazio, NameSortParm deve ser definido como "name_desc"; caso contrário, ele deve ser definido como uma cadeia de caracteres vazia.</span><span class="sxs-lookup"><span data-stu-id="f5267-134">The first one specifies that if the `sortOrder` parameter is null or empty, NameSortParm should be set to "name_desc"; otherwise, it should be set to an empty string.</span></span> <span data-ttu-id="f5267-135">Essas duas instruções permitem que a exibição defina os hiperlinks de título de coluna da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="f5267-135">These two statements enable the view to set the column heading hyperlinks as follows:</span></span>

|  <span data-ttu-id="f5267-136">Ordem de classificação atual</span><span class="sxs-lookup"><span data-stu-id="f5267-136">Current sort order</span></span>  | <span data-ttu-id="f5267-137">Hiperlink do sobrenome</span><span class="sxs-lookup"><span data-stu-id="f5267-137">Last Name Hyperlink</span></span> | <span data-ttu-id="f5267-138">Hiperlink de data</span><span class="sxs-lookup"><span data-stu-id="f5267-138">Date Hyperlink</span></span> |
|:--------------------:|:-------------------:|:--------------:|
| <span data-ttu-id="f5267-139">Sobrenome ascendente</span><span class="sxs-lookup"><span data-stu-id="f5267-139">Last Name ascending</span></span>  | <span data-ttu-id="f5267-140">descending</span><span class="sxs-lookup"><span data-stu-id="f5267-140">descending</span></span>          | <span data-ttu-id="f5267-141">ascending</span><span class="sxs-lookup"><span data-stu-id="f5267-141">ascending</span></span>      |
| <span data-ttu-id="f5267-142">Sobrenome descendente</span><span class="sxs-lookup"><span data-stu-id="f5267-142">Last Name descending</span></span> | <span data-ttu-id="f5267-143">ascending</span><span class="sxs-lookup"><span data-stu-id="f5267-143">ascending</span></span>           | <span data-ttu-id="f5267-144">ascending</span><span class="sxs-lookup"><span data-stu-id="f5267-144">ascending</span></span>      |
| <span data-ttu-id="f5267-145">Data ascendente</span><span class="sxs-lookup"><span data-stu-id="f5267-145">Date ascending</span></span>       | <span data-ttu-id="f5267-146">ascending</span><span class="sxs-lookup"><span data-stu-id="f5267-146">ascending</span></span>           | <span data-ttu-id="f5267-147">descending</span><span class="sxs-lookup"><span data-stu-id="f5267-147">descending</span></span>     |
| <span data-ttu-id="f5267-148">Data descendente</span><span class="sxs-lookup"><span data-stu-id="f5267-148">Date descending</span></span>      | <span data-ttu-id="f5267-149">ascending</span><span class="sxs-lookup"><span data-stu-id="f5267-149">ascending</span></span>           | <span data-ttu-id="f5267-150">ascending</span><span class="sxs-lookup"><span data-stu-id="f5267-150">ascending</span></span>      |

<span data-ttu-id="f5267-151">O método usa o LINQ to Entities para especificar a coluna pela qual classificar.</span><span class="sxs-lookup"><span data-stu-id="f5267-151">The method uses LINQ to Entities to specify the column to sort by.</span></span> <span data-ttu-id="f5267-152">O código cria uma variável `IQueryable` antes da instrução switch, modifica-a na instrução switch e chama o método `ToListAsync` após a instrução `switch`.</span><span class="sxs-lookup"><span data-stu-id="f5267-152">The code creates an `IQueryable` variable before the switch statement, modifies it in the switch statement, and calls the `ToListAsync` method after the `switch` statement.</span></span> <span data-ttu-id="f5267-153">Quando você cria e modifica variáveis `IQueryable`, nenhuma consulta é enviada para o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="f5267-153">When you create and modify `IQueryable` variables, no query is sent to the database.</span></span> <span data-ttu-id="f5267-154">A consulta não é executada até que você converta o objeto `IQueryable` em uma coleção chamando um método, como `ToListAsync`.</span><span class="sxs-lookup"><span data-stu-id="f5267-154">The query isn't executed until you convert the `IQueryable` object into a collection by calling a method such as `ToListAsync`.</span></span> <span data-ttu-id="f5267-155">Portanto, esse código resulta em uma única consulta que não é executada até a instrução `return View`.</span><span class="sxs-lookup"><span data-stu-id="f5267-155">Therefore, this code results in a single query that's not executed until the `return View` statement.</span></span>

<span data-ttu-id="f5267-156">Este código pode ficar detalhado com um grande número de colunas.</span><span class="sxs-lookup"><span data-stu-id="f5267-156">This code could get verbose with a large number of columns.</span></span> <span data-ttu-id="f5267-157">[O último tutorial desta série](advanced.md#dynamic-linq) mostra como escrever um código que permite que você passe o nome da coluna `OrderBy` em uma variável de cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="f5267-157">[The last tutorial in this series](advanced.md#dynamic-linq) shows how to write code that lets you pass the name of the `OrderBy` column in a string variable.</span></span>

### <a name="add-column-heading-hyperlinks-to-the-student-index-view"></a><span data-ttu-id="f5267-158">Adicionar hiperlinks de título de coluna à exibição Índice de Alunos</span><span class="sxs-lookup"><span data-stu-id="f5267-158">Add column heading hyperlinks to the Student Index view</span></span>

<span data-ttu-id="f5267-159">Substitua o código em *Views/Students/Index.cshtml* pelo código a seguir para adicionar hiperlinks de título de coluna.</span><span class="sxs-lookup"><span data-stu-id="f5267-159">Replace the code in *Views/Students/Index.cshtml*, with the following code to add column heading hyperlinks.</span></span> <span data-ttu-id="f5267-160">As linhas alteradas são realçadas.</span><span class="sxs-lookup"><span data-stu-id="f5267-160">The changed lines are highlighted.</span></span>

[!code-html[](intro/samples/cu/Views/Students/Index2.cshtml?highlight=16,22)]

<span data-ttu-id="f5267-161">Esse código usa as informações nas propriedades `ViewData` para configurar hiperlinks com os valores de cadeia de caracteres de consulta apropriados.</span><span class="sxs-lookup"><span data-stu-id="f5267-161">This code uses the information in `ViewData` properties to set up hyperlinks with the appropriate query string values.</span></span>

<span data-ttu-id="f5267-162">Execute o aplicativo, selecione a guia **Alunos** e, em seguida, clique nos títulos de coluna **Sobrenome** e **Data de Registro** para verificar se a classificação funciona.</span><span class="sxs-lookup"><span data-stu-id="f5267-162">Run the app, select the **Students** tab, and click the **Last Name** and **Enrollment Date** column headings to verify that sorting works.</span></span>

![Página Índice de Alunos na ordem do nome](sort-filter-page/_static/name-order.png)

## <a name="add-a-search-box"></a><span data-ttu-id="f5267-164">Adicionar uma caixa Pesquisa</span><span class="sxs-lookup"><span data-stu-id="f5267-164">Add a Search box</span></span>

<span data-ttu-id="f5267-165">Para adicionar a filtragem à página Índice de Alunos, você adicionará uma caixa de texto e um botão Enviar à exibição e fará alterações correspondentes no método `Index`.</span><span class="sxs-lookup"><span data-stu-id="f5267-165">To add filtering to the Students Index page, you'll add a text box and a submit button to the view and make corresponding changes in the `Index` method.</span></span> <span data-ttu-id="f5267-166">A caixa de texto permitirá que você insira uma cadeia de caracteres a ser pesquisada nos campos de nome e sobrenome.</span><span class="sxs-lookup"><span data-stu-id="f5267-166">The text box will let you enter a string to search for in the first name and last name fields.</span></span>

### <a name="add-filtering-functionality-to-the-index-method"></a><span data-ttu-id="f5267-167">Adicionar a funcionalidade de filtragem a método Index</span><span class="sxs-lookup"><span data-stu-id="f5267-167">Add filtering functionality to the Index method</span></span>

<span data-ttu-id="f5267-168">Em *StudentsController.cs*, substitua o método `Index` pelo código a seguir (as alterações são realçadas).</span><span class="sxs-lookup"><span data-stu-id="f5267-168">In *StudentsController.cs*, replace the `Index` method with the following code (the changes are highlighted).</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilter&highlight=1,5,9-13)]

<span data-ttu-id="f5267-169">Você adicionou um parâmetro `searchString` ao método `Index`.</span><span class="sxs-lookup"><span data-stu-id="f5267-169">You've added a `searchString` parameter to the `Index` method.</span></span> <span data-ttu-id="f5267-170">O valor de cadeia de caracteres de pesquisa é recebido em uma caixa de texto que você adicionará à exibição Índice.</span><span class="sxs-lookup"><span data-stu-id="f5267-170">The search string value is received from a text box that you'll add to the Index view.</span></span> <span data-ttu-id="f5267-171">Você também adicionou à instrução LINQ uma cláusula Where, que seleciona somente os alunos cujo nome ou sobrenome contém a cadeia de caracteres de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="f5267-171">You've also added to the LINQ statement a where clause that selects only students whose first name or last name contains the search string.</span></span> <span data-ttu-id="f5267-172">A instrução que adiciona a cláusula Where é executada somente se há um valor a ser pesquisado.</span><span class="sxs-lookup"><span data-stu-id="f5267-172">The statement that adds the where clause is executed only if there's a value to search for.</span></span>

> [!NOTE]
> <span data-ttu-id="f5267-173">Aqui você está chamando o método `Where` em um objeto `IQueryable`, e o filtro será processado no servidor.</span><span class="sxs-lookup"><span data-stu-id="f5267-173">Here you are calling the `Where` method on an `IQueryable` object, and the filter will be processed on the server.</span></span> <span data-ttu-id="f5267-174">Em alguns cenários, você pode chamar o método `Where` como um método de extensão em uma coleção em memória.</span><span class="sxs-lookup"><span data-stu-id="f5267-174">In some scenarios you might be calling the `Where` method as an extension method on an in-memory collection.</span></span> <span data-ttu-id="f5267-175">(Por exemplo, suponha que você altere a referência a `_context.Students`, de modo que em vez de um `DbSet` do EF, ela referencie um método de repositório que retorna uma coleção `IEnumerable`.) O resultado normalmente é o mesmo, mas em alguns casos pode ser diferente.</span><span class="sxs-lookup"><span data-stu-id="f5267-175">(For example, suppose you change the reference to `_context.Students` so that instead of an EF `DbSet` it references a repository method that returns an `IEnumerable` collection.) The result would normally be the same but in some cases may be different.</span></span>
>
><span data-ttu-id="f5267-176">Por exemplo, a implementação do .NET Framework do método `Contains` executa uma comparação que diferencia maiúsculas de minúsculas por padrão, mas no SQL Server, isso é determinado pela configuração de ordenação da instância do SQL Server.</span><span class="sxs-lookup"><span data-stu-id="f5267-176">For example, the .NET Framework implementation of the `Contains` method performs a case-sensitive comparison by default, but in SQL Server this is determined by the collation setting of the SQL Server instance.</span></span> <span data-ttu-id="f5267-177">Por padrão, essa configuração diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="f5267-177">That setting defaults to case-insensitive.</span></span> <span data-ttu-id="f5267-178">Você poderia chamar o método `ToUpper` para fazer com que o teste diferencie maiúsculas de minúsculas de forma explícita:  *Where(s => s.LastName.ToUpper().Contains(searchString.ToUpper())*.</span><span class="sxs-lookup"><span data-stu-id="f5267-178">You could call the `ToUpper` method to make the test explicitly case-insensitive:  *Where(s => s.LastName.ToUpper().Contains(searchString.ToUpper())*.</span></span> <span data-ttu-id="f5267-179">Isso garantirá que os resultados permaneçam os mesmos se você alterar o código mais tarde para usar um repositório que retorna uma coleção `IEnumerable` em vez de um objeto `IQueryable`.</span><span class="sxs-lookup"><span data-stu-id="f5267-179">That would ensure that results stay the same if you change the code later to use a repository which returns   an `IEnumerable` collection instead of an `IQueryable` object.</span></span> <span data-ttu-id="f5267-180">(Quando você chama o método `Contains` em uma coleção `IEnumerable`, obtém a implementação do .NET Framework; quando chama-o em um objeto `IQueryable`, obtém a implementação do provedor de banco de dados.) No entanto, há uma penalidade de desempenho para essa solução.</span><span class="sxs-lookup"><span data-stu-id="f5267-180">(When you call the `Contains` method on an `IEnumerable` collection, you get the .NET Framework implementation; when you call it on an `IQueryable` object, you get the database provider implementation.) However, there's a performance penalty for this solution.</span></span> <span data-ttu-id="f5267-181">O código `ToUpper` colocará uma função na cláusula WHERE da instrução TSQL SELECT.</span><span class="sxs-lookup"><span data-stu-id="f5267-181">The `ToUpper` code would put a function in the WHERE clause of the TSQL SELECT statement.</span></span> <span data-ttu-id="f5267-182">Isso pode impedir que o otimizador use um índice.</span><span class="sxs-lookup"><span data-stu-id="f5267-182">That would prevent the optimizer from using an index.</span></span> <span data-ttu-id="f5267-183">Considerando que o SQL geralmente é instalado como não diferenciando maiúsculas e minúsculas, é melhor evitar o código `ToUpper` até você migrar para um armazenamento de dados que diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="f5267-183">Given that SQL is mostly installed as case-insensitive, it's best to avoid the `ToUpper` code until you migrate to a case-sensitive data store.</span></span>

### <a name="add-a-search-box-to-the-student-index-view"></a><span data-ttu-id="f5267-184">Adicionar uma Caixa de Pesquisa à exibição Índice de Alunos</span><span class="sxs-lookup"><span data-stu-id="f5267-184">Add a Search Box to the Student Index View</span></span>

<span data-ttu-id="f5267-185">Em *Views/Student/Index.cshtml*, adicione o código realçado imediatamente antes da marcação de tabela de abertura para criar uma legenda, uma caixa de texto e um botão **Pesquisar**.</span><span class="sxs-lookup"><span data-stu-id="f5267-185">In *Views/Student/Index.cshtml*, add the highlighted code immediately before the opening table tag in order to create a caption, a text box, and a **Search** button.</span></span>

[!code-html[](intro/samples/cu/Views/Students/Index3.cshtml?range=9-23&highlight=5-13)]

<span data-ttu-id="f5267-186">Esse código usa o [auxiliar de marcação](xref:mvc/views/tag-helpers/intro) `<form>` para adicionar o botão e a caixa de texto de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="f5267-186">This code uses the `<form>` [tag helper](xref:mvc/views/tag-helpers/intro) to add the search text box and button.</span></span> <span data-ttu-id="f5267-187">Por padrão, o auxiliar de marcação `<form>` envia dados de formulário com um POST, o que significa que os parâmetros são passados no corpo da mensagem HTTP e não na URL como cadeias de consulta.</span><span class="sxs-lookup"><span data-stu-id="f5267-187">By default, the `<form>` tag helper submits form data with a POST, which means that parameters are passed in the HTTP message body and not in the URL as query strings.</span></span> <span data-ttu-id="f5267-188">Quando você especifica HTTP GET, os dados de formulário são passados na URL como cadeias de consulta, o que permite aos usuários marcar a URL.</span><span class="sxs-lookup"><span data-stu-id="f5267-188">When you specify HTTP GET, the form data is passed in the URL as query strings, which enables users to bookmark the URL.</span></span> <span data-ttu-id="f5267-189">As diretrizes do W3C recomendam o uso de GET quando a ação não resulta em uma atualização.</span><span class="sxs-lookup"><span data-stu-id="f5267-189">The W3C guidelines recommend that you should use GET when the action doesn't result in an update.</span></span>

<span data-ttu-id="f5267-190">Execute o aplicativo, selecione a guia **Alunos**, insira uma cadeia de caracteres de pesquisa e clique em Pesquisar para verificar se a filtragem está funcionando.</span><span class="sxs-lookup"><span data-stu-id="f5267-190">Run the app, select the **Students** tab, enter a search string, and click Search to verify that filtering is working.</span></span>

![Página Índice de Alunos com filtragem](sort-filter-page/_static/filtering.png)

<span data-ttu-id="f5267-192">Observe que a URL contém a cadeia de caracteres de pesquisa.</span><span class="sxs-lookup"><span data-stu-id="f5267-192">Notice that the URL contains the search string.</span></span>

```html
http://localhost:5813/Students?SearchString=an
```

<span data-ttu-id="f5267-193">Se você marcar essa página, obterá a lista filtrada quando usar o indicador.</span><span class="sxs-lookup"><span data-stu-id="f5267-193">If you bookmark this page, you'll get the filtered list when you use the bookmark.</span></span> <span data-ttu-id="f5267-194">A adição de `method="get"` à marcação `form` é o que fez com que a cadeia de caracteres de consulta fosse gerada.</span><span class="sxs-lookup"><span data-stu-id="f5267-194">Adding `method="get"` to the `form` tag is what caused the query string to be generated.</span></span>

<span data-ttu-id="f5267-195">Neste estágio, se você clicar em um link de classificação de título de coluna perderá o valor de filtro inserido na caixa **Pesquisa**.</span><span class="sxs-lookup"><span data-stu-id="f5267-195">At this stage, if you click a column heading sort link you'll lose the filter value that you entered in the **Search** box.</span></span> <span data-ttu-id="f5267-196">Você corrigirá isso na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="f5267-196">You'll fix that in the next section.</span></span>

## <a name="add-paging-to-students-index"></a><span data-ttu-id="f5267-197">Adicionar paginação ao Índice de Alunos</span><span class="sxs-lookup"><span data-stu-id="f5267-197">Add paging to Students Index</span></span>

<span data-ttu-id="f5267-198">Para adicionar a paginação à página Índice de alunos, você criará uma classe `PaginatedList` que usa as instruções `Skip` e `Take` para filtrar os dados no servidor, em vez de recuperar sempre todas as linhas da tabela.</span><span class="sxs-lookup"><span data-stu-id="f5267-198">To add paging to the Students Index page, you'll create a `PaginatedList` class that uses `Skip` and `Take` statements to filter data on the server instead of always retrieving all rows of the table.</span></span> <span data-ttu-id="f5267-199">Em seguida, você fará outras alterações no método `Index` e adicionará botões de paginação à exibição `Index`.</span><span class="sxs-lookup"><span data-stu-id="f5267-199">Then you'll make additional changes in the `Index` method and add paging buttons to the `Index` view.</span></span> <span data-ttu-id="f5267-200">A ilustração a seguir mostra os botões de paginação.</span><span class="sxs-lookup"><span data-stu-id="f5267-200">The following illustration shows the paging buttons.</span></span>

![Página Índice de alunos com links de paginação](sort-filter-page/_static/paging.png)

<span data-ttu-id="f5267-202">Na pasta do projeto, crie `PaginatedList.cs` e, em seguida, substitua o código de modelo pelo código a seguir.</span><span class="sxs-lookup"><span data-stu-id="f5267-202">In the project folder, create `PaginatedList.cs`, and then replace the template code with the following code.</span></span>

[!code-csharp[](intro/samples/cu/PaginatedList.cs)]

<span data-ttu-id="f5267-203">O método `CreateAsync` nesse código usa o tamanho da página e o número da página e aplica as instruções `Skip` e `Take` ao `IQueryable`.</span><span class="sxs-lookup"><span data-stu-id="f5267-203">The `CreateAsync` method in this code takes page size and page number and applies the appropriate `Skip` and `Take` statements to the `IQueryable`.</span></span> <span data-ttu-id="f5267-204">Quando `ToListAsync` for chamado no `IQueryable`, ele retornará uma Lista que contém somente a página solicitada.</span><span class="sxs-lookup"><span data-stu-id="f5267-204">When `ToListAsync` is called on the `IQueryable`, it will return a List containing only the requested page.</span></span> <span data-ttu-id="f5267-205">As propriedades `HasPreviousPage` e `HasNextPage` podem ser usadas para habilitar ou desabilitar os botões de paginação **Anterior** e **Próximo**.</span><span class="sxs-lookup"><span data-stu-id="f5267-205">The properties `HasPreviousPage` and `HasNextPage` can be used to enable or disable **Previous** and **Next** paging buttons.</span></span>

<span data-ttu-id="f5267-206">Um método `CreateAsync` é usado em vez de um construtor para criar o objeto `PaginatedList<T>`, porque os construtores não podem executar um código assíncrono.</span><span class="sxs-lookup"><span data-stu-id="f5267-206">A `CreateAsync` method is used instead of a constructor to create the `PaginatedList<T>` object because constructors can't run asynchronous code.</span></span>

## <a name="add-paging-to-index-method"></a><span data-ttu-id="f5267-207">Adicionar paginação ao método Index</span><span class="sxs-lookup"><span data-stu-id="f5267-207">Add paging to Index method</span></span>

<span data-ttu-id="f5267-208">Em *StudentsController.cs*, substitua o método `Index` pelo código a seguir.</span><span class="sxs-lookup"><span data-stu-id="f5267-208">In *StudentsController.cs*, replace the `Index` method with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_SortFilterPage&highlight=1-5,7,11-18,45-46)]

<span data-ttu-id="f5267-209">Esse código adiciona um parâmetro de número de página, um parâmetro de ordem de classificação atual e um parâmetro de filtro atual à assinatura do método.</span><span class="sxs-lookup"><span data-stu-id="f5267-209">This code adds a page number parameter, a current sort order parameter, and a current filter parameter to the method signature.</span></span>

```csharp
public async Task<IActionResult> Index(
    string sortOrder,
    string currentFilter,
    string searchString,
    int? pageNumber)
```

<span data-ttu-id="f5267-210">Na primeira vez que a página for exibida, ou se o usuário ainda não tiver clicado em um link de paginação ou classificação, todos os parâmetros serão nulos.</span><span class="sxs-lookup"><span data-stu-id="f5267-210">The first time the page is displayed, or if the user hasn't clicked a paging or sorting link, all the parameters will be null.</span></span>  <span data-ttu-id="f5267-211">Se um link de paginação receber um clique, a variável de página conterá o número da página a ser exibido.</span><span class="sxs-lookup"><span data-stu-id="f5267-211">If a paging link is clicked, the page variable will contain the page number to display.</span></span>

<span data-ttu-id="f5267-212">O elemento `ViewData` chamado CurrentSort fornece à exibição a ordem de classificação atual, pois isso precisa ser incluído nos links de paginação para manter a ordem de classificação igual durante a paginação.</span><span class="sxs-lookup"><span data-stu-id="f5267-212">The `ViewData` element named CurrentSort provides the view with the current sort order, because this must be included in the paging links in order to keep the sort order the same while paging.</span></span>

<span data-ttu-id="f5267-213">O elemento `ViewData` chamado CurrentFilter fornece à exibição a cadeia de caracteres de filtro atual.</span><span class="sxs-lookup"><span data-stu-id="f5267-213">The `ViewData` element named CurrentFilter provides the view with the current filter string.</span></span> <span data-ttu-id="f5267-214">Esse valor precisa ser incluído nos links de paginação para manter as configurações de filtro durante a paginação e precisa ser restaurado para a caixa de texto quando a página é exibida novamente.</span><span class="sxs-lookup"><span data-stu-id="f5267-214">This value must be included in the paging links in order to maintain the filter settings during paging, and it must be restored to the text box when the page is redisplayed.</span></span>

<span data-ttu-id="f5267-215">Se a cadeia de caracteres de pesquisa for alterada durante a paginação, a página precisará ser redefinida como 1, porque o novo filtro pode resultar na exibição de dados diferentes.</span><span class="sxs-lookup"><span data-stu-id="f5267-215">If the search string is changed during paging, the page has to be reset to 1, because the new filter can result in different data to display.</span></span> <span data-ttu-id="f5267-216">A cadeia de caracteres de pesquisa é alterada quando um valor é inserido na caixa de texto e o botão Enviar é pressionado.</span><span class="sxs-lookup"><span data-stu-id="f5267-216">The search string is changed when a value is entered in the text box and the Submit button is pressed.</span></span> <span data-ttu-id="f5267-217">Nesse caso, o parâmetro `searchString` não é nulo.</span><span class="sxs-lookup"><span data-stu-id="f5267-217">In that case, the `searchString` parameter isn't null.</span></span>

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

<span data-ttu-id="f5267-218">Ao final do método `Index`, o método `PaginatedList.CreateAsync` converte a consulta de alunos em uma única página de alunos de um tipo de coleção compatível com paginação.</span><span class="sxs-lookup"><span data-stu-id="f5267-218">At the end of the `Index` method, the `PaginatedList.CreateAsync` method converts the student query to a single page of students in a collection type that supports paging.</span></span> <span data-ttu-id="f5267-219">A única página de alunos é então passada para a exibição.</span><span class="sxs-lookup"><span data-stu-id="f5267-219">That single page of students is then passed to the view.</span></span>

```csharp
return View(await PaginatedList<Student>.CreateAsync(students.AsNoTracking(), pageNumber ?? 1, pageSize));
```

<span data-ttu-id="f5267-220">O método `PaginatedList.CreateAsync` usa um número de página.</span><span class="sxs-lookup"><span data-stu-id="f5267-220">The `PaginatedList.CreateAsync` method takes a page number.</span></span> <span data-ttu-id="f5267-221">Os dois pontos de interrogação representam o operador de união de nulo.</span><span class="sxs-lookup"><span data-stu-id="f5267-221">The two question marks represent the null-coalescing operator.</span></span> <span data-ttu-id="f5267-222">O operador de união de nulo define um valor padrão para um tipo que permite valor nulo; a expressão `(pageNumber ?? 1)` significa retornar o valor de `pageNumber` se ele tiver um valor ou retornar 1 se `pageNumber` for nulo.</span><span class="sxs-lookup"><span data-stu-id="f5267-222">The null-coalescing operator defines a default value for a nullable type; the expression `(pageNumber ?? 1)` means return the value of `pageNumber` if it has a value, or return 1 if `pageNumber` is null.</span></span>

## <a name="add-paging-links"></a><span data-ttu-id="f5267-223">Adicionar links de paginação</span><span class="sxs-lookup"><span data-stu-id="f5267-223">Add paging links</span></span>

<span data-ttu-id="f5267-224">Em *Views/Students/Index.cshtml*, substitua o código existente pelo código a seguir.</span><span class="sxs-lookup"><span data-stu-id="f5267-224">In *Views/Students/Index.cshtml*, replace the existing code with the following code.</span></span> <span data-ttu-id="f5267-225">As alterações são realçadas.</span><span class="sxs-lookup"><span data-stu-id="f5267-225">The changes are highlighted.</span></span>

[!code-html[](intro/samples/cu/Views/Students/Index.cshtml?highlight=1,27,30,33,61-79)]

<span data-ttu-id="f5267-226">A instrução `@model` na parte superior da página especifica que a exibição agora obtém um objeto `PaginatedList<T>`, em vez de um objeto `List<T>`.</span><span class="sxs-lookup"><span data-stu-id="f5267-226">The `@model` statement at the top of the page specifies that the view now gets a `PaginatedList<T>` object instead of a `List<T>` object.</span></span>

<span data-ttu-id="f5267-227">Os links de cabeçalho de coluna usam a cadeia de caracteres de consulta para passar a cadeia de caracteres de pesquisa atual para o controlador, de modo que o usuário possa classificar nos resultados do filtro:</span><span class="sxs-lookup"><span data-stu-id="f5267-227">The column header links use the query string to pass the current search string to the controller so that the user can sort within filter results:</span></span>

```html
<a asp-action="Index" asp-route-sortOrder="@ViewData["DateSortParm"]" asp-route-currentFilter ="@ViewData["CurrentFilter"]">Enrollment Date</a>
```

<span data-ttu-id="f5267-228">Os botões de paginação são exibidos por auxiliares de marcação:</span><span class="sxs-lookup"><span data-stu-id="f5267-228">The paging buttons are displayed by tag helpers:</span></span>

```html
<a asp-action="Index"
   asp-route-sortOrder="@ViewData["CurrentSort"]"
   asp-route-pageNumber="@(Model.PageIndex - 1)"
   asp-route-currentFilter="@ViewData["CurrentFilter"]"
   class="btn btn-default @prevDisabled">
   Previous
</a>
```

<span data-ttu-id="f5267-229">Execute o aplicativo e acesse a página Alunos.</span><span class="sxs-lookup"><span data-stu-id="f5267-229">Run the app and go to the Students page.</span></span>

![Página Índice de alunos com links de paginação](sort-filter-page/_static/paging.png)

<span data-ttu-id="f5267-231">Clique nos links de paginação em ordens de classificação diferentes para verificar se a paginação funciona.</span><span class="sxs-lookup"><span data-stu-id="f5267-231">Click the paging links in different sort orders to make sure paging works.</span></span> <span data-ttu-id="f5267-232">Em seguida, insira uma cadeia de caracteres de pesquisa e tente fazer a paginação novamente para verificar se ela também funciona corretamente com a classificação e filtragem.</span><span class="sxs-lookup"><span data-stu-id="f5267-232">Then enter a search string and try paging again to verify that paging also works correctly with sorting and filtering.</span></span>

## <a name="create-an-about-page"></a><span data-ttu-id="f5267-233">Criar uma página Sobre</span><span class="sxs-lookup"><span data-stu-id="f5267-233">Create an About page</span></span>

<span data-ttu-id="f5267-234">Para a página **Sobre** do site da Contoso University, você exibirá quantos alunos se registraram para cada data de registro.</span><span class="sxs-lookup"><span data-stu-id="f5267-234">For the Contoso University website's **About** page, you'll display how many students have enrolled for each enrollment date.</span></span> <span data-ttu-id="f5267-235">Isso exige agrupamento e cálculos simples nos grupos.</span><span class="sxs-lookup"><span data-stu-id="f5267-235">This requires grouping and simple calculations on the groups.</span></span> <span data-ttu-id="f5267-236">Para fazer isso, você fará o seguinte:</span><span class="sxs-lookup"><span data-stu-id="f5267-236">To accomplish this, you'll do the following:</span></span>

* <span data-ttu-id="f5267-237">Criar uma classe de modelo de exibição para os dados que você precisa passar para a exibição.</span><span class="sxs-lookup"><span data-stu-id="f5267-237">Create a view model class for the data that you need to pass to the view.</span></span>
* <span data-ttu-id="f5267-238">Criar o método Sobre no controlador Início.</span><span class="sxs-lookup"><span data-stu-id="f5267-238">Create the About method in the Home controller.</span></span>
* <span data-ttu-id="f5267-239">Criar a exibição Sobre.</span><span class="sxs-lookup"><span data-stu-id="f5267-239">Create the About view.</span></span>

### <a name="create-the-view-model"></a><span data-ttu-id="f5267-240">Criar o modelo de exibição</span><span class="sxs-lookup"><span data-stu-id="f5267-240">Create the view model</span></span>

<span data-ttu-id="f5267-241">Crie uma pasta *SchoolViewModels* na pasta *Models*.</span><span class="sxs-lookup"><span data-stu-id="f5267-241">Create a *SchoolViewModels* folder in the *Models* folder.</span></span>

<span data-ttu-id="f5267-242">Na nova pasta, adicione um arquivo de classe *EnrollmentDateGroup.cs* e substitua o código de modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="f5267-242">In the new folder, add a class file *EnrollmentDateGroup.cs* and replace the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/EnrollmentDateGroup.cs)]

### <a name="modify-the-home-controller"></a><span data-ttu-id="f5267-243">Modificar o controlador Home</span><span class="sxs-lookup"><span data-stu-id="f5267-243">Modify the Home Controller</span></span>

<span data-ttu-id="f5267-244">Em *HomeController.cs*, adicione o seguinte usando as instruções na parte superior do arquivo:</span><span class="sxs-lookup"><span data-stu-id="f5267-244">In *HomeController.cs*, add the following using statements at the top of the file:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings1)]

<span data-ttu-id="f5267-245">Adicione uma variável de classe ao contexto de banco de dados imediatamente após a chave de abertura da classe e obtenha uma instância do contexto da DI do ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="f5267-245">Add a class variable for the database context immediately after the opening curly brace for the class, and get an instance of the context from ASP.NET Core DI:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_AddContext&highlight=3,5,7)]

<span data-ttu-id="f5267-246">Adicione um método `About` com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="f5267-246">Add an `About` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseDbSet)]

<span data-ttu-id="f5267-247">A instrução LINQ agrupa as entidades de alunos por data de registro, calcula o número de entidades em cada grupo e armazena os resultados em uma coleção de objetos de modelo de exibição `EnrollmentDateGroup`.</span><span class="sxs-lookup"><span data-stu-id="f5267-247">The LINQ statement groups the student entities by enrollment date, calculates the number of entities in each group, and stores the results in a collection of `EnrollmentDateGroup` view model objects.</span></span>

### <a name="create-the-about-view"></a><span data-ttu-id="f5267-248">Criar a exibição Sobre</span><span class="sxs-lookup"><span data-stu-id="f5267-248">Create the About View</span></span>

<span data-ttu-id="f5267-249">Adicione um arquivo *Views/Home/About.cshtml* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="f5267-249">Add a *Views/Home/About.cshtml* file with the following code:</span></span>

[!code-html[](intro/samples/cu/Views/Home/About.cshtml)]

<span data-ttu-id="f5267-250">Execute o aplicativo e acesse a página Sobre.</span><span class="sxs-lookup"><span data-stu-id="f5267-250">Run the app and go to the About page.</span></span> <span data-ttu-id="f5267-251">A contagem de alunos para cada data de registro é exibida em uma tabela.</span><span class="sxs-lookup"><span data-stu-id="f5267-251">The count of students for each enrollment date is displayed in a table.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="f5267-252">Obter o código</span><span class="sxs-lookup"><span data-stu-id="f5267-252">Get the code</span></span>

[<span data-ttu-id="f5267-253">Baixe ou exiba o aplicativo concluído.</span><span class="sxs-lookup"><span data-stu-id="f5267-253">Download or view the completed application.</span></span>](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="f5267-254">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="f5267-254">Next steps</span></span>

<span data-ttu-id="f5267-255">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="f5267-255">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f5267-256">Adicionou links de classificação de coluna</span><span class="sxs-lookup"><span data-stu-id="f5267-256">Added column sort links</span></span>
> * <span data-ttu-id="f5267-257">Adicionou uma caixa Pesquisa</span><span class="sxs-lookup"><span data-stu-id="f5267-257">Added a Search box</span></span>
> * <span data-ttu-id="f5267-258">Adicionou paginação ao Índice de Alunos</span><span class="sxs-lookup"><span data-stu-id="f5267-258">Added paging to Students Index</span></span>
> * <span data-ttu-id="f5267-259">Adicionou paginação ao método Index</span><span class="sxs-lookup"><span data-stu-id="f5267-259">Added paging to Index method</span></span>
> * <span data-ttu-id="f5267-260">Adicionou links de paginação</span><span class="sxs-lookup"><span data-stu-id="f5267-260">Added paging links</span></span>
> * <span data-ttu-id="f5267-261">Criou uma página Sobre</span><span class="sxs-lookup"><span data-stu-id="f5267-261">Created an About page</span></span>

<span data-ttu-id="f5267-262">Vá para o próximo tutorial para aprender a manipular as alterações do modelo de dados usando migrações.</span><span class="sxs-lookup"><span data-stu-id="f5267-262">Advance to the next tutorial to learn how to handle data model changes by using migrations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="f5267-263">Avançar: Manipular mudanças no modelo de dados</span><span class="sxs-lookup"><span data-stu-id="f5267-263">Next: Handle data model changes</span></span>](migrations.md)
