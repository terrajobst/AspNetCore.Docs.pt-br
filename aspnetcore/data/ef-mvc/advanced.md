---
title: ASP.NET Core MVC com EF Core – avançado – 10 de 10
author: rick-anderson
description: Este tutorial apresenta tópicos úteis para ir além das noções básicas de desenvolvimento de aplicativos Web ASP.NET Core que usam o Entity Framework Core.
ms.author: tdykstra
ms.custom: mvc
ms.date: 10/24/2018
uid: data/ef-mvc/advanced
ms.openlocfilehash: ba3834b29e78972bf914a5cba1a2cae3cc19a315
ms.sourcegitcommit: 184ba5b44d1c393076015510ac842b77bc9d4d93
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2019
ms.locfileid: "50090771"
---
# <a name="aspnet-core-mvc-with-ef-core---advanced---10-of-10"></a><span data-ttu-id="3f709-103">ASP.NET Core MVC com EF Core – avançado – 10 de 10</span><span class="sxs-lookup"><span data-stu-id="3f709-103">ASP.NET Core MVC with EF Core - Advanced - 10 of 10</span></span>

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc-21.md)]

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="3f709-104">Por [Tom Dykstra](https://github.com/tdykstra) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3f709-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="3f709-105">O aplicativo web de exemplo Contoso University demonstra como criar aplicativos web do ASP.NET Core MVC usando o Entity Framework Core e o Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="3f709-105">The Contoso University sample web application demonstrates how to create ASP.NET Core MVC web applications using Entity Framework Core and Visual Studio.</span></span> <span data-ttu-id="3f709-106">Para obter informações sobre a série de tutoriais, consulte [o primeiro tutorial da série](intro.md).</span><span class="sxs-lookup"><span data-stu-id="3f709-106">For information about the tutorial series, see [the first tutorial in the series](intro.md).</span></span>

<span data-ttu-id="3f709-107">No tutorial anterior, você implementou a herança de tabela por hierarquia.</span><span class="sxs-lookup"><span data-stu-id="3f709-107">In the previous tutorial, you implemented table-per-hierarchy inheritance.</span></span> <span data-ttu-id="3f709-108">Este tutorial apresenta vários tópicos que são úteis para consideração quando você vai além dos conceitos básicos de desenvolvimento de aplicativos Web ASP.NET Core que usam o Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="3f709-108">This tutorial introduces several topics that are useful to be aware of when you go beyond the basics of developing ASP.NET Core web applications that use Entity Framework Core.</span></span>

## <a name="raw-sql-queries"></a><span data-ttu-id="3f709-109">Consultas SQL brutas</span><span class="sxs-lookup"><span data-stu-id="3f709-109">Raw SQL Queries</span></span>

<span data-ttu-id="3f709-110">Uma das vantagens de usar o Entity Framework é que ele evita vincular o código de forma muito próxima a um método específico de armazenamento de dados.</span><span class="sxs-lookup"><span data-stu-id="3f709-110">One of the advantages of using the Entity Framework is that it avoids tying your code too closely to a particular method of storing data.</span></span> <span data-ttu-id="3f709-111">Ele faz isso pela geração de consultas SQL e comandos para você, que também libera você da necessidade de escrevê-los.</span><span class="sxs-lookup"><span data-stu-id="3f709-111">It does this by generating SQL queries and commands for you, which also frees you from having to write them yourself.</span></span> <span data-ttu-id="3f709-112">Mas há casos excepcionais em que você precisa executar consultas SQL específicas criadas manualmente.</span><span class="sxs-lookup"><span data-stu-id="3f709-112">But there are exceptional scenarios when you need to run specific SQL queries that you have manually created.</span></span> <span data-ttu-id="3f709-113">Para esses cenários, a API do Code First do Entity Framework inclui métodos que permitem passar comandos SQL diretamente para o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="3f709-113">For these scenarios, the Entity Framework Code First API includes methods that enable you to pass SQL commands directly to the database.</span></span> <span data-ttu-id="3f709-114">Você tem as seguintes opções no EF Core 1.0:</span><span class="sxs-lookup"><span data-stu-id="3f709-114">You have the following options in EF Core 1.0:</span></span>

* <span data-ttu-id="3f709-115">Use o método `DbSet.FromSql` para consultas que retornam tipos de entidade.</span><span class="sxs-lookup"><span data-stu-id="3f709-115">Use the `DbSet.FromSql` method for queries that return entity types.</span></span> <span data-ttu-id="3f709-116">Os objetos retornados precisam ser do tipo esperado pelo objeto `DbSet` e são controlados automaticamente pelo contexto de banco de dados, a menos que você [desative o controle](crud.md#no-tracking-queries).</span><span class="sxs-lookup"><span data-stu-id="3f709-116">The returned objects must be of the type expected by the `DbSet` object, and they're automatically tracked by the database context unless you [turn tracking off](crud.md#no-tracking-queries).</span></span>

* <span data-ttu-id="3f709-117">Use o `Database.ExecuteSqlCommand` para comandos que não sejam de consulta.</span><span class="sxs-lookup"><span data-stu-id="3f709-117">Use the `Database.ExecuteSqlCommand` for non-query commands.</span></span>

<span data-ttu-id="3f709-118">Caso precise executar uma consulta que retorna tipos que não são entidades, use o ADO.NET com a conexão de banco de dados fornecida pelo EF.</span><span class="sxs-lookup"><span data-stu-id="3f709-118">If you need to run a query that returns types that aren't entities, you can use ADO.NET with the database connection provided by EF.</span></span> <span data-ttu-id="3f709-119">Os dados retornados não são controlados pelo contexto de banco de dados, mesmo se esse método é usado para recuperar tipos de entidade.</span><span class="sxs-lookup"><span data-stu-id="3f709-119">The returned data isn't tracked by the database context, even if you use this method to retrieve entity types.</span></span>

<span data-ttu-id="3f709-120">Como é sempre verdadeiro quando você executa comandos SQL em um aplicativo Web, é necessário tomar precauções para proteger o site contra ataques de injeção de SQL.</span><span class="sxs-lookup"><span data-stu-id="3f709-120">As is always true when you execute SQL commands in a web application, you must take precautions to protect your site against SQL injection attacks.</span></span> <span data-ttu-id="3f709-121">Uma maneira de fazer isso é usar consultas parametrizadas para garantir que as cadeias de caracteres enviadas por uma página da Web não possam ser interpretadas como comandos SQL.</span><span class="sxs-lookup"><span data-stu-id="3f709-121">One way to do that is to use parameterized queries to make sure that strings submitted by a web page can't be interpreted as SQL commands.</span></span> <span data-ttu-id="3f709-122">Neste tutorial, você usará consultas parametrizadas ao integrar a entrada do usuário a uma consulta.</span><span class="sxs-lookup"><span data-stu-id="3f709-122">In this tutorial you'll use parameterized queries when integrating user input into a query.</span></span>

## <a name="call-a-query-that-returns-entities"></a><span data-ttu-id="3f709-123">Chamar uma consulta que retorna entidades</span><span class="sxs-lookup"><span data-stu-id="3f709-123">Call a query that returns entities</span></span>

<span data-ttu-id="3f709-124">A classe `DbSet<TEntity>` fornece um método que você pode usar para executar uma consulta que retorna uma entidade do tipo `TEntity`.</span><span class="sxs-lookup"><span data-stu-id="3f709-124">The `DbSet<TEntity>` class provides a method that you can use to execute a query that returns an entity of type `TEntity`.</span></span> <span data-ttu-id="3f709-125">Para ver como isso funciona, você alterará o código no método `Details` do controlador Departamento.</span><span class="sxs-lookup"><span data-stu-id="3f709-125">To see how this works you'll change the code in the `Details` method of the Department controller.</span></span>

<span data-ttu-id="3f709-126">Em *DepartmentsController.cs*, no método `Details`, substitua o código que recupera um departamento com uma chamada de método `FromSql`, conforme mostrado no seguinte código realçado:</span><span class="sxs-lookup"><span data-stu-id="3f709-126">In *DepartmentsController.cs*, in the `Details` method, replace the code that retrieves a department with a `FromSql` method call, as shown in the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_RawSQL&highlight=8,9,10,13)]

<span data-ttu-id="3f709-127">Para verificar se o novo código funciona corretamente, selecione a guia **Departamentos** e, em seguida, **Detalhes** de um dos departamentos.</span><span class="sxs-lookup"><span data-stu-id="3f709-127">To verify that the new code works correctly, select the **Departments** tab and then **Details** for one of the departments.</span></span>

![Detalhes do departamento](advanced/_static/department-details.png)

## <a name="call-a-query-that-returns-other-types"></a><span data-ttu-id="3f709-129">Chamar uma consulta que retorna outros tipos</span><span class="sxs-lookup"><span data-stu-id="3f709-129">Call a query that returns other types</span></span>

<span data-ttu-id="3f709-130">Anteriormente, você criou uma grade de estatísticas de alunos para a página Sobre que mostrava o número de alunos para cada data de registro.</span><span class="sxs-lookup"><span data-stu-id="3f709-130">Earlier you created a student statistics grid for the About page that showed the number of students for each enrollment date.</span></span> <span data-ttu-id="3f709-131">Você obteve os dados do conjunto de entidades Students (`_context.Students`) e usou o LINQ para projetar os resultados em uma lista de objetos de modelo de exibição `EnrollmentDateGroup`.</span><span class="sxs-lookup"><span data-stu-id="3f709-131">You got the data from the Students entity set (`_context.Students`) and used LINQ to project the results into a list of `EnrollmentDateGroup` view model objects.</span></span> <span data-ttu-id="3f709-132">Suponha que você deseje gravar o próprio SQL em vez de usar LINQ.</span><span class="sxs-lookup"><span data-stu-id="3f709-132">Suppose you want to write the SQL itself rather than using LINQ.</span></span> <span data-ttu-id="3f709-133">Para fazer isso, você precisa executar uma consulta SQL que retorna algo diferente de objetos de entidade.</span><span class="sxs-lookup"><span data-stu-id="3f709-133">To do that you need to run a SQL query that returns something other than entity objects.</span></span> <span data-ttu-id="3f709-134">No EF Core 1.0, uma maneira de fazer isso é escrever um código ADO.NET e obter a conexão de banco de dados do EF.</span><span class="sxs-lookup"><span data-stu-id="3f709-134">In EF Core 1.0, one way to do that is write ADO.NET code and get the database connection from EF.</span></span>

<span data-ttu-id="3f709-135">Em *HomeController.cs*, substitua o método `About` pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="3f709-135">In *HomeController.cs*, replace the `About` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseRawSQL&highlight=3-32)]

<span data-ttu-id="3f709-136">Adicionar uma instrução using:</span><span class="sxs-lookup"><span data-stu-id="3f709-136">Add a using statement:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings2)]

<span data-ttu-id="3f709-137">Execute o aplicativo e acesse a página Sobre.</span><span class="sxs-lookup"><span data-stu-id="3f709-137">Run the app and go to the About page.</span></span> <span data-ttu-id="3f709-138">Ela exibe os mesmos dados que antes.</span><span class="sxs-lookup"><span data-stu-id="3f709-138">It displays the same data it did before.</span></span>

![Página Sobre](advanced/_static/about.png)

## <a name="call-an-update-query"></a><span data-ttu-id="3f709-140">Chamar uma consulta de atualização</span><span class="sxs-lookup"><span data-stu-id="3f709-140">Call an update query</span></span>

<span data-ttu-id="3f709-141">Suponha que os administradores do Contoso University desejem executar alterações globais no banco de dados, como alterar o número de créditos para cada curso.</span><span class="sxs-lookup"><span data-stu-id="3f709-141">Suppose Contoso University administrators want to perform global changes in the database, such as changing the number of credits for every course.</span></span> <span data-ttu-id="3f709-142">Se a universidade tiver uma grande quantidade de cursos, poderá ser ineficiente recuperá-los como entidades e alterá-los individualmente.</span><span class="sxs-lookup"><span data-stu-id="3f709-142">If the university has a large number of courses, it would be inefficient to retrieve them all as entities and change them individually.</span></span> <span data-ttu-id="3f709-143">Nesta seção, você implementará uma página da Web que permite ao usuário especificar um fator pelo qual alterar o número de créditos para todos os cursos e você fará a alteração executando uma instrução SQL UPDATE.</span><span class="sxs-lookup"><span data-stu-id="3f709-143">In this section you'll implement a web page that enables the user to specify a factor by which to change the number of credits for all courses, and you'll make the change by executing a SQL UPDATE statement.</span></span> <span data-ttu-id="3f709-144">A página da Web será semelhante à seguinte ilustração:</span><span class="sxs-lookup"><span data-stu-id="3f709-144">The web page will look like the following illustration:</span></span>

![Página Atualizar Créditos de Curso](advanced/_static/update-credits.png)

<span data-ttu-id="3f709-146">Em *CoursesController.cs*, adicione métodos UpdateCourseCredits para HttpGet e HttpPost:</span><span class="sxs-lookup"><span data-stu-id="3f709-146">In *CoursesContoller.cs*, add UpdateCourseCredits methods for HttpGet and HttpPost:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdateGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdatePost)]

<span data-ttu-id="3f709-147">Quando o controlador processa uma solicitação HttpGet, nada é retornado em `ViewData["RowsAffected"]`, e a exibição mostra uma caixa de texto vazia e um botão Enviar, conforme mostrado na ilustração anterior.</span><span class="sxs-lookup"><span data-stu-id="3f709-147">When the controller processes an HttpGet request, nothing is returned in `ViewData["RowsAffected"]`, and the view displays an empty text box and a submit button, as shown in the preceding illustration.</span></span>

<span data-ttu-id="3f709-148">Quando o botão **Atualizar** recebe um clique, o método HttpPost é chamado e multiplicador tem o valor inserido na caixa de texto.</span><span class="sxs-lookup"><span data-stu-id="3f709-148">When the **Update** button is clicked, the HttpPost method is called, and multiplier has the value entered in the text box.</span></span> <span data-ttu-id="3f709-149">Em seguida, o código executa o SQL que atualiza os cursos e retorna o número de linhas afetadas para a exibição em `ViewData`.</span><span class="sxs-lookup"><span data-stu-id="3f709-149">The code then executes the SQL that updates courses and returns the number of affected rows to the view in `ViewData`.</span></span> <span data-ttu-id="3f709-150">Quando a exibição obtém um valor `RowsAffected`, ela mostra o número de linhas atualizadas.</span><span class="sxs-lookup"><span data-stu-id="3f709-150">When the view gets a `RowsAffected` value, it displays the number of rows updated.</span></span>

<span data-ttu-id="3f709-151">No **Gerenciador de Soluções**, clique com o botão direito do mouse na pasta *Views/Courses* e, em seguida, clique em **Adicionar > Novo Item**.</span><span class="sxs-lookup"><span data-stu-id="3f709-151">In **Solution Explorer**, right-click the *Views/Courses* folder, and then click **Add > New Item**.</span></span>

<span data-ttu-id="3f709-152">Na caixa de diálogo **Adicionar Novo Item**, clique em **ASP.NET** em **Instalado** no painel esquerdo, clique em **Página de Exibição MVC** e nomeie a nova exibição *UpdateCourseCredits.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="3f709-152">In the **Add New Item** dialog, click **ASP.NET** under **Installed** in the left pane, click **MVC View Page**, and name the new view *UpdateCourseCredits.cshtml*.</span></span>

<span data-ttu-id="3f709-153">Em *Views/Courses/UpdateCourseCredits.cshtml*, substitua o código de modelo pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="3f709-153">In *Views/Courses/UpdateCourseCredits.cshtml*, replace the template code with the following code:</span></span>

[!code-html[](intro/samples/cu/Views/Courses/UpdateCourseCredits.cshtml)]

<span data-ttu-id="3f709-154">Execute o método `UpdateCourseCredits` selecionando a guia **Cursos**, adicionando, em seguida, "/UpdateCourseCredits" ao final da URL na barra de endereços do navegador (por exemplo: `http://localhost:5813/Courses/UpdateCourseCredits`).</span><span class="sxs-lookup"><span data-stu-id="3f709-154">Run the `UpdateCourseCredits` method by selecting the **Courses** tab, then adding "/UpdateCourseCredits" to the end of the URL in the browser's address bar (for example: `http://localhost:5813/Courses/UpdateCourseCredits`).</span></span> <span data-ttu-id="3f709-155">Insira um número na caixa de texto:</span><span class="sxs-lookup"><span data-stu-id="3f709-155">Enter a number in the text box:</span></span>

![Página Atualizar Créditos de Curso](advanced/_static/update-credits.png)

<span data-ttu-id="3f709-157">Clique em **Atualizar**.</span><span class="sxs-lookup"><span data-stu-id="3f709-157">Click **Update**.</span></span> <span data-ttu-id="3f709-158">O número de linhas afetadas é exibido:</span><span class="sxs-lookup"><span data-stu-id="3f709-158">You see the number of rows affected:</span></span>

![Linhas afetadas na página Atualizar Créditos de Curso](advanced/_static/update-credits-rows-affected.png)

<span data-ttu-id="3f709-160">Clique em **Voltar para a Lista** para ver a lista de cursos com o número revisado de créditos.</span><span class="sxs-lookup"><span data-stu-id="3f709-160">Click **Back to List** to see the list of courses with the revised number of credits.</span></span>

<span data-ttu-id="3f709-161">Observe que o código de produção deve garantir que as atualizações sempre resultem em dados válidos.</span><span class="sxs-lookup"><span data-stu-id="3f709-161">Note that production code would ensure that updates always result in valid data.</span></span> <span data-ttu-id="3f709-162">O código simplificado mostrado aqui pode multiplicar o número de créditos o suficiente para resultar em números maiores que 5.</span><span class="sxs-lookup"><span data-stu-id="3f709-162">The simplified code shown here could multiply the number of credits enough to result in numbers greater than 5.</span></span> <span data-ttu-id="3f709-163">(A propriedade `Credits` tem um atributo `[Range(0, 5)]`.) A consulta de atualização funciona, mas os dados inválidos podem causar resultados inesperados em outras partes do sistema que supõem que o número de créditos seja 5 ou inferior.</span><span class="sxs-lookup"><span data-stu-id="3f709-163">(The `Credits` property has a `[Range(0, 5)]` attribute.) The update query would work but the invalid data could cause unexpected results in other parts of the system that assume the number of credits is 5 or less.</span></span>

<span data-ttu-id="3f709-164">Para obter mais informações sobre consultas SQL brutas, consulte [Consultas SQL brutas](/ef/core/querying/raw-sql).</span><span class="sxs-lookup"><span data-stu-id="3f709-164">For more information about raw SQL queries, see [Raw SQL Queries](/ef/core/querying/raw-sql).</span></span>

## <a name="examine-sql-sent-to-the-database"></a><span data-ttu-id="3f709-165">Examinar o SQL enviado ao banco de dados</span><span class="sxs-lookup"><span data-stu-id="3f709-165">Examine SQL sent to the database</span></span>

<span data-ttu-id="3f709-166">Às vezes, é útil poder ver as consultas SQL reais que são enviadas ao banco de dados.</span><span class="sxs-lookup"><span data-stu-id="3f709-166">Sometimes it's helpful to be able to see the actual SQL queries that are sent to the database.</span></span> <span data-ttu-id="3f709-167">A funcionalidade de log interno do ASP.NET Core é usada automaticamente pelo EF Core para gravar logs que contêm o SQL de consultas e atualizações.</span><span class="sxs-lookup"><span data-stu-id="3f709-167">Built-in logging functionality for ASP.NET Core is automatically used by EF Core to write logs that contain the SQL for queries and updates.</span></span> <span data-ttu-id="3f709-168">Nesta seção, você verá alguns exemplos de log do SQL.</span><span class="sxs-lookup"><span data-stu-id="3f709-168">In this section you'll see some examples of SQL logging.</span></span>

<span data-ttu-id="3f709-169">Abra *StudentsController.cs* e, no método `Details`, defina um ponto de interrupção na instrução `if (student == null)`.</span><span class="sxs-lookup"><span data-stu-id="3f709-169">Open *StudentsController.cs* and in the `Details` method set a breakpoint on the `if (student == null)` statement.</span></span>

<span data-ttu-id="3f709-170">Execute o aplicativo no modo de depuração e acesse a página Detalhes de um aluno.</span><span class="sxs-lookup"><span data-stu-id="3f709-170">Run the app in debug mode, and go to the Details page for a student.</span></span>

<span data-ttu-id="3f709-171">Acesse a janela de **Saída** mostrando a saída de depuração e você verá a consulta:</span><span class="sxs-lookup"><span data-stu-id="3f709-171">Go to the **Output** window showing debug output, and you see the query:</span></span>

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

<span data-ttu-id="3f709-172">Você observará algo aqui que pode ser surpreendente: o SQL seleciona até 2 linhas (`TOP(2)`) da tabela Person.</span><span class="sxs-lookup"><span data-stu-id="3f709-172">You'll notice something here that might surprise you: the SQL selects up to 2 rows (`TOP(2)`) from the Person table.</span></span> <span data-ttu-id="3f709-173">O método `SingleOrDefaultAsync` não é resolvido para uma 1 linha no servidor.</span><span class="sxs-lookup"><span data-stu-id="3f709-173">The `SingleOrDefaultAsync` method doesn't resolve to 1 row on the server.</span></span> <span data-ttu-id="3f709-174">Eis o porquê:</span><span class="sxs-lookup"><span data-stu-id="3f709-174">Here's why:</span></span>

* <span data-ttu-id="3f709-175">Se a consulta retorna várias linhas, o método retorna nulo.</span><span class="sxs-lookup"><span data-stu-id="3f709-175">If the query would return multiple rows, the method returns null.</span></span>
* <span data-ttu-id="3f709-176">Para determinar se a consulta retorna várias linhas, o EF precisa verificar se ela retorna pelo menos 2.</span><span class="sxs-lookup"><span data-stu-id="3f709-176">To determine whether the query would return multiple rows, EF has to check if it returns at least 2.</span></span>

<span data-ttu-id="3f709-177">Observe que você não precisa usar o modo de depuração e parar em um ponto de interrupção para obter a saída de log na janela de **Saída**.</span><span class="sxs-lookup"><span data-stu-id="3f709-177">Note that you don't have to use debug mode and stop at a breakpoint to get logging output in the **Output** window.</span></span> <span data-ttu-id="3f709-178">É apenas um modo conveniente de parar o log no ponto em que você deseja examinar a saída.</span><span class="sxs-lookup"><span data-stu-id="3f709-178">It's just a convenient way to stop the logging at the point you want to look at the output.</span></span> <span data-ttu-id="3f709-179">Se você não fizer isso, o log continuará e você precisará rolar para baixo para localizar as partes de seu interesse.</span><span class="sxs-lookup"><span data-stu-id="3f709-179">If you don't do that, logging continues and you have to scroll back to find the parts you're interested in.</span></span>

## <a name="repository-and-unit-of-work-patterns"></a><span data-ttu-id="3f709-180">Padrões de repositório e unidade de trabalho</span><span class="sxs-lookup"><span data-stu-id="3f709-180">Repository and unit of work patterns</span></span>

<span data-ttu-id="3f709-181">Muitos desenvolvedores escrevem um código para implementar padrões de repositório e unidade de trabalho como um wrapper em torno do código que funciona com o Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="3f709-181">Many developers write code to implement the repository and unit of work patterns as a wrapper around code that works with the Entity Framework.</span></span> <span data-ttu-id="3f709-182">Esses padrões destinam-se a criar uma camada de abstração entre a camada de acesso a dados e a camada da lógica de negócios de um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3f709-182">These patterns are intended to create an abstraction layer between the data access layer and the business logic layer of an application.</span></span> <span data-ttu-id="3f709-183">A implementação desses padrões pode ajudar a isolar o aplicativo de alterações no armazenamento de dados e pode facilitar o teste de unidade automatizado ou TDD (desenvolvimento orientado por testes).</span><span class="sxs-lookup"><span data-stu-id="3f709-183">Implementing these patterns can help insulate your application from changes in the data store and can facilitate automated unit testing or test-driven development (TDD).</span></span> <span data-ttu-id="3f709-184">No entanto, escrever um código adicional para implementar esses padrões nem sempre é a melhor escolha para aplicativos que usam o EF, por vários motivos:</span><span class="sxs-lookup"><span data-stu-id="3f709-184">However, writing additional code to implement these patterns isn't always the best choice for applications that use EF, for several reasons:</span></span>

* <span data-ttu-id="3f709-185">A própria classe de contexto do EF isola o código de código específico a um armazenamento de dados.</span><span class="sxs-lookup"><span data-stu-id="3f709-185">The EF context class itself insulates your code from data-store-specific code.</span></span>

* <span data-ttu-id="3f709-186">A classe de contexto do EF pode atuar como uma classe de unidade de trabalho para as atualizações de banco de dados feitas com o EF.</span><span class="sxs-lookup"><span data-stu-id="3f709-186">The EF context class can act as a unit-of-work class for database updates that you do using EF.</span></span>

* <span data-ttu-id="3f709-187">O EF inclui recursos para implementar o TDD sem escrever um código de repositório.</span><span class="sxs-lookup"><span data-stu-id="3f709-187">EF includes features for implementing TDD without writing repository code.</span></span>

<span data-ttu-id="3f709-188">Para obter informações sobre como implementar os padrões de repositório e unidade de trabalho, consulte [a versão do Entity Framework 5 desta série de tutoriais](/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application).</span><span class="sxs-lookup"><span data-stu-id="3f709-188">For information about how to implement the repository and unit of work patterns, see [the Entity Framework 5 version of this tutorial series](/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application).</span></span>

<span data-ttu-id="3f709-189">O Entity Framework Core implementa um provedor de banco de dados em memória que pode ser usado para teste.</span><span class="sxs-lookup"><span data-stu-id="3f709-189">Entity Framework Core implements an in-memory database provider that can be used for testing.</span></span> <span data-ttu-id="3f709-190">Para obter mais informações, confira [Testar com InMemory](/ef/core/miscellaneous/testing/in-memory).</span><span class="sxs-lookup"><span data-stu-id="3f709-190">For more information, see [Test with InMemory](/ef/core/miscellaneous/testing/in-memory).</span></span>

## <a name="automatic-change-detection"></a><span data-ttu-id="3f709-191">Detecção automática de alterações</span><span class="sxs-lookup"><span data-stu-id="3f709-191">Automatic change detection</span></span>

<span data-ttu-id="3f709-192">O Entity Framework determina como uma entidade foi alterada (e, portanto, quais atualizações precisam ser enviadas ao banco de dados), comparando os valores atuais de uma entidade com os valores originais.</span><span class="sxs-lookup"><span data-stu-id="3f709-192">The Entity Framework determines how an entity has changed (and therefore which updates need to be sent to the database) by comparing the current values of an entity with the original values.</span></span> <span data-ttu-id="3f709-193">Os valores originais são armazenados quando a entidade é consultada ou anexada.</span><span class="sxs-lookup"><span data-stu-id="3f709-193">The original values are stored when the entity is queried or attached.</span></span> <span data-ttu-id="3f709-194">Alguns dos métodos que causam a detecção automática de alterações são os seguintes:</span><span class="sxs-lookup"><span data-stu-id="3f709-194">Some of the methods that cause automatic change detection are the following:</span></span>

* <span data-ttu-id="3f709-195">DbContext.SaveChanges</span><span class="sxs-lookup"><span data-stu-id="3f709-195">DbContext.SaveChanges</span></span>

* <span data-ttu-id="3f709-196">DbContext.Entry</span><span class="sxs-lookup"><span data-stu-id="3f709-196">DbContext.Entry</span></span>

* <span data-ttu-id="3f709-197">ChangeTracker.Entries</span><span class="sxs-lookup"><span data-stu-id="3f709-197">ChangeTracker.Entries</span></span>

<span data-ttu-id="3f709-198">Se você estiver controlando um grande número de entidades e chamar um desses métodos muitas vezes em um loop, poderá obter melhorias significativas de desempenho desativando temporariamente a detecção automática de alterações usando a propriedade `ChangeTracker.AutoDetectChangesEnabled`.</span><span class="sxs-lookup"><span data-stu-id="3f709-198">If you're tracking a large number of entities and you call one of these methods many times in a loop, you might get significant performance improvements by temporarily turning off automatic change detection using the `ChangeTracker.AutoDetectChangesEnabled` property.</span></span> <span data-ttu-id="3f709-199">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="3f709-199">For example:</span></span>

```csharp
_context.ChangeTracker.AutoDetectChangesEnabled = false;
```

## <a name="entity-framework-core-source-code-and-development-plans"></a><span data-ttu-id="3f709-200">Código-fonte e planos de desenvolvimento do Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="3f709-200">Entity Framework Core source code and development plans</span></span>

<span data-ttu-id="3f709-201">O código-fonte do Entity Framework Core está em [https://github.com/aspnet/EntityFrameworkCore](https://github.com/aspnet/EntityFrameworkCore).</span><span class="sxs-lookup"><span data-stu-id="3f709-201">The Entity Framework Core source is at [https://github.com/aspnet/EntityFrameworkCore](https://github.com/aspnet/EntityFrameworkCore).</span></span> <span data-ttu-id="3f709-202">O repositório do EF Core contém builds noturnos, acompanhamento de questões, especificações de recurso, notas de reuniões de design e [o roteiro para desenvolvimento futuro](https://github.com/aspnet/EntityFrameworkCore/wiki/Roadmap).</span><span class="sxs-lookup"><span data-stu-id="3f709-202">The EF Core repository contains nightly builds, issue tracking, feature specs, design meeting notes, and [the roadmap for future development](https://github.com/aspnet/EntityFrameworkCore/wiki/Roadmap).</span></span> <span data-ttu-id="3f709-203">Arquive ou encontre bugs e contribua.</span><span class="sxs-lookup"><span data-stu-id="3f709-203">You can file or find bugs, and contribute.</span></span>

<span data-ttu-id="3f709-204">Embora o código-fonte seja aberto, há suporte completo para o Entity Framework Core como um produto Microsoft.</span><span class="sxs-lookup"><span data-stu-id="3f709-204">Although the source code is open, Entity Framework Core is fully supported as a Microsoft product.</span></span> <span data-ttu-id="3f709-205">A equipe do Microsoft Entity Framework mantém controle sobre quais contribuições são aceitas e testa todas as alterações de código para garantir a qualidade de cada versão.</span><span class="sxs-lookup"><span data-stu-id="3f709-205">The Microsoft Entity Framework team keeps control over which contributions are accepted and tests all code changes to ensure the quality of each release.</span></span>

## <a name="reverse-engineer-from-existing-database"></a><span data-ttu-id="3f709-206">Fazer engenharia reversa do banco de dados existente</span><span class="sxs-lookup"><span data-stu-id="3f709-206">Reverse engineer from existing database</span></span>

<span data-ttu-id="3f709-207">Para fazer engenharia reversa de um modelo de dados, incluindo classes de entidade de um banco de dados existente, use o comando [scaffold-dbcontext](/ef/core/miscellaneous/cli/powershell#scaffold-dbcontext).</span><span class="sxs-lookup"><span data-stu-id="3f709-207">To reverse engineer a data model including entity classes from an existing database, use the [scaffold-dbcontext](/ef/core/miscellaneous/cli/powershell#scaffold-dbcontext) command.</span></span> <span data-ttu-id="3f709-208">Consulte o [tutorial de introdução](/ef/core/get-started/aspnetcore/existing-db).</span><span class="sxs-lookup"><span data-stu-id="3f709-208">See the [getting-started tutorial](/ef/core/get-started/aspnetcore/existing-db).</span></span>

<a id="dynamic-linq"></a>
## <a name="use-dynamic-linq-to-simplify-sort-selection-code"></a><span data-ttu-id="3f709-209">Usar o LINQ dinâmico para simplificar o código de seleção de classificação</span><span class="sxs-lookup"><span data-stu-id="3f709-209">Use dynamic LINQ to simplify sort selection code</span></span>

<span data-ttu-id="3f709-210">O [terceiro tutorial desta série](sort-filter-page.md) mostra como escrever um código LINQ embutindo nomes de colunas em código em uma instrução `switch`.</span><span class="sxs-lookup"><span data-stu-id="3f709-210">The [third tutorial in this series](sort-filter-page.md) shows how to write LINQ code by hard-coding column names in a `switch` statement.</span></span> <span data-ttu-id="3f709-211">Com duas colunas para escolha, isso funciona bem, mas se você tiver muitas colunas, o código poderá ficar detalhado.</span><span class="sxs-lookup"><span data-stu-id="3f709-211">With two columns to choose from, this works fine, but if you have many columns the code could get verbose.</span></span> <span data-ttu-id="3f709-212">Para resolver esse problema, use o método `EF.Property` para especificar o nome da propriedade como uma cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="3f709-212">To solve that problem, you can use the `EF.Property` method to specify the name of the property as a string.</span></span> <span data-ttu-id="3f709-213">Para usar essa abordagem, substitua o método `Index` no `StudentsController` pelo código a seguir.</span><span class="sxs-lookup"><span data-stu-id="3f709-213">To try out this approach, replace the `Index` method in the `StudentsController` with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DynamicLinq)]

## <a name="next-steps"></a><span data-ttu-id="3f709-214">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="3f709-214">Next steps</span></span>

<span data-ttu-id="3f709-215">Isso conclui esta série de tutoriais sobre como usar o Entity Framework Core em um aplicativo ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="3f709-215">This completes this series of tutorials on using the Entity Framework Core in an ASP.NET Core MVC application.</span></span>

<span data-ttu-id="3f709-216">Para obter mais informações sobre o EF Core, consulte a [documentação do Entity Framework Core](/ef/core).</span><span class="sxs-lookup"><span data-stu-id="3f709-216">For more information about EF Core, see the [Entity Framework Core documentation](/ef/core).</span></span> <span data-ttu-id="3f709-217">Também há um livro disponível: [Entity Framework Core in Action](https://www.manning.com/books/entity-framework-core-in-action) (Entity Framework Core em ação).</span><span class="sxs-lookup"><span data-stu-id="3f709-217">A book is also available: [Entity Framework Core in Action](https://www.manning.com/books/entity-framework-core-in-action).</span></span>

<span data-ttu-id="3f709-218">Para obter informações sobre como implantar um aplicativo Web, confira <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="3f709-218">For information on how to deploy a web app, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="3f709-219">Para obter informações sobre outros tópicos relacionados ao ASP.NET Core MVC, como autenticação e autorização, confira <xref:index>.</span><span class="sxs-lookup"><span data-stu-id="3f709-219">For information about other topics related to ASP.NET Core MVC, such as authentication and authorization, see <xref:index>.</span></span>

## <a name="acknowledgments"></a><span data-ttu-id="3f709-220">Agradecimentos</span><span class="sxs-lookup"><span data-stu-id="3f709-220">Acknowledgments</span></span>

<span data-ttu-id="3f709-221">Tom Dykstra e Rick Anderson (twitter @RickAndMSFT) escreveram este tutorial.</span><span class="sxs-lookup"><span data-stu-id="3f709-221">Tom Dykstra and Rick Anderson (twitter @RickAndMSFT) wrote this tutorial.</span></span> <span data-ttu-id="3f709-222">Rowan Miller, Diego Vega e outros membros da equipe do Entity Framework auxiliaram com revisões de código e ajudaram com problemas de depuração que surgiram durante a codificação para os tutoriais.</span><span class="sxs-lookup"><span data-stu-id="3f709-222">Rowan Miller, Diego Vega, and other members of the Entity Framework team assisted with code reviews and helped debug issues that arose while we were writing code for the tutorials.</span></span>

## <a name="common-errors"></a><span data-ttu-id="3f709-223">Erros comuns</span><span class="sxs-lookup"><span data-stu-id="3f709-223">Common errors</span></span>

### <a name="contosouniversitydll-used-by-another-process"></a><span data-ttu-id="3f709-224">ContosoUniversity.dll usada por outro processo</span><span class="sxs-lookup"><span data-stu-id="3f709-224">ContosoUniversity.dll used by another process</span></span>

<span data-ttu-id="3f709-225">Mensagem de erro:</span><span class="sxs-lookup"><span data-stu-id="3f709-225">Error message:</span></span>

> <span data-ttu-id="3f709-226">Não é possível abrir '...bin\Debug\netcoreapp1.0\ContosoUniversity.dll' para gravação – 'O processo não pode acessar o arquivo '...\bin\Debug\netcoreapp1.0\ContosoUniversity.dll' porque ele está sendo usado por outro processo.</span><span class="sxs-lookup"><span data-stu-id="3f709-226">Cannot open '...bin\Debug\netcoreapp1.0\ContosoUniversity.dll' for writing -- 'The process cannot access the file '...\bin\Debug\netcoreapp1.0\ContosoUniversity.dll' because it is being used by another process.</span></span>

<span data-ttu-id="3f709-227">Solução:</span><span class="sxs-lookup"><span data-stu-id="3f709-227">Solution:</span></span>

<span data-ttu-id="3f709-228">Pare o site no IIS Express.</span><span class="sxs-lookup"><span data-stu-id="3f709-228">Stop the site in IIS Express.</span></span> <span data-ttu-id="3f709-229">Acesse a Bandeja do Sistema do Windows, localize o IIS Express e clique com o botão direito do mouse em seu ícone, selecione o site da Contoso University e, em seguida, clique em **Parar Site**.</span><span class="sxs-lookup"><span data-stu-id="3f709-229">Go to the Windows System Tray, find IIS Express and right-click its icon, select the Contoso University site, and then click **Stop Site**.</span></span>

### <a name="migration-scaffolded-with-no-code-in-up-and-down-methods"></a><span data-ttu-id="3f709-230">Migração gerada por scaffolding sem nenhum código nos métodos Up e Down</span><span class="sxs-lookup"><span data-stu-id="3f709-230">Migration scaffolded with no code in Up and Down methods</span></span>

<span data-ttu-id="3f709-231">Possível causa:</span><span class="sxs-lookup"><span data-stu-id="3f709-231">Possible cause:</span></span>

<span data-ttu-id="3f709-232">Os comandos da CLI do EF não fecham e salvam arquivos de código automaticamente.</span><span class="sxs-lookup"><span data-stu-id="3f709-232">The EF CLI commands don't automatically close and save code files.</span></span> <span data-ttu-id="3f709-233">Se você tiver alterações não salvas ao executar o comando `migrations add`, o EF não encontrará as alterações.</span><span class="sxs-lookup"><span data-stu-id="3f709-233">If you have unsaved changes when you run the `migrations add` command, EF won't find your changes.</span></span>

<span data-ttu-id="3f709-234">Solução:</span><span class="sxs-lookup"><span data-stu-id="3f709-234">Solution:</span></span>

<span data-ttu-id="3f709-235">Execute o comando `migrations remove`, salve as alterações de código e execute o comando `migrations add` novamente.</span><span class="sxs-lookup"><span data-stu-id="3f709-235">Run the `migrations remove` command, save your code changes and rerun the `migrations add` command.</span></span>

### <a name="errors-while-running-database-update"></a><span data-ttu-id="3f709-236">Erros durante a execução da atualização de banco de dados</span><span class="sxs-lookup"><span data-stu-id="3f709-236">Errors while running database update</span></span>

<span data-ttu-id="3f709-237">É possível receber outros erros ao fazer alterações de esquema em um banco de dados que contém dados existentes.</span><span class="sxs-lookup"><span data-stu-id="3f709-237">It's possible to get other errors when making schema changes in a database that has existing data.</span></span> <span data-ttu-id="3f709-238">Se você receber erros de migração que não consegue resolver, altere o nome do banco de dados na cadeia de conexão ou exclua o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="3f709-238">If you get migration errors you can't resolve, you can either change the database name in the connection string or delete the database.</span></span> <span data-ttu-id="3f709-239">Com um novo banco de dados, não há nenhum dado a ser migrado e o comando de atualização de banco de dados terá uma probabilidade muito maior de ser concluído sem erros.</span><span class="sxs-lookup"><span data-stu-id="3f709-239">With a new database, there's no data to migrate, and the update-database command is much more likely to complete without errors.</span></span>

<span data-ttu-id="3f709-240">A abordagem mais simples é renomear o banco de dados em *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="3f709-240">The simplest approach is to rename the database in *appsettings.json*.</span></span> <span data-ttu-id="3f709-241">Na próxima vez que você executar `database update`, um novo banco de dados será criado.</span><span class="sxs-lookup"><span data-stu-id="3f709-241">The next time you run `database update`, a new database will be created.</span></span>

<span data-ttu-id="3f709-242">Para excluir um banco de dados no SSOX, clique com o botão direito do mouse no banco de dados, clique **Excluir** e, em seguida, na caixa de diálogo **Excluir Banco de Dados**, selecione **Fechar conexões existentes** e clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="3f709-242">To delete a database in SSOX, right-click the database, click **Delete**, and then in the **Delete Database** dialog box select **Close existing connections** and click **OK**.</span></span>

<span data-ttu-id="3f709-243">Para excluir um banco de dados usando a CLI, execute o comando `database drop` da CLI:</span><span class="sxs-lookup"><span data-stu-id="3f709-243">To delete a database by using the CLI, run the `database drop` CLI command:</span></span>

```console
dotnet ef database drop
```

### <a name="error-locating-sql-server-instance"></a><span data-ttu-id="3f709-244">Erro ao localizar a instância do SQL Server</span><span class="sxs-lookup"><span data-stu-id="3f709-244">Error locating SQL Server instance</span></span>

<span data-ttu-id="3f709-245">Mensagem de erro:</span><span class="sxs-lookup"><span data-stu-id="3f709-245">Error Message:</span></span>

> <span data-ttu-id="3f709-246">Ocorreu um erro relacionado à rede ou específico a uma instância ao estabelecer uma conexão com o SQL Server.</span><span class="sxs-lookup"><span data-stu-id="3f709-246">A network-related or instance-specific error occurred while establishing a connection to SQL Server.</span></span> <span data-ttu-id="3f709-247">O servidor não foi encontrado ou não estava acessível.</span><span class="sxs-lookup"><span data-stu-id="3f709-247">The server was not found or was not accessible.</span></span> <span data-ttu-id="3f709-248">Verifique se o nome da instância está correto e se o SQL Server está configurado para permitir conexões remotas.</span><span class="sxs-lookup"><span data-stu-id="3f709-248">Verify that the instance name is correct and that SQL Server is configured to allow remote connections.</span></span> <span data-ttu-id="3f709-249">(provedor: Adaptadores de Rede do SQL, erro: 26 – Erro ao localizar a instância/o servidor especificado)</span><span class="sxs-lookup"><span data-stu-id="3f709-249">(provider: SQL Network Interfaces, error: 26 - Error Locating Server/Instance Specified)</span></span>

<span data-ttu-id="3f709-250">Solução:</span><span class="sxs-lookup"><span data-stu-id="3f709-250">Solution:</span></span>

<span data-ttu-id="3f709-251">Verifique a cadeia de conexão.</span><span class="sxs-lookup"><span data-stu-id="3f709-251">Check the connection string.</span></span> <span data-ttu-id="3f709-252">Se você excluiu o arquivo de banco de dados manualmente, altere o nome do banco de dados na cadeia de caracteres de construção para começar novamente com um novo banco de dados.</span><span class="sxs-lookup"><span data-stu-id="3f709-252">If you have manually deleted the database file, change the name of the database in the construction string to start over with a new database.</span></span>

::: moniker-end

> [!div class="step-by-step"]
> [<span data-ttu-id="3f709-253">Anterior</span><span class="sxs-lookup"><span data-stu-id="3f709-253">Previous</span></span>](inheritance.md)
