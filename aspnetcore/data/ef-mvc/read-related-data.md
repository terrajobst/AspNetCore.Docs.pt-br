---
title: 'Tutorial: Ler dados relacionados - ASP.NET MVC com EF Core'
description: Neste tutorial, você lerá e exibirá dados relacionados – ou seja, os dados que o Entity Framework carrega nas propriedades de navegação.
author: tdykstra
ms.author: tdykstra
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/read-related-data
ms.openlocfilehash: 43d05d0a5f938e60cdb54a0025d8bc277059c2e3
ms.sourcegitcommit: 257cc3fe8c1d61341aa3b07e5bc0fa3d1c1c1d1c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69583437"
---
# <a name="tutorial-read-related-data---aspnet-mvc-with-ef-core"></a><span data-ttu-id="e1fda-103">Tutorial: Ler dados relacionados - ASP.NET MVC com EF Core</span><span class="sxs-lookup"><span data-stu-id="e1fda-103">Tutorial: Read related data - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="e1fda-104">No tutorial anterior, você concluiu o modelo de dados Escola.</span><span class="sxs-lookup"><span data-stu-id="e1fda-104">In the previous tutorial, you completed the School data model.</span></span> <span data-ttu-id="e1fda-105">Neste tutorial, você lerá e exibirá dados relacionados – ou seja, os dados que o Entity Framework carrega nas propriedades de navegação.</span><span class="sxs-lookup"><span data-stu-id="e1fda-105">In this tutorial, you'll read and display related data -- that is, data that the Entity Framework loads into navigation properties.</span></span>

<span data-ttu-id="e1fda-106">As ilustrações a seguir mostram as páginas com as quais você trabalhará.</span><span class="sxs-lookup"><span data-stu-id="e1fda-106">The following illustrations show the pages that you'll work with.</span></span>

![Página Índice de Cursos](read-related-data/_static/courses-index.png)

![Página Índice de Instrutores](read-related-data/_static/instructors-index.png)

<span data-ttu-id="e1fda-109">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="e1fda-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="e1fda-110">Aprender a carregar entidades relacionadas</span><span class="sxs-lookup"><span data-stu-id="e1fda-110">Learn how to load related data</span></span>
> * <span data-ttu-id="e1fda-111">Criar uma página Cursos</span><span class="sxs-lookup"><span data-stu-id="e1fda-111">Create a Courses page</span></span>
> * <span data-ttu-id="e1fda-112">Criar uma página Instrutores</span><span class="sxs-lookup"><span data-stu-id="e1fda-112">Create an Instructors page</span></span>
> * <span data-ttu-id="e1fda-113">Aprender sobre o carregamento explícito</span><span class="sxs-lookup"><span data-stu-id="e1fda-113">Learn about explicit loading</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e1fda-114">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="e1fda-114">Prerequisites</span></span>

* [<span data-ttu-id="e1fda-115">Criar um modelo de dados complexo</span><span class="sxs-lookup"><span data-stu-id="e1fda-115">Create a complex data model</span></span>](complex-data-model.md)

## <a name="learn-how-to-load-related-data"></a><span data-ttu-id="e1fda-116">Aprender a carregar entidades relacionadas</span><span class="sxs-lookup"><span data-stu-id="e1fda-116">Learn how to load related data</span></span>

<span data-ttu-id="e1fda-117">Há várias maneiras pelas quais um software ORM (Object-Relational Mapping), como o Entity Framework, pode carregar dados relacionados nas propriedades de navegação de uma entidade:</span><span class="sxs-lookup"><span data-stu-id="e1fda-117">There are several ways that Object-Relational Mapping (ORM) software such as Entity Framework can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="e1fda-118">Carregamento adiantado.</span><span class="sxs-lookup"><span data-stu-id="e1fda-118">Eager loading.</span></span> <span data-ttu-id="e1fda-119">Quando a entidade é lida, os dados relacionados são recuperados com ela.</span><span class="sxs-lookup"><span data-stu-id="e1fda-119">When the entity is read, related data is retrieved along with it.</span></span> <span data-ttu-id="e1fda-120">Normalmente, isso resulta em uma única consulta de junção que recupera todos os dados necessários.</span><span class="sxs-lookup"><span data-stu-id="e1fda-120">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="e1fda-121">Especifique o carregamento adiantado no Entity Framework Core usando os métodos `Include` e `ThenInclude`.</span><span class="sxs-lookup"><span data-stu-id="e1fda-121">You specify eager loading in Entity Framework Core by using the `Include` and `ThenInclude` methods.</span></span>

  ![Exemplo de carregamento adiantado](read-related-data/_static/eager-loading.png)

  <span data-ttu-id="e1fda-123">Recupere alguns dos dados em consultas separadas e o EF "corrigirá" as propriedades de navegação.</span><span class="sxs-lookup"><span data-stu-id="e1fda-123">You can retrieve some of the data in separate queries, and EF "fixes up" the navigation properties.</span></span>  <span data-ttu-id="e1fda-124">Ou seja, o EF adiciona de forma automática as entidades recuperadas separadamente no local em que pertencem nas propriedades de navegação de entidades recuperadas anteriormente.</span><span class="sxs-lookup"><span data-stu-id="e1fda-124">That is, EF automatically adds the separately retrieved entities where they belong in navigation properties of previously retrieved entities.</span></span> <span data-ttu-id="e1fda-125">Para a consulta que recupera dados relacionados, você pode usar o método `Load` em vez de um método que retorna uma lista ou um objeto, como `ToList` ou `Single`.</span><span class="sxs-lookup"><span data-stu-id="e1fda-125">For the query that retrieves related data, you can use the `Load` method instead of a method that returns a list or object, such as `ToList` or `Single`.</span></span>

  ![Exemplo de consultas separadas](read-related-data/_static/separate-queries.png)

* <span data-ttu-id="e1fda-127">Carregamento explícito.</span><span class="sxs-lookup"><span data-stu-id="e1fda-127">Explicit loading.</span></span> <span data-ttu-id="e1fda-128">Quando a entidade é lida pela primeira vez, os dados relacionados não são recuperados.</span><span class="sxs-lookup"><span data-stu-id="e1fda-128">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="e1fda-129">Você escreve o código que recupera os dados relacionados se eles são necessários.</span><span class="sxs-lookup"><span data-stu-id="e1fda-129">You write code that retrieves the related data if it's needed.</span></span> <span data-ttu-id="e1fda-130">Como no caso do carregamento adiantado com consultas separadas, o carregamento explícito resulta no envio de várias consultas ao banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e1fda-130">As in the case of eager loading with separate queries, explicit loading results in multiple queries sent to the database.</span></span> <span data-ttu-id="e1fda-131">A diferença é que, com o carregamento explícito, o código especifica as propriedades de navegação a serem carregadas.</span><span class="sxs-lookup"><span data-stu-id="e1fda-131">The difference is that with explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="e1fda-132">No Entity Framework Core 1.1, você pode usar o método `Load` para fazer o carregamento explícito.</span><span class="sxs-lookup"><span data-stu-id="e1fda-132">In Entity Framework Core 1.1 you can use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="e1fda-133">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="e1fda-133">For example:</span></span>

  ![Exemplo de carregamento explícito](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="e1fda-135">Carregamento lento.</span><span class="sxs-lookup"><span data-stu-id="e1fda-135">Lazy loading.</span></span> <span data-ttu-id="e1fda-136">Quando a entidade é lida pela primeira vez, os dados relacionados não são recuperados.</span><span class="sxs-lookup"><span data-stu-id="e1fda-136">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="e1fda-137">No entanto, na primeira vez que você tenta acessar uma propriedade de navegação, os dados necessários para essa propriedade de navegação são recuperados automaticamente.</span><span class="sxs-lookup"><span data-stu-id="e1fda-137">However, the first time you attempt to access a navigation property, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="e1fda-138">Uma consulta é enviada ao banco de dados sempre que você tenta obter dados de uma propriedade de navegação pela primeira vez.</span><span class="sxs-lookup"><span data-stu-id="e1fda-138">A query is sent to the database each time you try to get data from a navigation property for the first time.</span></span> <span data-ttu-id="e1fda-139">O Entity Framework Core 1.0 não dá suporte ao carregamento lento.</span><span class="sxs-lookup"><span data-stu-id="e1fda-139">Entity Framework Core 1.0 doesn't support lazy loading.</span></span>

### <a name="performance-considerations"></a><span data-ttu-id="e1fda-140">Considerações sobre desempenho</span><span class="sxs-lookup"><span data-stu-id="e1fda-140">Performance considerations</span></span>

<span data-ttu-id="e1fda-141">Se você sabe que precisa de dados relacionados para cada entidade recuperada, o carregamento adiantado costuma oferecer o melhor desempenho, porque uma única consulta enviada para o banco de dados é geralmente mais eficiente do que consultas separadas para cada entidade recuperada.</span><span class="sxs-lookup"><span data-stu-id="e1fda-141">If you know you need related data for every entity retrieved, eager loading often offers the best performance, because a single query sent to the database is typically more efficient than separate queries for each entity retrieved.</span></span> <span data-ttu-id="e1fda-142">Por exemplo, suponha que cada departamento tenha dez cursos relacionados.</span><span class="sxs-lookup"><span data-stu-id="e1fda-142">For example, suppose that each department has ten related courses.</span></span> <span data-ttu-id="e1fda-143">O carregamento adiantado de todos os dados relacionados resultará em apenas uma única consulta (junção) e uma única viagem de ida e volta para o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e1fda-143">Eager loading of all related data would result in just a single (join) query and a single round trip to the database.</span></span> <span data-ttu-id="e1fda-144">Uma consulta separada para cursos de cada departamento resultará em onze viagens de ida e volta para o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="e1fda-144">A separate query for courses for each department would result in eleven round trips to the database.</span></span> <span data-ttu-id="e1fda-145">As viagens de ida e volta extras para o banco de dados são especialmente prejudiciais ao desempenho quando a latência é alta.</span><span class="sxs-lookup"><span data-stu-id="e1fda-145">The extra round trips to the database are especially detrimental to performance when latency is high.</span></span>

<span data-ttu-id="e1fda-146">Por outro lado, em alguns cenários, consultas separadas são mais eficientes.</span><span class="sxs-lookup"><span data-stu-id="e1fda-146">On the other hand, in some scenarios separate queries is more efficient.</span></span> <span data-ttu-id="e1fda-147">O carregamento adiantado de todos os dados relacionados em uma consulta pode fazer com que uma junção muito complexa seja gerada, que o SQL Server não consegue processar com eficiência.</span><span class="sxs-lookup"><span data-stu-id="e1fda-147">Eager loading of all related data in one query might cause a very complex join to be generated, which SQL Server can't process efficiently.</span></span> <span data-ttu-id="e1fda-148">Ou se precisar acessar as propriedades de navegação de uma entidade somente para um subconjunto de um conjunto de entidades que está sendo processado, consultas separadas poderão ter um melhor desempenho, pois o carregamento adiantado de tudo desde o início recupera mais dados do que você precisa.</span><span class="sxs-lookup"><span data-stu-id="e1fda-148">Or if you need to access an entity's navigation properties only for a subset of a set of the entities you're processing, separate queries might perform better because eager loading of everything up front would retrieve more data than you need.</span></span> <span data-ttu-id="e1fda-149">Se o desempenho for crítico, será melhor testar o desempenho das duas maneiras para fazer a melhor escolha.</span><span class="sxs-lookup"><span data-stu-id="e1fda-149">If performance is critical, it's best to test performance both ways in order to make the best choice.</span></span>

## <a name="create-a-courses-page"></a><span data-ttu-id="e1fda-150">Criar uma página Cursos</span><span class="sxs-lookup"><span data-stu-id="e1fda-150">Create a Courses page</span></span>

<span data-ttu-id="e1fda-151">A entidade Course inclui uma propriedade de navegação que contém a entidade Department do departamento ao qual o curso é atribuído.</span><span class="sxs-lookup"><span data-stu-id="e1fda-151">The Course entity includes a navigation property that contains the Department entity of the department that the course is assigned to.</span></span> <span data-ttu-id="e1fda-152">Para exibir o nome do departamento atribuído em uma lista de cursos, você precisa obter a propriedade Name da entidade Department que está na propriedade de navegação `Course.Department`.</span><span class="sxs-lookup"><span data-stu-id="e1fda-152">To display the name of the assigned department in a list of courses, you need to get the Name property from the Department entity that's in the `Course.Department` navigation property.</span></span>

<span data-ttu-id="e1fda-153">Crie um controlador chamado CoursesController para o tipo de entidade Course, usando as mesmas opções para o scaffolder **Controlador MVC com exibições, usando o Entity Framework** que você usou anteriormente para o controlador Alunos, conforme mostrado na seguinte ilustração:</span><span class="sxs-lookup"><span data-stu-id="e1fda-153">Create a controller named CoursesController for the Course entity type, using the same options for the **MVC Controller with views, using Entity Framework** scaffolder that you did earlier for the Students controller, as shown in the following illustration:</span></span>

![Adicionar o controlador Courses](read-related-data/_static/add-courses-controller.png)

<span data-ttu-id="e1fda-155">Abra *CoursesController.cs* e examine o método `Index`.</span><span class="sxs-lookup"><span data-stu-id="e1fda-155">Open *CoursesController.cs* and examine the `Index` method.</span></span> <span data-ttu-id="e1fda-156">O scaffolding automático especificou o carregamento adiantado para a propriedade de navegação `Department` usando o método `Include`.</span><span class="sxs-lookup"><span data-stu-id="e1fda-156">The automatic scaffolding has specified eager loading for the `Department` navigation property by using the `Include` method.</span></span>

<span data-ttu-id="e1fda-157">Substitua o método `Index` pelo seguinte código, que usa um nome mais apropriado para o `IQueryable` que retorna as entidades Course (`courses` em vez de `schoolContext`):</span><span class="sxs-lookup"><span data-stu-id="e1fda-157">Replace the `Index` method with the following code that uses a more appropriate name for the `IQueryable` that returns Course entities (`courses` instead of `schoolContext`):</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_RevisedIndexMethod)]

<span data-ttu-id="e1fda-158">Abra *Views/Courses/Index.cshtml* e substitua o código de modelo pelo código a seguir.</span><span class="sxs-lookup"><span data-stu-id="e1fda-158">Open *Views/Courses/Index.cshtml* and replace the template code with the following code.</span></span> <span data-ttu-id="e1fda-159">As alterações são realçadas:</span><span class="sxs-lookup"><span data-stu-id="e1fda-159">The changes are highlighted:</span></span>

[!code-html[](intro/samples/cu/Views/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

<span data-ttu-id="e1fda-160">Você fez as seguintes alterações no código gerado por scaffolding:</span><span class="sxs-lookup"><span data-stu-id="e1fda-160">You've made the following changes to the scaffolded code:</span></span>

* <span data-ttu-id="e1fda-161">Alterou o cabeçalho de Índice para Cursos.</span><span class="sxs-lookup"><span data-stu-id="e1fda-161">Changed the heading from Index to Courses.</span></span>

* <span data-ttu-id="e1fda-162">Adicionou uma coluna **Número** que mostra o valor da propriedade `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="e1fda-162">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="e1fda-163">Por padrão, as chaves primárias não são geradas por scaffolding porque normalmente não têm sentido para os usuários finais.</span><span class="sxs-lookup"><span data-stu-id="e1fda-163">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="e1fda-164">No entanto, nesse caso, a chave primária é significativa e você deseja mostrá-la.</span><span class="sxs-lookup"><span data-stu-id="e1fda-164">However, in this case the primary key is meaningful and you want to show it.</span></span>

* <span data-ttu-id="e1fda-165">Alterou a coluna **Departamento** para que ela exiba o nome de departamento.</span><span class="sxs-lookup"><span data-stu-id="e1fda-165">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="e1fda-166">O código exibe a propriedade `Name` da entidade Department que é carregada na propriedade de navegação `Department`:</span><span class="sxs-lookup"><span data-stu-id="e1fda-166">The code displays the `Name` property of the Department entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="e1fda-167">Execute o aplicativo e selecione a guia **Cursos** para ver a lista com nomes de departamentos.</span><span class="sxs-lookup"><span data-stu-id="e1fda-167">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Página Índice de Cursos](read-related-data/_static/courses-index.png)

## <a name="create-an-instructors-page"></a><span data-ttu-id="e1fda-169">Criar uma página Instrutores</span><span class="sxs-lookup"><span data-stu-id="e1fda-169">Create an Instructors page</span></span>

<span data-ttu-id="e1fda-170">Nesta seção, você criará um controlador e uma exibição para a entidade Instructor para exibir a página Instrutores:</span><span class="sxs-lookup"><span data-stu-id="e1fda-170">In this section, you'll create a controller and view for the Instructor entity in order to display the Instructors page:</span></span>

![Página Índice de Instrutores](read-related-data/_static/instructors-index.png)

<span data-ttu-id="e1fda-172">Essa página lê e exibe dados relacionados das seguintes maneiras:</span><span class="sxs-lookup"><span data-stu-id="e1fda-172">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="e1fda-173">A lista de instrutores exibe dados relacionados da entidade OfficeAssignment.</span><span class="sxs-lookup"><span data-stu-id="e1fda-173">The list of instructors displays related data from the OfficeAssignment entity.</span></span> <span data-ttu-id="e1fda-174">As entidades Instructor e OfficeAssignment estão em uma relação um para zero ou um.</span><span class="sxs-lookup"><span data-stu-id="e1fda-174">The Instructor and OfficeAssignment entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="e1fda-175">Você usará o carregamento adiantado para as entidades OfficeAssignment.</span><span class="sxs-lookup"><span data-stu-id="e1fda-175">You'll use eager loading for the OfficeAssignment entities.</span></span> <span data-ttu-id="e1fda-176">Conforme explicado anteriormente, o carregamento adiantado é geralmente mais eficiente quando você precisa dos dados relacionados para todas as linhas recuperadas da tabela primária.</span><span class="sxs-lookup"><span data-stu-id="e1fda-176">As explained earlier, eager loading is typically more efficient when you need the related data for all retrieved rows of the primary table.</span></span> <span data-ttu-id="e1fda-177">Nesse caso, você deseja exibir atribuições de escritório para todos os instrutores exibidos.</span><span class="sxs-lookup"><span data-stu-id="e1fda-177">In this case, you want to display office assignments for all displayed instructors.</span></span>

* <span data-ttu-id="e1fda-178">Quando o usuário seleciona um instrutor, as entidades Course relacionadas são exibidas.</span><span class="sxs-lookup"><span data-stu-id="e1fda-178">When the user selects an instructor, related Course entities are displayed.</span></span> <span data-ttu-id="e1fda-179">As entidades Instructor e Course estão em uma relação muitos para muitos.</span><span class="sxs-lookup"><span data-stu-id="e1fda-179">The Instructor and Course entities are in a many-to-many relationship.</span></span> <span data-ttu-id="e1fda-180">Você usará o carregamento adiantado para as entidades Course e suas entidades Department relacionadas.</span><span class="sxs-lookup"><span data-stu-id="e1fda-180">You'll use eager loading for the Course entities and their related Department entities.</span></span> <span data-ttu-id="e1fda-181">Nesse caso, consultas separadas podem ser mais eficientes porque você precisa de cursos somente para o instrutor selecionado.</span><span class="sxs-lookup"><span data-stu-id="e1fda-181">In this case, separate queries might be more efficient because you need courses only for the selected instructor.</span></span> <span data-ttu-id="e1fda-182">No entanto, este exemplo mostra como usar o carregamento adiantado para propriedades de navegação em entidades que estão nas propriedades de navegação.</span><span class="sxs-lookup"><span data-stu-id="e1fda-182">However, this example shows how to use eager loading for navigation properties within entities that are themselves in navigation properties.</span></span>

* <span data-ttu-id="e1fda-183">Quando o usuário seleciona um curso, dados relacionados do conjunto de entidades Enrollments são exibidos.</span><span class="sxs-lookup"><span data-stu-id="e1fda-183">When the user selects a course, related data from the Enrollments entity set is displayed.</span></span> <span data-ttu-id="e1fda-184">As entidades Course e Enrollment estão em uma relação um para muitos.</span><span class="sxs-lookup"><span data-stu-id="e1fda-184">The Course and Enrollment entities are in a one-to-many relationship.</span></span> <span data-ttu-id="e1fda-185">Você usará consultas separadas para entidades Enrollment e suas entidades Student relacionadas.</span><span class="sxs-lookup"><span data-stu-id="e1fda-185">You'll use separate queries for Enrollment entities and their related Student entities.</span></span>

### <a name="create-a-view-model-for-the-instructor-index-view"></a><span data-ttu-id="e1fda-186">Criar um modelo de exibição para a exibição Índice de Instrutor</span><span class="sxs-lookup"><span data-stu-id="e1fda-186">Create a view model for the Instructor Index view</span></span>

<span data-ttu-id="e1fda-187">A página Instrutores mostra dados de três tabelas diferentes.</span><span class="sxs-lookup"><span data-stu-id="e1fda-187">The Instructors page shows data from three different tables.</span></span> <span data-ttu-id="e1fda-188">Portanto, você criará um modelo de exibição que inclui três propriedades, cada uma contendo os dados de uma das tabelas.</span><span class="sxs-lookup"><span data-stu-id="e1fda-188">Therefore, you'll create a view model that includes three properties, each holding the data for one of the tables.</span></span>

<span data-ttu-id="e1fda-189">Na pasta *SchoolViewModels*, crie *InstructorIndexData.cs* e substitua o código existente pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="e1fda-189">In the *SchoolViewModels* folder, create *InstructorIndexData.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="create-the-instructor-controller-and-views"></a><span data-ttu-id="e1fda-190">Criar exibições e o controlador Instrutor</span><span class="sxs-lookup"><span data-stu-id="e1fda-190">Create the Instructor controller and views</span></span>

<span data-ttu-id="e1fda-191">Crie um controlador Instrutores com ações de leitura/gravação do EF, conforme mostrado na seguinte ilustração:</span><span class="sxs-lookup"><span data-stu-id="e1fda-191">Create an Instructors controller with EF read/write actions as shown in the following illustration:</span></span>

![Adicionar o controlador Instrutores](read-related-data/_static/add-instructors-controller.png)

<span data-ttu-id="e1fda-193">Abra *InstructorsController.cs* e adicione um usando a instrução para o namespace ViewModels:</span><span class="sxs-lookup"><span data-stu-id="e1fda-193">Open *InstructorsController.cs* and add a using statement for the ViewModels namespace:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Using)]

<span data-ttu-id="e1fda-194">Substitua o método Index pelo código a seguir para fazer o carregamento adiantado de dados relacionados e colocá-los no modelo de exibição.</span><span class="sxs-lookup"><span data-stu-id="e1fda-194">Replace the Index method with the following code to do eager loading of related data and put it in the view model.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EagerLoading)]

<span data-ttu-id="e1fda-195">O método aceita dados de rota opcionais (`id`) e um parâmetro de cadeia de caracteres de consulta (`courseID`) que fornece os valores de ID do curso e do instrutor selecionados.</span><span class="sxs-lookup"><span data-stu-id="e1fda-195">The method accepts optional route data (`id`) and a query string parameter (`courseID`) that provide the ID values of the selected instructor and selected course.</span></span> <span data-ttu-id="e1fda-196">Os parâmetros são fornecidos pelos hiperlinks **Selecionar** na página.</span><span class="sxs-lookup"><span data-stu-id="e1fda-196">The parameters are provided by the **Select** hyperlinks on the page.</span></span>

<span data-ttu-id="e1fda-197">O código começa com a criação de uma instância do modelo de exibição e colocando-a na lista de instrutores.</span><span class="sxs-lookup"><span data-stu-id="e1fda-197">The code begins by creating an instance of the view model and putting in it the list of instructors.</span></span> <span data-ttu-id="e1fda-198">O código especifica o carregamento adiantado para as propriedades de navegação `Instructor.OfficeAssignment` e `Instructor.CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="e1fda-198">The code specifies eager loading for the `Instructor.OfficeAssignment` and the `Instructor.CourseAssignments` navigation properties.</span></span> <span data-ttu-id="e1fda-199">Dentro da propriedade `CourseAssignments`, a propriedade `Course` é carregada e, dentro dela, as propriedades `Enrollments` e `Department` são carregadas e, dentro de cada entidade `Enrollment`, a propriedade `Student` é carregada.</span><span class="sxs-lookup"><span data-stu-id="e1fda-199">Within the `CourseAssignments` property, the `Course` property is loaded, and within that, the `Enrollments` and `Department` properties are loaded, and within each `Enrollment` entity the `Student` property is loaded.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude)]

<span data-ttu-id="e1fda-200">Como a exibição sempre exige a entidade OfficeAssignment, é mais eficiente buscar isso na mesma consulta.</span><span class="sxs-lookup"><span data-stu-id="e1fda-200">Since the view always requires the OfficeAssignment entity, it's more efficient to fetch that in the same query.</span></span> <span data-ttu-id="e1fda-201">As entidades Course são necessárias quando um instrutor é selecionado na página da Web; portanto, uma única consulta é melhor do que várias consultas apenas se a página é exibida com mais frequência com um curso selecionado do que sem ele.</span><span class="sxs-lookup"><span data-stu-id="e1fda-201">Course entities are required when an instructor is selected in the web page, so a single query is better than multiple queries only if the page is displayed more often with a course selected than without.</span></span>

<span data-ttu-id="e1fda-202">O código repete `CourseAssignments` e `Course` porque você precisa de duas propriedades de `Course`.</span><span class="sxs-lookup"><span data-stu-id="e1fda-202">The code repeats `CourseAssignments` and `Course` because you need two properties from `Course`.</span></span> <span data-ttu-id="e1fda-203">A primeira cadeia de caracteres de chamadas `ThenInclude` obtém `CourseAssignment.Course`, `Course.Enrollments` e `Enrollment.Student`.</span><span class="sxs-lookup"><span data-stu-id="e1fda-203">The first string of `ThenInclude` calls gets `CourseAssignment.Course`, `Course.Enrollments`, and `Enrollment.Student`.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=3-6)]

<span data-ttu-id="e1fda-204">Nesse ponto do código, outro `ThenInclude` se refere às propriedades de navegação de `Student`, que não é necessário.</span><span class="sxs-lookup"><span data-stu-id="e1fda-204">At that point in the code, another `ThenInclude` would be for navigation properties of `Student`, which you don't need.</span></span> <span data-ttu-id="e1fda-205">Mas a chamada a `Include` é reiniciada com propriedades `Instructor` e, portanto, você precisa passar pela cadeia novamente, dessa vez, especificando `Course.Department` em vez de `Course.Enrollments`.</span><span class="sxs-lookup"><span data-stu-id="e1fda-205">But calling `Include` starts over with `Instructor` properties, so you have to go through the chain again, this time specifying `Course.Department` instead of `Course.Enrollments`.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=7-9)]

<span data-ttu-id="e1fda-206">O código a seguir é executado quando o instrutor é selecionado.</span><span class="sxs-lookup"><span data-stu-id="e1fda-206">The following code executes when an instructor was selected.</span></span> <span data-ttu-id="e1fda-207">O instrutor selecionado é recuperado da lista de instrutores no modelo de exibição.</span><span class="sxs-lookup"><span data-stu-id="e1fda-207">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="e1fda-208">Em seguida, a propriedade `Courses` do modelo de exibição é carregada com as entidades Course da propriedade de navegação `CourseAssignments` desse instrutor.</span><span class="sxs-lookup"><span data-stu-id="e1fda-208">The view model's `Courses` property is then loaded with the Course entities from that instructor's `CourseAssignments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=56-62)]

<span data-ttu-id="e1fda-209">O método `Where` retorna uma coleção, mas nesse caso, os critérios passado para esse método resultam no retorno de apenas uma única entidade Instructor.</span><span class="sxs-lookup"><span data-stu-id="e1fda-209">The `Where` method returns a collection, but in this case the criteria passed to that method result in only a single Instructor entity being returned.</span></span> <span data-ttu-id="e1fda-210">O método `Single` converte a coleção em uma única entidade Instructor, que fornece acesso à propriedade `CourseAssignments` dessa entidade.</span><span class="sxs-lookup"><span data-stu-id="e1fda-210">The `Single` method converts the collection into a single Instructor entity, which gives you access to that entity's `CourseAssignments` property.</span></span> <span data-ttu-id="e1fda-211">A propriedade `CourseAssignments` contém entidades `CourseAssignment`, das quais você deseja apenas entidades `Course` relacionadas.</span><span class="sxs-lookup"><span data-stu-id="e1fda-211">The `CourseAssignments` property contains `CourseAssignment` entities, from which you want only the related `Course` entities.</span></span>

<span data-ttu-id="e1fda-212">Use o método `Single` em uma coleção quando souber que a coleção terá apenas um item.</span><span class="sxs-lookup"><span data-stu-id="e1fda-212">You use the `Single` method on a collection when you know the collection will have only one item.</span></span> <span data-ttu-id="e1fda-213">O método Single gera uma exceção se a coleção passada para ele está vazia ou se há mais de um item.</span><span class="sxs-lookup"><span data-stu-id="e1fda-213">The Single method throws an exception if the collection passed to it's empty or if there's more than one item.</span></span> <span data-ttu-id="e1fda-214">Uma alternativa é `SingleOrDefault`, que retorna um valor padrão (nulo, nesse caso) se a coleção está vazia.</span><span class="sxs-lookup"><span data-stu-id="e1fda-214">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span> <span data-ttu-id="e1fda-215">No entanto, nesse caso, isso ainda resultará em uma exceção (da tentativa de encontrar uma propriedade `Courses` em uma referência nula), e a mensagem de exceção menos claramente indicará a causa do problema.</span><span class="sxs-lookup"><span data-stu-id="e1fda-215">However, in this case that would still result in an exception (from trying to find a `Courses` property on a null reference), and the exception message would less clearly indicate the cause of the problem.</span></span> <span data-ttu-id="e1fda-216">Quando você chama o método `Single`, também pode passar a condição Where, em vez de chamar o método `Where` separadamente:</span><span class="sxs-lookup"><span data-stu-id="e1fda-216">When you call the `Single` method, you can also pass in the Where condition instead of calling the `Where` method separately:</span></span>

```csharp
.Single(i => i.ID == id.Value)
```

<span data-ttu-id="e1fda-217">Em vez de:</span><span class="sxs-lookup"><span data-stu-id="e1fda-217">Instead of:</span></span>

```csharp
.Where(i => i.ID == id.Value).Single()
```

<span data-ttu-id="e1fda-218">Em seguida, se um curso foi selecionado, o curso selecionado é recuperado na lista de cursos no modelo de exibição.</span><span class="sxs-lookup"><span data-stu-id="e1fda-218">Next, if a course was selected, the selected course is retrieved from the list of courses in the view model.</span></span> <span data-ttu-id="e1fda-219">Em seguida, a propriedade `Enrollments` do modelo de exibição é carregada com as entidades Enrollment da propriedade de navegação `Enrollments` desse curso.</span><span class="sxs-lookup"><span data-stu-id="e1fda-219">Then the view model's `Enrollments` property is loaded with the Enrollment entities from that course's `Enrollments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=64-69)]

### <a name="modify-the-instructor-index-view"></a><span data-ttu-id="e1fda-220">Modificar a exibição Índice de Instrutor</span><span class="sxs-lookup"><span data-stu-id="e1fda-220">Modify the Instructor Index view</span></span>

<span data-ttu-id="e1fda-221">Em *Views/Instructors/Index.cshtml*, substitua o código de modelo pelo código a seguir.</span><span class="sxs-lookup"><span data-stu-id="e1fda-221">In *Views/Instructors/Index.cshtml*, replace the template code with the following code.</span></span> <span data-ttu-id="e1fda-222">As alterações são realçadas.</span><span class="sxs-lookup"><span data-stu-id="e1fda-222">The changes are highlighted.</span></span>

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=1-64&highlight=1,3-7,15-19,24,26-31,41-54,56)]

<span data-ttu-id="e1fda-223">Você fez as seguintes alterações no código existente:</span><span class="sxs-lookup"><span data-stu-id="e1fda-223">You've made the following changes to the existing code:</span></span>

* <span data-ttu-id="e1fda-224">Alterou a classe de modelo para `InstructorIndexData`.</span><span class="sxs-lookup"><span data-stu-id="e1fda-224">Changed the model class to `InstructorIndexData`.</span></span>

* <span data-ttu-id="e1fda-225">Alterou o título de página de **Índice** para **Instrutores**.</span><span class="sxs-lookup"><span data-stu-id="e1fda-225">Changed the page title from **Index** to **Instructors**.</span></span>

* <span data-ttu-id="e1fda-226">Adicionou uma coluna **Office** que exibe `item.OfficeAssignment.Location` somente se `item.OfficeAssignment` não é nulo.</span><span class="sxs-lookup"><span data-stu-id="e1fda-226">Added an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="e1fda-227">(Como essa é uma relação um para zero ou um, pode não haver uma entidade OfficeAssignment relacionada.)</span><span class="sxs-lookup"><span data-stu-id="e1fda-227">(Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.)</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="e1fda-228">Adicionou uma coluna **Courses** que exibe os cursos ministrados por cada instrutor.</span><span class="sxs-lookup"><span data-stu-id="e1fda-228">Added a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="e1fda-229">Para obter mais informações, consulte a seção [Transição de linha explícita com @:](xref:mvc/views/razor#explicit-line-transition-with-) no artigo sobre Sintaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="e1fda-229">For more information, see the [Explicit line transition with @:](xref:mvc/views/razor#explicit-line-transition-with-) section of the Razor syntax article.</span></span>

* <span data-ttu-id="e1fda-230">Adicionou um código que adiciona `class="success"` dinamicamente ao elemento `tr` do instrutor selecionado.</span><span class="sxs-lookup"><span data-stu-id="e1fda-230">Added code that dynamically adds `class="success"` to the `tr` element of the selected instructor.</span></span> <span data-ttu-id="e1fda-231">Isso define uma cor da tela de fundo para a linha selecionada usando uma classe Bootstrap.</span><span class="sxs-lookup"><span data-stu-id="e1fda-231">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.ID == (int?)ViewData["InstructorID"])
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="e1fda-232">Adicionou um novo hiperlink rotulado **Selecionar** imediatamente antes dos outros links em cada linha, o que faz com que a ID do instrutor selecionado seja enviada para o método `Index`.</span><span class="sxs-lookup"><span data-stu-id="e1fda-232">Added a new hyperlink labeled **Select** immediately before the other links in each row, which causes the selected instructor's ID to be sent to the `Index` method.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

<span data-ttu-id="e1fda-233">Execute o aplicativo e selecione a guia **Instrutores**. A página exibe a propriedade Location das entidades OfficeAssignment relacionadas e uma célula de tabela vazia quando não há nenhuma entidade OfficeAssignment relacionada.</span><span class="sxs-lookup"><span data-stu-id="e1fda-233">Run the app and select the **Instructors** tab. The page displays the Location property of related OfficeAssignment entities and an empty table cell when there's no related OfficeAssignment entity.</span></span>

![Página Índice de Instrutores – nenhuma opção selecionada](read-related-data/_static/instructors-index-no-selection.png)

<span data-ttu-id="e1fda-235">No arquivo *Views/Instructors/Index.cshtml*, após o elemento de tabela de fechamento (ao final do arquivo), adicione o código a seguir.</span><span class="sxs-lookup"><span data-stu-id="e1fda-235">In the *Views/Instructors/Index.cshtml* file, after the closing table element (at the end of the file), add the following code.</span></span> <span data-ttu-id="e1fda-236">Esse código exibe uma lista de cursos relacionados a um instrutor quando um instrutor é selecionado.</span><span class="sxs-lookup"><span data-stu-id="e1fda-236">This code displays a list of courses related to an instructor when an instructor is selected.</span></span>

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=66-101)]

<span data-ttu-id="e1fda-237">Esse código lê a propriedade `Courses` do modelo de exibição para exibir uma lista de cursos.</span><span class="sxs-lookup"><span data-stu-id="e1fda-237">This code reads the `Courses` property of the view model to display a list of courses.</span></span> <span data-ttu-id="e1fda-238">Também fornece um hiperlink **Selecionar** que envia a ID do curso selecionado para o método de ação `Index`.</span><span class="sxs-lookup"><span data-stu-id="e1fda-238">It also provides a **Select** hyperlink that sends the ID of the selected course to the `Index` action method.</span></span>

<span data-ttu-id="e1fda-239">Atualize a página e selecione um instrutor.</span><span class="sxs-lookup"><span data-stu-id="e1fda-239">Refresh the page and select an instructor.</span></span> <span data-ttu-id="e1fda-240">Agora, você verá uma grade que exibe os cursos atribuídos ao instrutor selecionado, e para cada curso, verá o nome do departamento atribuído.</span><span class="sxs-lookup"><span data-stu-id="e1fda-240">Now you see a grid that displays courses assigned to the selected instructor, and for each course you see the name of the assigned department.</span></span>

![Página Índice de Instrutores – instrutor selecionado](read-related-data/_static/instructors-index-instructor-selected.png)

<span data-ttu-id="e1fda-242">Após o bloco de código que você acabou de adicionar, adicione o código a seguir.</span><span class="sxs-lookup"><span data-stu-id="e1fda-242">After the code block you just added, add the following code.</span></span> <span data-ttu-id="e1fda-243">Isso exibe uma lista dos alunos que estão registrados em um curso quando esse curso é selecionado.</span><span class="sxs-lookup"><span data-stu-id="e1fda-243">This displays a list of the students who are enrolled in a course when that course is selected.</span></span>

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=103-125)]

<span data-ttu-id="e1fda-244">Esse código lê a propriedade Enrollments do modelo de exibição para exibir uma lista dos alunos registrados no curso.</span><span class="sxs-lookup"><span data-stu-id="e1fda-244">This code reads the Enrollments property of the view model in order to display a list of students enrolled in the course.</span></span>

<span data-ttu-id="e1fda-245">Atualize a página novamente e selecione um instrutor.</span><span class="sxs-lookup"><span data-stu-id="e1fda-245">Refresh the page again and select an instructor.</span></span> <span data-ttu-id="e1fda-246">Em seguida, selecione um curso para ver a lista de alunos registrados e suas notas.</span><span class="sxs-lookup"><span data-stu-id="e1fda-246">Then select a course to see the list of enrolled students and their grades.</span></span>

![Página Índice de Instrutores – instrutor e curso selecionados](read-related-data/_static/instructors-index.png)

## <a name="about-explicit-loading"></a><span data-ttu-id="e1fda-248">Sobre o carregamento explícito</span><span class="sxs-lookup"><span data-stu-id="e1fda-248">About explicit loading</span></span>

<span data-ttu-id="e1fda-249">Quando você recuperou a lista de instrutores em *InstructorsController.cs*, você especificou o carregamento adiantado para a propriedade de navegação `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="e1fda-249">When you retrieved the list of instructors in *InstructorsController.cs*, you specified eager loading for the `CourseAssignments` navigation property.</span></span>

<span data-ttu-id="e1fda-250">Suponha que os usuários esperados raramente desejem ver registros em um curso e um instrutor selecionados.</span><span class="sxs-lookup"><span data-stu-id="e1fda-250">Suppose you expected users to only rarely want to see enrollments in a selected instructor and course.</span></span> <span data-ttu-id="e1fda-251">Nesse caso, talvez você deseje carregar os dados de registro somente se eles forem solicitados.</span><span class="sxs-lookup"><span data-stu-id="e1fda-251">In that case, you might want to load the enrollment data only if it's requested.</span></span> <span data-ttu-id="e1fda-252">Para ver um exemplo de como fazer carregamento explícito, substitua o método `Index` pelo código a seguir, que remove o carregamento adiantado para Enrollments e carrega essa propriedade de forma explícita.</span><span class="sxs-lookup"><span data-stu-id="e1fda-252">To see an example of how to do explicit loading, replace the `Index` method with the following code, which removes eager loading for Enrollments and loads that property explicitly.</span></span> <span data-ttu-id="e1fda-253">As alterações de código são realçadas.</span><span class="sxs-lookup"><span data-stu-id="e1fda-253">The code changes are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ExplicitLoading&highlight=23-29)]

<span data-ttu-id="e1fda-254">O novo código remove as chamadas do método *ThenInclude* para dados de registro do código que recupera as entidades do instrutor.</span><span class="sxs-lookup"><span data-stu-id="e1fda-254">The new code drops the *ThenInclude* method calls for enrollment data from the code that retrieves instructor entities.</span></span> <span data-ttu-id="e1fda-255">Se um curso e um instrutor são selecionados, o código realçado recupera entidades Enrollment para o curso selecionado e as entidades Student para cada Enrollment.</span><span class="sxs-lookup"><span data-stu-id="e1fda-255">If an instructor and course are selected, the highlighted code retrieves Enrollment entities for the selected course, and Student entities for each Enrollment.</span></span>

<span data-ttu-id="e1fda-256">Execute que o aplicativo, acesse a página Índice de Instrutores agora e você não verá nenhuma diferença no que é exibido na página, embora você tenha alterado a maneira como os dados são recuperados.</span><span class="sxs-lookup"><span data-stu-id="e1fda-256">Run the app, go to the Instructors Index page now and you'll see no difference in what's displayed on the page, although you've changed how the data is retrieved.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="e1fda-257">Obter o código</span><span class="sxs-lookup"><span data-stu-id="e1fda-257">Get the code</span></span>

[<span data-ttu-id="e1fda-258">Baixe ou exiba o aplicativo concluído.</span><span class="sxs-lookup"><span data-stu-id="e1fda-258">Download or view the completed application.</span></span>](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="e1fda-259">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="e1fda-259">Next steps</span></span>

<span data-ttu-id="e1fda-260">Neste tutorial, você:</span><span class="sxs-lookup"><span data-stu-id="e1fda-260">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="e1fda-261">Aprendeu a carregar dados relacionados</span><span class="sxs-lookup"><span data-stu-id="e1fda-261">Learned how to load related data</span></span>
> * <span data-ttu-id="e1fda-262">Criou uma página Cursos</span><span class="sxs-lookup"><span data-stu-id="e1fda-262">Created a Courses page</span></span>
> * <span data-ttu-id="e1fda-263">Criou uma página Instrutores</span><span class="sxs-lookup"><span data-stu-id="e1fda-263">Created an Instructors page</span></span>
> * <span data-ttu-id="e1fda-264">Aprendeu sobre o carregamento explícito</span><span class="sxs-lookup"><span data-stu-id="e1fda-264">Learned about explicit loading</span></span>

<span data-ttu-id="e1fda-265">Vá para o próximo tutorial para aprender a atualizar dados relacionados.</span><span class="sxs-lookup"><span data-stu-id="e1fda-265">Advance to the next tutorial to learn how to update related data.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="e1fda-266">Atualizar dados relacionados</span><span class="sxs-lookup"><span data-stu-id="e1fda-266">Update related data</span></span>](update-related-data.md)
