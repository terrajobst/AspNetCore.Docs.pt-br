---
title: 'Tutorial: Saiba mais sobre cenários avançados - ASP.NET MVC com EF Core'
description: Este tutorial apresenta tópicos úteis para ir além das noções básicas de desenvolvimento de aplicativos Web ASP.NET Core que usam o Entity Framework Core.
author: rick-anderson
ms.author: tdykstra
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/advanced
ms.openlocfilehash: c6255e2b4fc67c6174bab4458ec82035b1886002
ms.sourcegitcommit: 3e9e1f6d572947e15347e818f769e27dea56b648
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2019
ms.locfileid: "58750630"
---
# <a name="tutorial-learn-about-advanced-scenarios---aspnet-mvc-with-ef-core"></a><span data-ttu-id="97083-103">Tutorial: Saiba mais sobre cenários avançados - ASP.NET MVC com EF Core</span><span class="sxs-lookup"><span data-stu-id="97083-103">Tutorial: Learn about advanced scenarios - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="97083-104">No tutorial anterior, você implementou a herança de tabela por hierarquia.</span><span class="sxs-lookup"><span data-stu-id="97083-104">In the previous tutorial, you implemented table-per-hierarchy inheritance.</span></span> <span data-ttu-id="97083-105">Este tutorial apresenta vários tópicos que são úteis para consideração quando você vai além dos conceitos básicos de desenvolvimento de aplicativos Web ASP.NET Core que usam o Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="97083-105">This tutorial introduces several topics that are useful to be aware of when you go beyond the basics of developing ASP.NET Core web applications that use Entity Framework Core.</span></span>

<span data-ttu-id="97083-106">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="97083-106">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="97083-107">Executar consultas SQL brutas</span><span class="sxs-lookup"><span data-stu-id="97083-107">Perform raw SQL queries</span></span>
> * <span data-ttu-id="97083-108">Chamar uma consulta para retornar entidades</span><span class="sxs-lookup"><span data-stu-id="97083-108">Call a query to return entities</span></span>
> * <span data-ttu-id="97083-109">Chamar uma consulta para outros tipos</span><span class="sxs-lookup"><span data-stu-id="97083-109">Call a query to return other types</span></span>
> * <span data-ttu-id="97083-110">Chamar uma consulta de atualização</span><span class="sxs-lookup"><span data-stu-id="97083-110">Call an update query</span></span>
> * <span data-ttu-id="97083-111">Examinar consultas SQL</span><span class="sxs-lookup"><span data-stu-id="97083-111">Examine SQL queries</span></span>
> * <span data-ttu-id="97083-112">Criar uma camada de abstração</span><span class="sxs-lookup"><span data-stu-id="97083-112">Create an abstraction layer</span></span>
> * <span data-ttu-id="97083-113">Aprender sobre a Detecção automática de alterações</span><span class="sxs-lookup"><span data-stu-id="97083-113">Learn about Automatic change detection</span></span>
> * <span data-ttu-id="97083-114">Aprender sobre o código-fonte e planos de desenvolvimento do EF Core</span><span class="sxs-lookup"><span data-stu-id="97083-114">Learn about EF Core source code and development plans</span></span>
> * <span data-ttu-id="97083-115">Aprender a usar o LINQ dinâmico para simplificar o código</span><span class="sxs-lookup"><span data-stu-id="97083-115">Learn how to use dynamic LINQ to simplify code</span></span>

## <a name="prerequisites"></a><span data-ttu-id="97083-116">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="97083-116">Prerequisites</span></span>

* [<span data-ttu-id="97083-117">Implementar herança</span><span class="sxs-lookup"><span data-stu-id="97083-117">Implement Inheritance</span></span>](inheritance.md)

## <a name="perform-raw-sql-queries"></a><span data-ttu-id="97083-118">Executar consultas SQL brutas</span><span class="sxs-lookup"><span data-stu-id="97083-118">Perform raw SQL queries</span></span>

<span data-ttu-id="97083-119">Uma das vantagens de usar o Entity Framework é que ele evita vincular o código de forma muito próxima a um método específico de armazenamento de dados.</span><span class="sxs-lookup"><span data-stu-id="97083-119">One of the advantages of using the Entity Framework is that it avoids tying your code too closely to a particular method of storing data.</span></span> <span data-ttu-id="97083-120">Ele faz isso pela geração de consultas SQL e comandos para você, que também libera você da necessidade de escrevê-los.</span><span class="sxs-lookup"><span data-stu-id="97083-120">It does this by generating SQL queries and commands for you, which also frees you from having to write them yourself.</span></span> <span data-ttu-id="97083-121">Mas há casos excepcionais em que você precisa executar consultas SQL específicas criadas manualmente.</span><span class="sxs-lookup"><span data-stu-id="97083-121">But there are exceptional scenarios when you need to run specific SQL queries that you have manually created.</span></span> <span data-ttu-id="97083-122">Para esses cenários, a API do Code First do Entity Framework inclui métodos que permitem passar comandos SQL diretamente para o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="97083-122">For these scenarios, the Entity Framework Code First API includes methods that enable you to pass SQL commands directly to the database.</span></span> <span data-ttu-id="97083-123">Você tem as seguintes opções no EF Core 1.0:</span><span class="sxs-lookup"><span data-stu-id="97083-123">You have the following options in EF Core 1.0:</span></span>

* <span data-ttu-id="97083-124">Use o método `DbSet.FromSql` para consultas que retornam tipos de entidade.</span><span class="sxs-lookup"><span data-stu-id="97083-124">Use the `DbSet.FromSql` method for queries that return entity types.</span></span> <span data-ttu-id="97083-125">Os objetos retornados precisam ser do tipo esperado pelo objeto `DbSet` e são controlados automaticamente pelo contexto de banco de dados, a menos que você [desative o controle](crud.md#no-tracking-queries).</span><span class="sxs-lookup"><span data-stu-id="97083-125">The returned objects must be of the type expected by the `DbSet` object, and they're automatically tracked by the database context unless you [turn tracking off](crud.md#no-tracking-queries).</span></span>

* <span data-ttu-id="97083-126">Use o `Database.ExecuteSqlCommand` para comandos que não sejam de consulta.</span><span class="sxs-lookup"><span data-stu-id="97083-126">Use the `Database.ExecuteSqlCommand` for non-query commands.</span></span>

<span data-ttu-id="97083-127">Caso precise executar uma consulta que retorna tipos que não são entidades, use o ADO.NET com a conexão de banco de dados fornecida pelo EF.</span><span class="sxs-lookup"><span data-stu-id="97083-127">If you need to run a query that returns types that aren't entities, you can use ADO.NET with the database connection provided by EF.</span></span> <span data-ttu-id="97083-128">Os dados retornados não são controlados pelo contexto de banco de dados, mesmo se esse método é usado para recuperar tipos de entidade.</span><span class="sxs-lookup"><span data-stu-id="97083-128">The returned data isn't tracked by the database context, even if you use this method to retrieve entity types.</span></span>

<span data-ttu-id="97083-129">Como é sempre verdadeiro quando você executa comandos SQL em um aplicativo Web, é necessário tomar precauções para proteger o site contra ataques de injeção de SQL.</span><span class="sxs-lookup"><span data-stu-id="97083-129">As is always true when you execute SQL commands in a web application, you must take precautions to protect your site against SQL injection attacks.</span></span> <span data-ttu-id="97083-130">Uma maneira de fazer isso é usar consultas parametrizadas para garantir que as cadeias de caracteres enviadas por uma página da Web não possam ser interpretadas como comandos SQL.</span><span class="sxs-lookup"><span data-stu-id="97083-130">One way to do that is to use parameterized queries to make sure that strings submitted by a web page can't be interpreted as SQL commands.</span></span> <span data-ttu-id="97083-131">Neste tutorial, você usará consultas parametrizadas ao integrar a entrada do usuário a uma consulta.</span><span class="sxs-lookup"><span data-stu-id="97083-131">In this tutorial you'll use parameterized queries when integrating user input into a query.</span></span>

## <a name="call-a-query-to-return-entities"></a><span data-ttu-id="97083-132">Chamar uma consulta para retornar entidades</span><span class="sxs-lookup"><span data-stu-id="97083-132">Call a query to return entities</span></span>

<span data-ttu-id="97083-133">A classe `DbSet<TEntity>` fornece um método que você pode usar para executar uma consulta que retorna uma entidade do tipo `TEntity`.</span><span class="sxs-lookup"><span data-stu-id="97083-133">The `DbSet<TEntity>` class provides a method that you can use to execute a query that returns an entity of type `TEntity`.</span></span> <span data-ttu-id="97083-134">Para ver como isso funciona, você alterará o código no método `Details` do controlador Departamento.</span><span class="sxs-lookup"><span data-stu-id="97083-134">To see how this works you'll change the code in the `Details` method of the Department controller.</span></span>

<span data-ttu-id="97083-135">Em *DepartmentsController.cs*, no método `Details`, substitua o código que recupera um departamento com uma chamada de método `FromSql`, conforme mostrado no seguinte código realçado:</span><span class="sxs-lookup"><span data-stu-id="97083-135">In *DepartmentsController.cs*, in the `Details` method, replace the code that retrieves a department with a `FromSql` method call, as shown in the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_RawSQL&highlight=8,9,10)]

<span data-ttu-id="97083-136">Para verificar se o novo código funciona corretamente, selecione a guia **Departamentos** e, em seguida, **Detalhes** de um dos departamentos.</span><span class="sxs-lookup"><span data-stu-id="97083-136">To verify that the new code works correctly, select the **Departments** tab and then **Details** for one of the departments.</span></span>

![Detalhes do departamento](advanced/_static/department-details.png)

## <a name="call-a-query-to-return-other-types"></a><span data-ttu-id="97083-138">Chamar uma consulta para outros tipos</span><span class="sxs-lookup"><span data-stu-id="97083-138">Call a query to return other types</span></span>

<span data-ttu-id="97083-139">Anteriormente, você criou uma grade de estatísticas de alunos para a página Sobre que mostrava o número de alunos para cada data de registro.</span><span class="sxs-lookup"><span data-stu-id="97083-139">Earlier you created a student statistics grid for the About page that showed the number of students for each enrollment date.</span></span> <span data-ttu-id="97083-140">Você obteve os dados do conjunto de entidades Students (`_context.Students`) e usou o LINQ para projetar os resultados em uma lista de objetos de modelo de exibição `EnrollmentDateGroup`.</span><span class="sxs-lookup"><span data-stu-id="97083-140">You got the data from the Students entity set (`_context.Students`) and used LINQ to project the results into a list of `EnrollmentDateGroup` view model objects.</span></span> <span data-ttu-id="97083-141">Suponha que você deseje gravar o próprio SQL em vez de usar LINQ.</span><span class="sxs-lookup"><span data-stu-id="97083-141">Suppose you want to write the SQL itself rather than using LINQ.</span></span> <span data-ttu-id="97083-142">Para fazer isso, você precisa executar uma consulta SQL que retorna algo diferente de objetos de entidade.</span><span class="sxs-lookup"><span data-stu-id="97083-142">To do that you need to run a SQL query that returns something other than entity objects.</span></span> <span data-ttu-id="97083-143">No EF Core 1.0, uma maneira de fazer isso é escrever um código ADO.NET e obter a conexão de banco de dados do EF.</span><span class="sxs-lookup"><span data-stu-id="97083-143">In EF Core 1.0, one way to do that is write ADO.NET code and get the database connection from EF.</span></span>

<span data-ttu-id="97083-144">Em *HomeController.cs*, substitua o método `About` pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="97083-144">In *HomeController.cs*, replace the `About` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseRawSQL&highlight=3-32)]

<span data-ttu-id="97083-145">Adicionar uma instrução using:</span><span class="sxs-lookup"><span data-stu-id="97083-145">Add a using statement:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings2)]

<span data-ttu-id="97083-146">Execute o aplicativo e acesse a página Sobre.</span><span class="sxs-lookup"><span data-stu-id="97083-146">Run the app and go to the About page.</span></span> <span data-ttu-id="97083-147">Ela exibe os mesmos dados que antes.</span><span class="sxs-lookup"><span data-stu-id="97083-147">It displays the same data it did before.</span></span>

![Página Sobre](advanced/_static/about.png)

## <a name="call-an-update-query"></a><span data-ttu-id="97083-149">Chamar uma consulta de atualização</span><span class="sxs-lookup"><span data-stu-id="97083-149">Call an update query</span></span>

<span data-ttu-id="97083-150">Suponha que os administradores do Contoso University desejem executar alterações globais no banco de dados, como alterar o número de créditos para cada curso.</span><span class="sxs-lookup"><span data-stu-id="97083-150">Suppose Contoso University administrators want to perform global changes in the database, such as changing the number of credits for every course.</span></span> <span data-ttu-id="97083-151">Se a universidade tiver uma grande quantidade de cursos, poderá ser ineficiente recuperá-los como entidades e alterá-los individualmente.</span><span class="sxs-lookup"><span data-stu-id="97083-151">If the university has a large number of courses, it would be inefficient to retrieve them all as entities and change them individually.</span></span> <span data-ttu-id="97083-152">Nesta seção, você implementará uma página da Web que permite ao usuário especificar um fator pelo qual alterar o número de créditos para todos os cursos e você fará a alteração executando uma instrução SQL UPDATE.</span><span class="sxs-lookup"><span data-stu-id="97083-152">In this section you'll implement a web page that enables the user to specify a factor by which to change the number of credits for all courses, and you'll make the change by executing a SQL UPDATE statement.</span></span> <span data-ttu-id="97083-153">A página da Web será semelhante à seguinte ilustração:</span><span class="sxs-lookup"><span data-stu-id="97083-153">The web page will look like the following illustration:</span></span>

![Página Atualizar Créditos de Curso](advanced/_static/update-credits.png)

<span data-ttu-id="97083-155">Em *CoursesController.cs*, adicione métodos UpdateCourseCredits para HttpGet e HttpPost:</span><span class="sxs-lookup"><span data-stu-id="97083-155">In *CoursesController.cs*, add UpdateCourseCredits methods for HttpGet and HttpPost:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdateGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdatePost)]

<span data-ttu-id="97083-156">Quando o controlador processa uma solicitação HttpGet, nada é retornado em `ViewData["RowsAffected"]`, e a exibição mostra uma caixa de texto vazia e um botão Enviar, conforme mostrado na ilustração anterior.</span><span class="sxs-lookup"><span data-stu-id="97083-156">When the controller processes an HttpGet request, nothing is returned in `ViewData["RowsAffected"]`, and the view displays an empty text box and a submit button, as shown in the preceding illustration.</span></span>

<span data-ttu-id="97083-157">Quando o botão **Atualizar** recebe um clique, o método HttpPost é chamado e multiplicador tem o valor inserido na caixa de texto.</span><span class="sxs-lookup"><span data-stu-id="97083-157">When the **Update** button is clicked, the HttpPost method is called, and multiplier has the value entered in the text box.</span></span> <span data-ttu-id="97083-158">Em seguida, o código executa o SQL que atualiza os cursos e retorna o número de linhas afetadas para a exibição em `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="97083-158">The code then executes the SQL that updates courses and returns the number of affected rows to the view in `ViewData`.</span></span> <span data-ttu-id="97083-159">Quando a exibição obtém um valor `RowsAffected`, ela mostra o número de linhas atualizadas.</span><span class="sxs-lookup"><span data-stu-id="97083-159">When the view gets a `RowsAffected` value, it displays the number of rows updated.</span></span>

<span data-ttu-id="97083-160">No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta *Views/Courses* e, em seguida, clique em **Adicionar > Novo Item**.</span><span class="sxs-lookup"><span data-stu-id="97083-160">In **Solution Explorer**, right-click the *Views/Courses* folder, and then click **Add > New Item**.</span></span>

<span data-ttu-id="97083-161">Na caixa de diálogo **Adicionar Novo Item**, clique em **ASP.NET Core** em **Instalado** no painel esquerdo, clique em **Razor View** e nomeie a nova exibição *UpdateCourseCredits.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="97083-161">In the **Add New Item** dialog, click **ASP.NET Core** under **Installed** in the left pane, click **Razor View**, and name the new view *UpdateCourseCredits.cshtml*.</span></span>

<span data-ttu-id="97083-162">Em *Views/Courses/UpdateCourseCredits.cshtml*, substitua o código de modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="97083-162">In *Views/Courses/UpdateCourseCredits.cshtml*, replace the template code with the following code:</span></span>

[!code-html[](intro/samples/cu/Views/Courses/UpdateCourseCredits.cshtml)]

<span data-ttu-id="97083-163">Execute o método `UpdateCourseCredits` selecionando a guia **Cursos**, adicionando, em seguida, "/UpdateCourseCredits" ao final da URL na barra de endereços do navegador (por exemplo: `http://localhost:5813/Courses/UpdateCourseCredits`).</span><span class="sxs-lookup"><span data-stu-id="97083-163">Run the `UpdateCourseCredits` method by selecting the **Courses** tab, then adding "/UpdateCourseCredits" to the end of the URL in the browser's address bar (for example: `http://localhost:5813/Courses/UpdateCourseCredits`).</span></span> <span data-ttu-id="97083-164">Insira um número na caixa de texto:</span><span class="sxs-lookup"><span data-stu-id="97083-164">Enter a number in the text box:</span></span>

![Página Atualizar Créditos de Curso](advanced/_static/update-credits.png)

<span data-ttu-id="97083-166">Clique em **Atualizar**.</span><span class="sxs-lookup"><span data-stu-id="97083-166">Click **Update**.</span></span> <span data-ttu-id="97083-167">O número de linhas afetadas é exibido:</span><span class="sxs-lookup"><span data-stu-id="97083-167">You see the number of rows affected:</span></span>

![Linhas afetadas na página Atualizar Créditos de Curso](advanced/_static/update-credits-rows-affected.png)

<span data-ttu-id="97083-169">Clique em **Voltar para a Lista** para ver a lista de cursos com o número revisado de créditos.</span><span class="sxs-lookup"><span data-stu-id="97083-169">Click **Back to List** to see the list of courses with the revised number of credits.</span></span>

<span data-ttu-id="97083-170">Observe que o código de produção deve garantir que as atualizações sempre resultem em dados válidos.</span><span class="sxs-lookup"><span data-stu-id="97083-170">Note that production code would ensure that updates always result in valid data.</span></span> <span data-ttu-id="97083-171">O código simplificado mostrado aqui pode multiplicar o número de créditos o suficiente para resultar em números maiores que 5.</span><span class="sxs-lookup"><span data-stu-id="97083-171">The simplified code shown here could multiply the number of credits enough to result in numbers greater than 5.</span></span> <span data-ttu-id="97083-172">(A propriedade `Credits` tem um atributo `[Range(0, 5)]`.) A consulta de atualização funciona, mas os dados inválidos podem causar resultados inesperados em outras partes do sistema que supõem que o número de créditos seja 5 ou inferior.</span><span class="sxs-lookup"><span data-stu-id="97083-172">(The `Credits` property has a `[Range(0, 5)]` attribute.) The update query would work but the invalid data could cause unexpected results in other parts of the system that assume the number of credits is 5 or less.</span></span>

<span data-ttu-id="97083-173">Para obter mais informações sobre consultas SQL brutas, consulte [Consultas SQL brutas](/ef/core/querying/raw-sql).</span><span class="sxs-lookup"><span data-stu-id="97083-173">For more information about raw SQL queries, see [Raw SQL Queries](/ef/core/querying/raw-sql).</span></span>

## <a name="examine-sql-queries"></a><span data-ttu-id="97083-174">Examinar consultas SQL</span><span class="sxs-lookup"><span data-stu-id="97083-174">Examine SQL queries</span></span>

<span data-ttu-id="97083-175">Às vezes, é útil poder ver as consultas SQL reais que são enviadas ao banco de dados.</span><span class="sxs-lookup"><span data-stu-id="97083-175">Sometimes it's helpful to be able to see the actual SQL queries that are sent to the database.</span></span> <span data-ttu-id="97083-176">A funcionalidade de log interno do ASP.NET Core é usada automaticamente pelo EF Core para gravar logs que contêm o SQL de consultas e atualizações.</span><span class="sxs-lookup"><span data-stu-id="97083-176">Built-in logging functionality for ASP.NET Core is automatically used by EF Core to write logs that contain the SQL for queries and updates.</span></span> <span data-ttu-id="97083-177">Nesta seção, você verá alguns exemplos de log do SQL.</span><span class="sxs-lookup"><span data-stu-id="97083-177">In this section you'll see some examples of SQL logging.</span></span>

<span data-ttu-id="97083-178">Abra *StudentsController.cs* e, no método `Details`, defina um ponto de interrupção na instrução `if (student == null)`.</span><span class="sxs-lookup"><span data-stu-id="97083-178">Open *StudentsController.cs* and in the `Details` method set a breakpoint on the `if (student == null)` statement.</span></span>

<span data-ttu-id="97083-179">Execute o aplicativo no modo de depuração e acesse a página Detalhes de um aluno.</span><span class="sxs-lookup"><span data-stu-id="97083-179">Run the app in debug mode, and go to the Details page for a student.</span></span>

<span data-ttu-id="97083-180">Acesse a janela de **Saída** mostrando a saída de depuração e você verá a consulta:</span><span class="sxs-lookup"><span data-stu-id="97083-180">Go to the **Output** window showing debug output, and you see the query:</span></span>

```
Microsoft.EntityFrameworkCore.Database.Command:Information: Executed DbCommand (56ms) [Parameters=[@__id_0='?'], CommandType='Text', CommandTimeout='30']
SELECT TOP(2) [s].[ID], [s].[Discriminator], [s].[FirstName], [s].[LastName], [s].[EnrollmentDate]
FROM [Person] AS [s]
WHERE ([s].[Discriminator] = N'Student') AND ([s].[ID] = @__id_0)
ORDER BY [s].[ID]
Microsoft.EntityFrameworkCore.Database.Command:Information: Executed DbCommand (122ms) [Parameters=[@__id_0='?'], CommandType='Text', CommandTimeout='30']
SELECT [s.Enrollments].[EnrollmentID], [s.Enrollments].[CourseID], [s.Enrollments].[Grade], [s.Enrollments].[StudentID], [e.Course].[CourseID], [e.Course].[Credits], [e.Course].[DepartmentID], [e.Course].[Title]
FROM [Enrollment] AS [s.Enrollments]
INNER JOIN [Course] AS [e.Course] ON [s.Enrollments].[CourseID] = [e.Course].[CourseID]
INNER JOIN (
    SELECT TOP(1) [s0].[ID]
    FROM [Person] AS [s0]
    WHERE ([s0].[Discriminator] = N'Student') AND ([s0].[ID] = @__id_0)
    ORDER BY [s0].[ID]
) AS [t] ON [s.Enrollments].[StudentID] = [t].[ID]
ORDER BY [t].[ID]
```

<span data-ttu-id="97083-181">Você observará algo aqui que pode ser surpreendente: o SQL seleciona até 2 linhas (`TOP(2)`) da tabela Person.</span><span class="sxs-lookup"><span data-stu-id="97083-181">You'll notice something here that might surprise you: the SQL selects up to 2 rows (`TOP(2)`) from the Person table.</span></span> <span data-ttu-id="97083-182">O método `SingleOrDefaultAsync` não é resolvido para uma 1 linha no servidor.</span><span class="sxs-lookup"><span data-stu-id="97083-182">The `SingleOrDefaultAsync` method doesn't resolve to 1 row on the server.</span></span> <span data-ttu-id="97083-183">Eis o porquê:</span><span class="sxs-lookup"><span data-stu-id="97083-183">Here's why:</span></span>

* <span data-ttu-id="97083-184">Se a consulta retorna várias linhas, o método retorna nulo.</span><span class="sxs-lookup"><span data-stu-id="97083-184">If the query would return multiple rows, the method returns null.</span></span>
* <span data-ttu-id="97083-185">Para determinar se a consulta retorna várias linhas, o EF precisa verificar se ela retorna pelo menos 2.</span><span class="sxs-lookup"><span data-stu-id="97083-185">To determine whether the query would return multiple rows, EF has to check if it returns at least 2.</span></span>

<span data-ttu-id="97083-186">Observe que você não precisa usar o modo de depuração e parar em um ponto de interrupção para obter a saída de log na janela de **Saída**.</span><span class="sxs-lookup"><span data-stu-id="97083-186">Note that you don't have to use debug mode and stop at a breakpoint to get logging output in the **Output** window.</span></span> <span data-ttu-id="97083-187">É apenas um modo conveniente de parar o log no ponto em que você deseja examinar a saída.</span><span class="sxs-lookup"><span data-stu-id="97083-187">It's just a convenient way to stop the logging at the point you want to look at the output.</span></span> <span data-ttu-id="97083-188">Se você não fizer isso, o log continuará e você precisará rolar para baixo para localizar as partes de seu interesse.</span><span class="sxs-lookup"><span data-stu-id="97083-188">If you don't do that, logging continues and you have to scroll back to find the parts you're interested in.</span></span>

## <a name="create-an-abstraction-layer"></a><span data-ttu-id="97083-189">Criar uma camada de abstração</span><span class="sxs-lookup"><span data-stu-id="97083-189">Create an abstraction layer</span></span>

<span data-ttu-id="97083-190">Muitos desenvolvedores escrevem um código para implementar padrões de repositório e unidade de trabalho como um wrapper em torno do código que funciona com o Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="97083-190">Many developers write code to implement the repository and unit of work patterns as a wrapper around code that works with the Entity Framework.</span></span> <span data-ttu-id="97083-191">Esses padrões destinam-se a criar uma camada de abstração entre a camada de acesso a dados e a camada da lógica de negócios de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="97083-191">These patterns are intended to create an abstraction layer between the data access layer and the business logic layer of an application.</span></span> <span data-ttu-id="97083-192">A implementação desses padrões pode ajudar a isolar o aplicativo de alterações no armazenamento de dados e pode facilitar o teste de unidade automatizado ou TDD (desenvolvimento orientado por testes).</span><span class="sxs-lookup"><span data-stu-id="97083-192">Implementing these patterns can help insulate your application from changes in the data store and can facilitate automated unit testing or test-driven development (TDD).</span></span> <span data-ttu-id="97083-193">No entanto, escrever um código adicional para implementar esses padrões nem sempre é a melhor escolha para aplicativos que usam o EF, por vários motivos:</span><span class="sxs-lookup"><span data-stu-id="97083-193">However, writing additional code to implement these patterns isn't always the best choice for applications that use EF, for several reasons:</span></span>

* <span data-ttu-id="97083-194">A própria classe de contexto do EF isola o código de código específico a um armazenamento de dados.</span><span class="sxs-lookup"><span data-stu-id="97083-194">The EF context class itself insulates your code from data-store-specific code.</span></span>

* <span data-ttu-id="97083-195">A classe de contexto do EF pode atuar como uma classe de unidade de trabalho para as atualizações de banco de dados feitas com o EF.</span><span class="sxs-lookup"><span data-stu-id="97083-195">The EF context class can act as a unit-of-work class for database updates that you do using EF.</span></span>

* <span data-ttu-id="97083-196">O EF inclui recursos para implementar o TDD sem escrever um código de repositório.</span><span class="sxs-lookup"><span data-stu-id="97083-196">EF includes features for implementing TDD without writing repository code.</span></span>

<span data-ttu-id="97083-197">Para obter informações sobre como implementar os padrões de repositório e unidade de trabalho, consulte [a versão do Entity Framework 5 desta série de tutoriais](/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application).</span><span class="sxs-lookup"><span data-stu-id="97083-197">For information about how to implement the repository and unit of work patterns, see [the Entity Framework 5 version of this tutorial series](/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application).</span></span>

<span data-ttu-id="97083-198">O Entity Framework Core implementa um provedor de banco de dados em memória que pode ser usado para teste.</span><span class="sxs-lookup"><span data-stu-id="97083-198">Entity Framework Core implements an in-memory database provider that can be used for testing.</span></span> <span data-ttu-id="97083-199">Para obter mais informações, confira [Testar com InMemory](/ef/core/miscellaneous/testing/in-memory).</span><span class="sxs-lookup"><span data-stu-id="97083-199">For more information, see [Test with InMemory](/ef/core/miscellaneous/testing/in-memory).</span></span>

## <a name="automatic-change-detection"></a><span data-ttu-id="97083-200">Detecção automática de alterações</span><span class="sxs-lookup"><span data-stu-id="97083-200">Automatic change detection</span></span>

<span data-ttu-id="97083-201">O Entity Framework determina como uma entidade foi alterada (e, portanto, quais atualizações precisam ser enviadas ao banco de dados), comparando os valores atuais de uma entidade com os valores originais.</span><span class="sxs-lookup"><span data-stu-id="97083-201">The Entity Framework determines how an entity has changed (and therefore which updates need to be sent to the database) by comparing the current values of an entity with the original values.</span></span> <span data-ttu-id="97083-202">Os valores originais são armazenados quando a entidade é consultada ou anexada.</span><span class="sxs-lookup"><span data-stu-id="97083-202">The original values are stored when the entity is queried or attached.</span></span> <span data-ttu-id="97083-203">Alguns dos métodos que causam a detecção automática de alterações são os seguintes:</span><span class="sxs-lookup"><span data-stu-id="97083-203">Some of the methods that cause automatic change detection are the following:</span></span>

* <span data-ttu-id="97083-204">DbContext.SaveChanges</span><span class="sxs-lookup"><span data-stu-id="97083-204">DbContext.SaveChanges</span></span>

* <span data-ttu-id="97083-205">DbContext.Entry</span><span class="sxs-lookup"><span data-stu-id="97083-205">DbContext.Entry</span></span>

* <span data-ttu-id="97083-206">ChangeTracker.Entries</span><span class="sxs-lookup"><span data-stu-id="97083-206">ChangeTracker.Entries</span></span>

<span data-ttu-id="97083-207">Se você estiver controlando um grande número de entidades e chamar um desses métodos muitas vezes em um loop, poderá obter melhorias significativas de desempenho desativando temporariamente a detecção automática de alterações usando a propriedade `ChangeTracker.AutoDetectChangesEnabled`.</span><span class="sxs-lookup"><span data-stu-id="97083-207">If you're tracking a large number of entities and you call one of these methods many times in a loop, you might get significant performance improvements by temporarily turning off automatic change detection using the `ChangeTracker.AutoDetectChangesEnabled` property.</span></span> <span data-ttu-id="97083-208">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="97083-208">For example:</span></span>

```csharp
_context.ChangeTracker.AutoDetectChangesEnabled = false;
```

## <a name="ef-core-source-code-and-development-plans"></a><span data-ttu-id="97083-209">Código-fonte e planos de desenvolvimento do EF Core</span><span class="sxs-lookup"><span data-stu-id="97083-209">EF Core source code and development plans</span></span>

<span data-ttu-id="97083-210">O código-fonte do Entity Framework Core está em [https://github.com/aspnet/EntityFrameworkCore](https://github.com/aspnet/EntityFrameworkCore).</span><span class="sxs-lookup"><span data-stu-id="97083-210">The Entity Framework Core source is at [https://github.com/aspnet/EntityFrameworkCore](https://github.com/aspnet/EntityFrameworkCore).</span></span> <span data-ttu-id="97083-211">O repositório do EF Core contém builds noturnos, acompanhamento de questões, especificações de recurso, notas de reuniões de design e [o roteiro para desenvolvimento futuro](https://github.com/aspnet/EntityFrameworkCore/wiki/Roadmap).</span><span class="sxs-lookup"><span data-stu-id="97083-211">The EF Core repository contains nightly builds, issue tracking, feature specs, design meeting notes, and [the roadmap for future development](https://github.com/aspnet/EntityFrameworkCore/wiki/Roadmap).</span></span> <span data-ttu-id="97083-212">Arquive ou encontre bugs e contribua.</span><span class="sxs-lookup"><span data-stu-id="97083-212">You can file or find bugs, and contribute.</span></span>

<span data-ttu-id="97083-213">Embora o código-fonte seja aberto, há suporte completo para o Entity Framework Core como um produto Microsoft.</span><span class="sxs-lookup"><span data-stu-id="97083-213">Although the source code is open, Entity Framework Core is fully supported as a Microsoft product.</span></span> <span data-ttu-id="97083-214">A equipe do Microsoft Entity Framework mantém controle sobre quais contribuições são aceitas e testa todas as alterações de código para garantir a qualidade de cada versão.</span><span class="sxs-lookup"><span data-stu-id="97083-214">The Microsoft Entity Framework team keeps control over which contributions are accepted and tests all code changes to ensure the quality of each release.</span></span>

## <a name="reverse-engineer-from-existing-database"></a><span data-ttu-id="97083-215">Fazer engenharia reversa do banco de dados existente</span><span class="sxs-lookup"><span data-stu-id="97083-215">Reverse engineer from existing database</span></span>

<span data-ttu-id="97083-216">Para fazer engenharia reversa de um modelo de dados, incluindo classes de entidade de um banco de dados existente, use o comando [scaffold-dbcontext](/ef/core/miscellaneous/cli/powershell#scaffold-dbcontext).</span><span class="sxs-lookup"><span data-stu-id="97083-216">To reverse engineer a data model including entity classes from an existing database, use the [scaffold-dbcontext](/ef/core/miscellaneous/cli/powershell#scaffold-dbcontext) command.</span></span> <span data-ttu-id="97083-217">Consulte o [tutorial de introdução](/ef/core/get-started/aspnetcore/existing-db).</span><span class="sxs-lookup"><span data-stu-id="97083-217">See the [getting-started tutorial](/ef/core/get-started/aspnetcore/existing-db).</span></span>

<a id="dynamic-linq"></a>

## <a name="use-dynamic-linq-to-simplify-code"></a><span data-ttu-id="97083-218">Usar o LINQ dinâmico para simplificar o código</span><span class="sxs-lookup"><span data-stu-id="97083-218">Use dynamic LINQ to simplify code</span></span>

<span data-ttu-id="97083-219">O [terceiro tutorial desta série](sort-filter-page.md) mostra como escrever um código LINQ embutindo nomes de colunas em código em uma instrução `switch`.</span><span class="sxs-lookup"><span data-stu-id="97083-219">The [third tutorial in this series](sort-filter-page.md) shows how to write LINQ code by hard-coding column names in a `switch` statement.</span></span> <span data-ttu-id="97083-220">Com duas colunas para escolha, isso funciona bem, mas se você tiver muitas colunas, o código poderá ficar detalhado.</span><span class="sxs-lookup"><span data-stu-id="97083-220">With two columns to choose from, this works fine, but if you have many columns the code could get verbose.</span></span> <span data-ttu-id="97083-221">Para resolver esse problema, use o método `EF.Property` para especificar o nome da propriedade como uma cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="97083-221">To solve that problem, you can use the `EF.Property` method to specify the name of the property as a string.</span></span> <span data-ttu-id="97083-222">Para usar essa abordagem, substitua o método `Index` no `StudentsController` pelo código a seguir.</span><span class="sxs-lookup"><span data-stu-id="97083-222">To try out this approach, replace the `Index` method in the `StudentsController` with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DynamicLinq)]

## <a name="acknowledgments"></a><span data-ttu-id="97083-223">Agradecimentos</span><span class="sxs-lookup"><span data-stu-id="97083-223">Acknowledgments</span></span>

<span data-ttu-id="97083-224">Tom Dykstra e Rick Anderson (twitter @RickAndMSFT) escreveram este tutorial.</span><span class="sxs-lookup"><span data-stu-id="97083-224">Tom Dykstra and Rick Anderson (twitter @RickAndMSFT) wrote this tutorial.</span></span> <span data-ttu-id="97083-225">Rowan Miller, Diego Vega e outros membros da equipe do Entity Framework auxiliaram com revisões de código e ajudaram com problemas de depuração que surgiram durante a codificação para os tutoriais.</span><span class="sxs-lookup"><span data-stu-id="97083-225">Rowan Miller, Diego Vega, and other members of the Entity Framework team assisted with code reviews and helped debug issues that arose while we were writing code for the tutorials.</span></span> <span data-ttu-id="97083-226">John Parente e Paul Goldman trabalharam na atualização do tutorial do ASP.NET Core 2.2.</span><span class="sxs-lookup"><span data-stu-id="97083-226">John Parente and Paul Goldman worked on updating the tutorial for ASP.NET Core 2.2.</span></span>

<a id="common-errors"></a>

## <a name="troubleshoot-common-errors"></a><span data-ttu-id="97083-227">Solucionar erros comuns</span><span class="sxs-lookup"><span data-stu-id="97083-227">Troubleshoot common errors</span></span>

### <a name="contosouniversitydll-used-by-another-process"></a><span data-ttu-id="97083-228">ContosoUniversity.dll usada por outro processo</span><span class="sxs-lookup"><span data-stu-id="97083-228">ContosoUniversity.dll used by another process</span></span>

<span data-ttu-id="97083-229">Mensagem de erro:</span><span class="sxs-lookup"><span data-stu-id="97083-229">Error message:</span></span>

> <span data-ttu-id="97083-230">Não é possível abrir '...bin\Debug\netcoreapp1.0\ContosoUniversity.dll' para gravação – 'O processo não pode acessar o arquivo '...\bin\Debug\netcoreapp1.0\ContosoUniversity.dll' porque ele está sendo usado por outro processo.</span><span class="sxs-lookup"><span data-stu-id="97083-230">Cannot open '...bin\Debug\netcoreapp1.0\ContosoUniversity.dll' for writing -- 'The process cannot access the file '...\bin\Debug\netcoreapp1.0\ContosoUniversity.dll' because it is being used by another process.</span></span>

<span data-ttu-id="97083-231">Solução:</span><span class="sxs-lookup"><span data-stu-id="97083-231">Solution:</span></span>

<span data-ttu-id="97083-232">Pare o site no IIS Express.</span><span class="sxs-lookup"><span data-stu-id="97083-232">Stop the site in IIS Express.</span></span> <span data-ttu-id="97083-233">Acesse a Bandeja do Sistema do Windows, localize o IIS Express e clique com o botão direito do mouse em seu ícone, selecione o site da Contoso University e, em seguida, clique em **Parar Site**.</span><span class="sxs-lookup"><span data-stu-id="97083-233">Go to the Windows System Tray, find IIS Express and right-click its icon, select the Contoso University site, and then click **Stop Site**.</span></span>

### <a name="migration-scaffolded-with-no-code-in-up-and-down-methods"></a><span data-ttu-id="97083-234">Migração gerada por scaffolding sem nenhum código nos métodos Up e Down</span><span class="sxs-lookup"><span data-stu-id="97083-234">Migration scaffolded with no code in Up and Down methods</span></span>

<span data-ttu-id="97083-235">Possível causa:</span><span class="sxs-lookup"><span data-stu-id="97083-235">Possible cause:</span></span>

<span data-ttu-id="97083-236">Os comandos da CLI do EF não fecham e salvam arquivos de código automaticamente.</span><span class="sxs-lookup"><span data-stu-id="97083-236">The EF CLI commands don't automatically close and save code files.</span></span> <span data-ttu-id="97083-237">Se você tiver alterações não salvas ao executar o comando `migrations add`, o EF não encontrará as alterações.</span><span class="sxs-lookup"><span data-stu-id="97083-237">If you have unsaved changes when you run the `migrations add` command, EF won't find your changes.</span></span>

<span data-ttu-id="97083-238">Solução:</span><span class="sxs-lookup"><span data-stu-id="97083-238">Solution:</span></span>

<span data-ttu-id="97083-239">Execute o comando `migrations remove`, salve as alterações de código e execute o comando `migrations add` novamente.</span><span class="sxs-lookup"><span data-stu-id="97083-239">Run the `migrations remove` command, save your code changes and rerun the `migrations add` command.</span></span>

### <a name="errors-while-running-database-update"></a><span data-ttu-id="97083-240">Erros durante a execução da atualização de banco de dados</span><span class="sxs-lookup"><span data-stu-id="97083-240">Errors while running database update</span></span>

<span data-ttu-id="97083-241">É possível receber outros erros ao fazer alterações de esquema em um banco de dados que contém dados existentes.</span><span class="sxs-lookup"><span data-stu-id="97083-241">It's possible to get other errors when making schema changes in a database that has existing data.</span></span> <span data-ttu-id="97083-242">Se você receber erros de migração que não consegue resolver, altere o nome do banco de dados na cadeia de conexão ou exclua o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="97083-242">If you get migration errors you can't resolve, you can either change the database name in the connection string or delete the database.</span></span> <span data-ttu-id="97083-243">Com um novo banco de dados, não há nenhum dado a ser migrado e o comando de atualização de banco de dados terá uma probabilidade muito maior de ser concluído sem erros.</span><span class="sxs-lookup"><span data-stu-id="97083-243">With a new database, there's no data to migrate, and the update-database command is much more likely to complete without errors.</span></span>

<span data-ttu-id="97083-244">A abordagem mais simples é renomear o banco de dados em *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="97083-244">The simplest approach is to rename the database in *appsettings.json*.</span></span> <span data-ttu-id="97083-245">Na próxima vez que você executar `database update`, um novo banco de dados será criado.</span><span class="sxs-lookup"><span data-stu-id="97083-245">The next time you run `database update`, a new database will be created.</span></span>

<span data-ttu-id="97083-246">Para excluir um banco de dados no SSOX, clique com o botão direito do mouse no banco de dados, clique **Excluir** e, em seguida, na caixa de diálogo **Excluir Banco de Dados**, selecione **Fechar conexões existentes** e clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="97083-246">To delete a database in SSOX, right-click the database, click **Delete**, and then in the **Delete Database** dialog box select **Close existing connections** and click **OK**.</span></span>

<span data-ttu-id="97083-247">Para excluir um banco de dados usando a CLI, execute o comando `database drop` da CLI:</span><span class="sxs-lookup"><span data-stu-id="97083-247">To delete a database by using the CLI, run the `database drop` CLI command:</span></span>

```console
dotnet ef database drop
```

### <a name="error-locating-sql-server-instance"></a><span data-ttu-id="97083-248">Erro ao localizar a instância do SQL Server</span><span class="sxs-lookup"><span data-stu-id="97083-248">Error locating SQL Server instance</span></span>

<span data-ttu-id="97083-249">Mensagem de erro:</span><span class="sxs-lookup"><span data-stu-id="97083-249">Error Message:</span></span>

> <span data-ttu-id="97083-250">Ocorreu um erro relacionado à rede ou específico a uma instância ao estabelecer uma conexão com o SQL Server.</span><span class="sxs-lookup"><span data-stu-id="97083-250">A network-related or instance-specific error occurred while establishing a connection to SQL Server.</span></span> <span data-ttu-id="97083-251">O servidor não foi encontrado ou não estava acessível.</span><span class="sxs-lookup"><span data-stu-id="97083-251">The server was not found or was not accessible.</span></span> <span data-ttu-id="97083-252">Verifique se o nome da instância está correto e se o SQL Server está configurado para permitir conexões remotas.</span><span class="sxs-lookup"><span data-stu-id="97083-252">Verify that the instance name is correct and that SQL Server is configured to allow remote connections.</span></span> <span data-ttu-id="97083-253">(provedor: Adaptadores de Rede do SQL, erro: 26 – Erro ao localizar a instância/o servidor especificado)</span><span class="sxs-lookup"><span data-stu-id="97083-253">(provider: SQL Network Interfaces, error: 26 - Error Locating Server/Instance Specified)</span></span>

<span data-ttu-id="97083-254">Solução:</span><span class="sxs-lookup"><span data-stu-id="97083-254">Solution:</span></span>

<span data-ttu-id="97083-255">Verifique a cadeia de conexão.</span><span class="sxs-lookup"><span data-stu-id="97083-255">Check the connection string.</span></span> <span data-ttu-id="97083-256">Se você excluiu o arquivo de banco de dados manualmente, altere o nome do banco de dados na cadeia de caracteres de construção para começar novamente com um novo banco de dados.</span><span class="sxs-lookup"><span data-stu-id="97083-256">If you have manually deleted the database file, change the name of the database in the construction string to start over with a new database.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="97083-257">Obter o código</span><span class="sxs-lookup"><span data-stu-id="97083-257">Get the code</span></span>

[<span data-ttu-id="97083-258">Baixe ou exiba o aplicativo concluído.</span><span class="sxs-lookup"><span data-stu-id="97083-258">Download or view the completed application.</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a><span data-ttu-id="97083-259">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="97083-259">Additional resources</span></span>

<span data-ttu-id="97083-260">Para obter mais informações sobre o EF Core, consulte a [documentação do Entity Framework Core](/ef/core).</span><span class="sxs-lookup"><span data-stu-id="97083-260">For more information about EF Core, see the [Entity Framework Core documentation](/ef/core).</span></span> <span data-ttu-id="97083-261">Também há um livro disponível: [Entity Framework Core in Action](https://www.manning.com/books/entity-framework-core-in-action) (Entity Framework Core em ação).</span><span class="sxs-lookup"><span data-stu-id="97083-261">A book is also available: [Entity Framework Core in Action](https://www.manning.com/books/entity-framework-core-in-action).</span></span>

<span data-ttu-id="97083-262">Para obter informações sobre como implantar um aplicativo Web, confira <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="97083-262">For information on how to deploy a web app, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="97083-263">Para obter informações sobre outros tópicos relacionados ao ASP.NET Core MVC, como autenticação e autorização, confira <xref:index>.</span><span class="sxs-lookup"><span data-stu-id="97083-263">For information about other topics related to ASP.NET Core MVC, such as authentication and authorization, see <xref:index>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="97083-264">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="97083-264">Next steps</span></span>

<span data-ttu-id="97083-265">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="97083-265">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="97083-266">Executou consultas SQL brutas</span><span class="sxs-lookup"><span data-stu-id="97083-266">Performed raw SQL queries</span></span>
> * <span data-ttu-id="97083-267">Chamou uma consulta para retornar entidades</span><span class="sxs-lookup"><span data-stu-id="97083-267">Called a query to return entities</span></span>
> * <span data-ttu-id="97083-268">Chamou uma consulta para outros tipos</span><span class="sxs-lookup"><span data-stu-id="97083-268">Called a query to return other types</span></span>
> * <span data-ttu-id="97083-269">Chamou uma consulta de atualização</span><span class="sxs-lookup"><span data-stu-id="97083-269">Called an update query</span></span>
> * <span data-ttu-id="97083-270">Examinou consultas SQL</span><span class="sxs-lookup"><span data-stu-id="97083-270">Examined SQL queries</span></span>
> * <span data-ttu-id="97083-271">Criou uma camada de abstração</span><span class="sxs-lookup"><span data-stu-id="97083-271">Created an abstraction layer</span></span>
> * <span data-ttu-id="97083-272">Aprendeu sobre a Detecção automática de alterações</span><span class="sxs-lookup"><span data-stu-id="97083-272">Learned about Automatic change detection</span></span>
> * <span data-ttu-id="97083-273">Aprendeu sobre o código-fonte e planos de desenvolvimento do EF Core</span><span class="sxs-lookup"><span data-stu-id="97083-273">Learned about EF Core source code and development plans</span></span>
> * <span data-ttu-id="97083-274">Aprendeu a usar o LINQ dinâmico para simplificar o código</span><span class="sxs-lookup"><span data-stu-id="97083-274">Learned how to use dynamic LINQ to simplify code</span></span>

<span data-ttu-id="97083-275">Isso conclui esta série de tutoriais sobre como usar o Entity Framework Core em um aplicativo ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="97083-275">This completes this series of tutorials on using the Entity Framework Core in an ASP.NET Core MVC application.</span></span> <span data-ttu-id="97083-276">Esta série trabalhou com um novo banco de dados; uma alternativa é fazer engenharia reversa com um modelo de um banco de dados existente.</span><span class="sxs-lookup"><span data-stu-id="97083-276">This series worked with a new database; an alternative is to  reverse engineer a model from an existing database.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="97083-277">Tutorial: EF Core com MVC, banco de dados existente</span><span class="sxs-lookup"><span data-stu-id="97083-277">Tutorial: EF Core with MVC, existing database</span></span>](/ef/core/get-started/aspnetcore/new-db?toc=/aspnet/core/toc.json&bc=/aspnet/core/breadcrumb/toc.json)
