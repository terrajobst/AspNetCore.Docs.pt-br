---
title: Páginas Razor com o EF Core no ASP.NET Core – Ler dados relacionados – 6 de 8
author: rick-anderson
description: Neste tutorial, você lê e exibe dados relacionados – ou seja, os dados que o Entity Framework carrega nas propriedades de navegação.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: data/ef-rp/read-related-data
ms.openlocfilehash: a264cdaf0f577be6ea2043935b485f4fd16e0229
ms.sourcegitcommit: 57792e5f594db1574742588017c708350958bdf0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58264951"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---read-related-data---6-of-8"></a><span data-ttu-id="651b9-103">Páginas Razor com o EF Core no ASP.NET Core – Ler dados relacionados – 6 de 8</span><span class="sxs-lookup"><span data-stu-id="651b9-103">Razor Pages with EF Core in ASP.NET Core - Read Related Data - 6 of 8</span></span>

<span data-ttu-id="651b9-104">Por [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="651b9-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

<span data-ttu-id="651b9-105">Neste tutorial, os dados relacionados são lidos e exibidos.</span><span class="sxs-lookup"><span data-stu-id="651b9-105">In this tutorial, related data is read and displayed.</span></span> <span data-ttu-id="651b9-106">Dados relacionados são dados que o EF Core carrega nas propriedades de navegação.</span><span class="sxs-lookup"><span data-stu-id="651b9-106">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="651b9-107">Caso tenha problemas que não consiga resolver, [baixe ou exiba o aplicativo concluído.](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span><span class="sxs-lookup"><span data-stu-id="651b9-107">If you run into problems you can't solve, [download or view the completed app.](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="651b9-108">[Instruções de download](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="651b9-108">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="651b9-109">As seguintes ilustrações mostram as páginas concluídas para este tutorial:</span><span class="sxs-lookup"><span data-stu-id="651b9-109">The following illustrations show the completed pages for this tutorial:</span></span>

![Página Índice de Cursos](read-related-data/_static/courses-index.png)

![Página Índice de Instrutores](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a><span data-ttu-id="651b9-112">Carregamento adiantado, explícito e lento de dados relacionados</span><span class="sxs-lookup"><span data-stu-id="651b9-112">Eager, explicit, and lazy Loading of related data</span></span>

<span data-ttu-id="651b9-113">Há várias maneiras pelas quais o EF Core pode carregar dados relacionados nas propriedades de navegação de uma entidade:</span><span class="sxs-lookup"><span data-stu-id="651b9-113">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="651b9-114">[Carregamento adiantado](/ef/core/querying/related-data#eager-loading).</span><span class="sxs-lookup"><span data-stu-id="651b9-114">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="651b9-115">O carregamento adiantado é quando uma consulta para um tipo de entidade também carrega entidades relacionadas.</span><span class="sxs-lookup"><span data-stu-id="651b9-115">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="651b9-116">Quando a entidade é lida, seus dados relacionados são recuperados.</span><span class="sxs-lookup"><span data-stu-id="651b9-116">When the entity is read, its related data is retrieved.</span></span> <span data-ttu-id="651b9-117">Normalmente, isso resulta em uma única consulta de junção que recupera todos os dados necessários.</span><span class="sxs-lookup"><span data-stu-id="651b9-117">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="651b9-118">O EF Core emitirá várias consultas para alguns tipos de carregamento adiantado.</span><span class="sxs-lookup"><span data-stu-id="651b9-118">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="651b9-119">A emissão de várias consultas pode ser mais eficiente do que era o caso para algumas consultas no EF6 quando havia uma única consulta.</span><span class="sxs-lookup"><span data-stu-id="651b9-119">Issuing multiple queries can be more efficient than was the case for some queries in EF6 where there was a single query.</span></span> <span data-ttu-id="651b9-120">O carregamento adiantado é especificado com os métodos `Include` e `ThenInclude`.</span><span class="sxs-lookup"><span data-stu-id="651b9-120">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![Exemplo de carregamento adiantado](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="651b9-122">O carregamento adiantado envia várias consultas quando a navegação de coleção é incluída:</span><span class="sxs-lookup"><span data-stu-id="651b9-122">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="651b9-123">Uma consulta para a consulta principal</span><span class="sxs-lookup"><span data-stu-id="651b9-123">One query for the main query</span></span> 
  * <span data-ttu-id="651b9-124">Uma consulta para cada "borda" de coleção na árvore de carregamento.</span><span class="sxs-lookup"><span data-stu-id="651b9-124">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="651b9-125">Separe consultas com `Load`: os dados podem ser recuperados em consultas separadas e o EF Core "corrige" as propriedades de navegação.</span><span class="sxs-lookup"><span data-stu-id="651b9-125">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="651b9-126">"Correção" significa que o EF Core popula automaticamente as propriedades de navegação.</span><span class="sxs-lookup"><span data-stu-id="651b9-126">"fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="651b9-127">A separação de consultas com `Load` é mais parecida com o carregamento explícito do que com o carregamento adiantado.</span><span class="sxs-lookup"><span data-stu-id="651b9-127">Separate queries with `Load` is more like explict loading than eager loading.</span></span>

  ![Exemplo de consultas separadas](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="651b9-129">Observação: o EF Core corrige automaticamente as propriedades de navegação para outras entidades que foram carregadas anteriormente na instância do contexto.</span><span class="sxs-lookup"><span data-stu-id="651b9-129">Note: EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="651b9-130">Mesmo se os dados de uma propriedade de navegação *não* foram incluídos de forma explícita, a propriedade ainda pode ser populada se algumas ou todas as entidades relacionadas foram carregadas anteriormente.</span><span class="sxs-lookup"><span data-stu-id="651b9-130">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="651b9-131">[Carregamento explícito](/ef/core/querying/related-data#explicit-loading).</span><span class="sxs-lookup"><span data-stu-id="651b9-131">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="651b9-132">Quando a entidade é lida pela primeira vez, os dados relacionados não são recuperados.</span><span class="sxs-lookup"><span data-stu-id="651b9-132">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="651b9-133">Um código precisa ser escrito para recuperar os dados relacionados quando eles forem necessários.</span><span class="sxs-lookup"><span data-stu-id="651b9-133">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="651b9-134">O carregamento explícito com consultas separadas resulta no envio de várias consultas ao BD.</span><span class="sxs-lookup"><span data-stu-id="651b9-134">Explicit loading with separate queries results in multiple queries sent to the DB.</span></span> <span data-ttu-id="651b9-135">Com o carregamento explícito, o código especifica as propriedades de navegação a serem carregadas.</span><span class="sxs-lookup"><span data-stu-id="651b9-135">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="651b9-136">Use o método `Load` para fazer o carregamento explícito.</span><span class="sxs-lookup"><span data-stu-id="651b9-136">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="651b9-137">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="651b9-137">For example:</span></span>

  ![Exemplo de carregamento explícito](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="651b9-139">[Carregamento lento](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="651b9-139">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="651b9-140">[O carregamento lento foi adicionado ao EF Core na versão 2.1](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="651b9-140">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="651b9-141">Quando a entidade é lida pela primeira vez, os dados relacionados não são recuperados.</span><span class="sxs-lookup"><span data-stu-id="651b9-141">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="651b9-142">Na primeira vez que uma propriedade de navegação é acessada, os dados necessários para essa propriedade de navegação são recuperados automaticamente.</span><span class="sxs-lookup"><span data-stu-id="651b9-142">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="651b9-143">Uma consulta é enviada para o BD sempre que uma propriedade de navegação é acessada pela primeira vez.</span><span class="sxs-lookup"><span data-stu-id="651b9-143">A query is sent to the DB each time a navigation property is accessed for the first time.</span></span>

* <span data-ttu-id="651b9-144">O operador `Select` carrega somente os dados relacionados necessários.</span><span class="sxs-lookup"><span data-stu-id="651b9-144">The `Select` operator loads only the related data needed.</span></span>

## <a name="create-a-course-page-that-displays-department-name"></a><span data-ttu-id="651b9-145">Criar uma página Course que exibe o nome do departamento</span><span class="sxs-lookup"><span data-stu-id="651b9-145">Create a Course page that displays department name</span></span>

<span data-ttu-id="651b9-146">A entidade Course inclui uma propriedade de navegação que contém a entidade `Department`.</span><span class="sxs-lookup"><span data-stu-id="651b9-146">The Course entity includes a navigation property that contains the `Department` entity.</span></span> <span data-ttu-id="651b9-147">A entidade `Department` contém o departamento ao qual o curso é atribuído.</span><span class="sxs-lookup"><span data-stu-id="651b9-147">The `Department` entity contains the department that the course is assigned to.</span></span>

<span data-ttu-id="651b9-148">Para exibir o nome do departamento atribuído em uma lista de cursos:</span><span class="sxs-lookup"><span data-stu-id="651b9-148">To display the name of the assigned department in a list of courses:</span></span>

* <span data-ttu-id="651b9-149">Obtenha a propriedade `Name` da entidade `Department`.</span><span class="sxs-lookup"><span data-stu-id="651b9-149">Get the `Name` property from the `Department` entity.</span></span>
* <span data-ttu-id="651b9-150">A entidade `Department` é obtida da propriedade de navegação `Course.Department`.</span><span class="sxs-lookup"><span data-stu-id="651b9-150">The `Department` entity comes from the `Course.Department` navigation property.</span></span>

![Course.Department](read-related-data/_static/dep-crs.png)

<a name="scaffold"></a>

### <a name="scaffold-the-course-model"></a><span data-ttu-id="651b9-152">Gerar o modelo Curso por scaffolding</span><span class="sxs-lookup"><span data-stu-id="651b9-152">Scaffold the Course model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="651b9-153">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="651b9-153">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="651b9-154">Siga as instruções em [Gere um modelo de aluno por scaffold](xref:data/ef-rp/intro#scaffold-the-student-model) e use `Course` para a classe de modelo.</span><span class="sxs-lookup"><span data-stu-id="651b9-154">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Course` for the model class.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="651b9-155">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="651b9-155">.NET Core CLI</span></span>](#tab/netcore-cli)

 <span data-ttu-id="651b9-156">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="651b9-156">Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

------

<span data-ttu-id="651b9-157">O comando anterior gera o modelo `Course` por scaffolding.</span><span class="sxs-lookup"><span data-stu-id="651b9-157">The preceding command scaffolds the `Course` model.</span></span> <span data-ttu-id="651b9-158">Abra o projeto no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="651b9-158">Open the project in Visual Studio.</span></span>

<span data-ttu-id="651b9-159">Abra *Pages/Courses/Index.cshtml.cs* e examine o método `OnGetAsync`.</span><span class="sxs-lookup"><span data-stu-id="651b9-159">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="651b9-160">O mecanismo de scaffolding especificou o carregamento adiantado para a propriedade de navegação `Department`.</span><span class="sxs-lookup"><span data-stu-id="651b9-160">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="651b9-161">O método `Include` especifica o carregamento adiantado.</span><span class="sxs-lookup"><span data-stu-id="651b9-161">The `Include` method specifies eager loading.</span></span>

<span data-ttu-id="651b9-162">Execute o aplicativo e selecione o link **Cursos**.</span><span class="sxs-lookup"><span data-stu-id="651b9-162">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="651b9-163">A coluna de departamento exibe a `DepartmentID`, que não é útil.</span><span class="sxs-lookup"><span data-stu-id="651b9-163">The department column displays the `DepartmentID`, which isn't useful.</span></span>

<span data-ttu-id="651b9-164">Atualize o método `OnGetAsync` pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="651b9-164">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod)]

<span data-ttu-id="651b9-165">O código anterior adiciona `AsNoTracking`.</span><span class="sxs-lookup"><span data-stu-id="651b9-165">The preceding code adds `AsNoTracking`.</span></span> <span data-ttu-id="651b9-166">`AsNoTracking` melhora o desempenho porque as entidades retornadas não são controladas.</span><span class="sxs-lookup"><span data-stu-id="651b9-166">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="651b9-167">As entidades não são controladas porque elas não são atualizadas no contexto atual.</span><span class="sxs-lookup"><span data-stu-id="651b9-167">The entities are not tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="651b9-168">Atualize *Pages/Courses/Index.cshtml* com a seguinte marcação realçada:</span><span class="sxs-lookup"><span data-stu-id="651b9-168">Update *Pages/Courses/Index.cshtml* with the following highlighted markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

<span data-ttu-id="651b9-169">As seguintes alterações foram feitas na biblioteca gerada por código em scaffolding:</span><span class="sxs-lookup"><span data-stu-id="651b9-169">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="651b9-170">Alterou o cabeçalho de Índice para Cursos.</span><span class="sxs-lookup"><span data-stu-id="651b9-170">Changed the heading from Index to Courses.</span></span>
* <span data-ttu-id="651b9-171">Adicionou uma coluna **Número** que mostra o valor da propriedade `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="651b9-171">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="651b9-172">Por padrão, as chaves primárias não são geradas por scaffolding porque normalmente não têm sentido para os usuários finais.</span><span class="sxs-lookup"><span data-stu-id="651b9-172">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="651b9-173">No entanto, nesse caso, a chave primária é significativa.</span><span class="sxs-lookup"><span data-stu-id="651b9-173">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="651b9-174">Alterou a coluna **Departamento** para que ela exiba o nome de departamento.</span><span class="sxs-lookup"><span data-stu-id="651b9-174">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="651b9-175">O código exibe a propriedade `Name` da entidade `Department` que é carregada na propriedade de navegação `Department`:</span><span class="sxs-lookup"><span data-stu-id="651b9-175">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="651b9-176">Execute o aplicativo e selecione a guia **Cursos** para ver a lista com nomes de departamentos.</span><span class="sxs-lookup"><span data-stu-id="651b9-176">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Página Índice de Cursos](read-related-data/_static/courses-index.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="651b9-178">Carregando dados relacionados com Select</span><span class="sxs-lookup"><span data-stu-id="651b9-178">Loading related data with Select</span></span>

<span data-ttu-id="651b9-179">O método `OnGetAsync` carrega dados relacionados com o método `Include`:</span><span class="sxs-lookup"><span data-stu-id="651b9-179">The `OnGetAsync` method loads related data with the `Include` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="651b9-180">O operador `Select` carrega somente os dados relacionados necessários.</span><span class="sxs-lookup"><span data-stu-id="651b9-180">The `Select` operator loads only the related data needed.</span></span> <span data-ttu-id="651b9-181">Para itens únicos, como o `Department.Name`, ele usa um SQL INNER JOIN.</span><span class="sxs-lookup"><span data-stu-id="651b9-181">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="651b9-182">Para coleções, ele usa outro acesso ao banco de dados, assim como o operador `Include` em coleções.</span><span class="sxs-lookup"><span data-stu-id="651b9-182">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="651b9-183">O seguinte código carrega dados relacionados com o método `Select`:</span><span class="sxs-lookup"><span data-stu-id="651b9-183">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="651b9-184">O `CourseViewModel`:</span><span class="sxs-lookup"><span data-stu-id="651b9-184">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="651b9-185">Consulte [IndexSelect.cshtml](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) e [IndexSelect.cshtml.cs](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) para obter um exemplo completo.</span><span class="sxs-lookup"><span data-stu-id="651b9-185">See [IndexSelect.cshtml](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a><span data-ttu-id="651b9-186">Criar uma página Instrutores que mostra Cursos e Registros</span><span class="sxs-lookup"><span data-stu-id="651b9-186">Create an Instructors page that shows Courses and Enrollments</span></span>

<span data-ttu-id="651b9-187">Nesta seção, a página Instrutores é criada.</span><span class="sxs-lookup"><span data-stu-id="651b9-187">In this section, the Instructors page is created.</span></span>

<a name="IP"></a>
<span data-ttu-id="651b9-188">![Página Índice de Instrutores](read-related-data/_static/instructors-index.png)</span><span class="sxs-lookup"><span data-stu-id="651b9-188">![Instructors Index page](read-related-data/_static/instructors-index.png)</span></span>

<span data-ttu-id="651b9-189">Essa página lê e exibe dados relacionados das seguintes maneiras:</span><span class="sxs-lookup"><span data-stu-id="651b9-189">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="651b9-190">A lista de instrutores exibe dados relacionados da entidade `OfficeAssignment` (Office na imagem anterior).</span><span class="sxs-lookup"><span data-stu-id="651b9-190">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="651b9-191">As entidades `Instructor` e `OfficeAssignment` estão em uma relação um para zero ou um.</span><span class="sxs-lookup"><span data-stu-id="651b9-191">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="651b9-192">O carregamento adiantado é usado para as entidades `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="651b9-192">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="651b9-193">O carregamento adiantado costuma ser mais eficiente quando os dados relacionados precisam ser exibidos.</span><span class="sxs-lookup"><span data-stu-id="651b9-193">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="651b9-194">Nesse caso, as atribuições de escritório para os instrutores são exibidas.</span><span class="sxs-lookup"><span data-stu-id="651b9-194">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="651b9-195">Quando o usuário seleciona um instrutor (Pedro na imagem anterior), as entidades `Course` relacionadas são exibidas.</span><span class="sxs-lookup"><span data-stu-id="651b9-195">When the user selects an instructor (Harui in the preceding image), related `Course` entities are displayed.</span></span> <span data-ttu-id="651b9-196">As entidades `Instructor` e `Course` estão em uma relação muitos para muitos.</span><span class="sxs-lookup"><span data-stu-id="651b9-196">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="651b9-197">O carregamento adiantado é usado para entidades `Course` e suas entidades `Department` relacionadas.</span><span class="sxs-lookup"><span data-stu-id="651b9-197">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="651b9-198">Nesse caso, consultas separadas podem ser mais eficientes porque somente os cursos para o instrutor selecionado são necessários.</span><span class="sxs-lookup"><span data-stu-id="651b9-198">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="651b9-199">Este exemplo mostra como usar o carregamento adiantado para propriedades de navegação em entidades que estão nas propriedades de navegação.</span><span class="sxs-lookup"><span data-stu-id="651b9-199">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="651b9-200">Quando o usuário seleciona um curso (Química na imagem anterior), os dados relacionados da entidade `Enrollments` são exibidos.</span><span class="sxs-lookup"><span data-stu-id="651b9-200">When the user selects a course (Chemistry in the preceding image), related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="651b9-201">Na imagem anterior, o nome do aluno e a nota são exibidos.</span><span class="sxs-lookup"><span data-stu-id="651b9-201">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="651b9-202">As entidades `Course` e `Enrollment` estão em uma relação um-para-muitos.</span><span class="sxs-lookup"><span data-stu-id="651b9-202">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model-for-the-instructor-index-view"></a><span data-ttu-id="651b9-203">Criar um modelo de exibição para a exibição Índice de Instrutor</span><span class="sxs-lookup"><span data-stu-id="651b9-203">Create a view model for the Instructor Index view</span></span>

<span data-ttu-id="651b9-204">A página Instrutores mostra dados de três tabelas diferentes.</span><span class="sxs-lookup"><span data-stu-id="651b9-204">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="651b9-205">É criado um modelo de exibição que inclui as três entidades que representam as três tabelas.</span><span class="sxs-lookup"><span data-stu-id="651b9-205">A view model is created that includes the three entities representing the three tables.</span></span>

<span data-ttu-id="651b9-206">Na pasta *SchoolViewModels*, crie *InstructorIndexData.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="651b9-206">In the *SchoolViewModels* folder, create *InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-the-instructor-model"></a><span data-ttu-id="651b9-207">Gerar o modelo Instrutor por scaffolding</span><span class="sxs-lookup"><span data-stu-id="651b9-207">Scaffold the Instructor model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="651b9-208">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="651b9-208">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="651b9-209">Siga as instruções em [Gere um modelo de aluno por scaffold](xref:data/ef-rp/intro#scaffold-the-student-model) e use `Instructor` para a classe de modelo.</span><span class="sxs-lookup"><span data-stu-id="651b9-209">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Instructor` for the model class.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="651b9-210">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="651b9-210">.NET Core CLI</span></span>](#tab/netcore-cli)

 <span data-ttu-id="651b9-211">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="651b9-211">Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

------

<span data-ttu-id="651b9-212">O comando anterior gera o modelo `Instructor` por scaffolding.</span><span class="sxs-lookup"><span data-stu-id="651b9-212">The preceding command scaffolds the `Instructor` model.</span></span> <span data-ttu-id="651b9-213">Execute o aplicativo e navegue para a página Instrutores.</span><span class="sxs-lookup"><span data-stu-id="651b9-213">Run the app and navigate to the instructors page.</span></span>

<span data-ttu-id="651b9-214">Substitua *Pages/Instructors/Index.cshtml.cs* pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="651b9-214">Replace *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,18-99)]

<span data-ttu-id="651b9-215">O método `OnGetAsync` aceita dados de rota opcionais para a ID do instrutor selecionado.</span><span class="sxs-lookup"><span data-stu-id="651b9-215">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="651b9-216">Examine a consulta no arquivo *Pages/Instructors/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="651b9-216">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="651b9-217">A consulta tem duas inclusões:</span><span class="sxs-lookup"><span data-stu-id="651b9-217">The query has two includes:</span></span>

* <span data-ttu-id="651b9-218">`OfficeAssignment`: mostrado na [exibição de instrutores](#IP).</span><span class="sxs-lookup"><span data-stu-id="651b9-218">`OfficeAssignment`: Displayed in the [instructors view](#IP).</span></span>
* <span data-ttu-id="651b9-219">`CourseAssignments`: que mostra os cursos ministrados.</span><span class="sxs-lookup"><span data-stu-id="651b9-219">`CourseAssignments`: Which brings in the courses taught.</span></span>

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="651b9-220">Atualizar a página Índice de instrutores</span><span class="sxs-lookup"><span data-stu-id="651b9-220">Update the instructors Index page</span></span>

<span data-ttu-id="651b9-221">Atualize *Pages/Instructors/Index.cshtml* com a seguinte marcação:</span><span class="sxs-lookup"><span data-stu-id="651b9-221">Update *Pages/Instructors/Index.cshtml* with the following markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=1-65&highlight=1,5,8,16-21,25-32,43-57)]

<span data-ttu-id="651b9-222">A marcação anterior faz as seguintes alterações:</span><span class="sxs-lookup"><span data-stu-id="651b9-222">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="651b9-223">Atualiza a diretiva `page` de `@page` para `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="651b9-223">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="651b9-224">`"{id:int?}"` é um modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="651b9-224">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="651b9-225">O modelo de rota altera cadeias de consulta de inteiro na URL para dados de rota.</span><span class="sxs-lookup"><span data-stu-id="651b9-225">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="651b9-226">Por exemplo, clicar no link **Selecionar** de um o instrutor apenas com a diretiva `@page` produz uma URL semelhante à seguinte:</span><span class="sxs-lookup"><span data-stu-id="651b9-226">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `http://localhost:1234/Instructors?id=2`

  <span data-ttu-id="651b9-227">Quando a diretiva de página é `@page "{id:int?}"`, a URL anterior é:</span><span class="sxs-lookup"><span data-stu-id="651b9-227">When the page directive is `@page "{id:int?}"`, the previous URL is:</span></span>

  `http://localhost:1234/Instructors/2`

* <span data-ttu-id="651b9-228">O título de página é **Instrutores**.</span><span class="sxs-lookup"><span data-stu-id="651b9-228">Page title is **Instructors**.</span></span>
* <span data-ttu-id="651b9-229">Adicionou uma coluna **Office** que exibe `item.OfficeAssignment.Location` somente se `item.OfficeAssignment` não é nulo.</span><span class="sxs-lookup"><span data-stu-id="651b9-229">Added an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="651b9-230">Como essa é uma relação um para zero ou um, pode não haver uma entidade OfficeAssignment relacionada.</span><span class="sxs-lookup"><span data-stu-id="651b9-230">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="651b9-231">Adicionou uma coluna **Courses** que exibe os cursos ministrados por cada instrutor.</span><span class="sxs-lookup"><span data-stu-id="651b9-231">Added a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="651b9-232">Consulte [Transição de linha explícita com `@:`](xref:mvc/views/razor#explicit-line-transition-with-) para obter mais informações sobre essa sintaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="651b9-232">See [Explicit Line Transition with `@:`](xref:mvc/views/razor#explicit-line-transition-with-) for more about this razor syntax.</span></span>

* <span data-ttu-id="651b9-233">Adicionou um código que adiciona `class="success"` dinamicamente ao elemento `tr` do instrutor selecionado.</span><span class="sxs-lookup"><span data-stu-id="651b9-233">Added code that dynamically adds `class="success"` to the `tr` element of the selected instructor.</span></span> <span data-ttu-id="651b9-234">Isso define uma cor da tela de fundo para a linha selecionada usando uma classe Bootstrap.</span><span class="sxs-lookup"><span data-stu-id="651b9-234">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="651b9-235">Adicionou um novo hiperlink rotulado **Selecionar**.</span><span class="sxs-lookup"><span data-stu-id="651b9-235">Added a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="651b9-236">Este link envia a ID do instrutor selecionado para o método `Index` e define uma cor da tela de fundo.</span><span class="sxs-lookup"><span data-stu-id="651b9-236">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

<span data-ttu-id="651b9-237">Execute o aplicativo e selecione a guia **Instrutores**. A página exibe o `Location` (escritório) da entidade `OfficeAssignment` relacionada.</span><span class="sxs-lookup"><span data-stu-id="651b9-237">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="651b9-238">Se OfficeAssignment é nulo, uma célula de tabela vazia é exibida.</span><span class="sxs-lookup"><span data-stu-id="651b9-238">If OfficeAssignment\` is null, an empty table cell is displayed.</span></span>

![Página Índice de Instrutores – nenhuma opção selecionada](read-related-data/_static/instructors-index-no-selection.png)

<span data-ttu-id="651b9-240">Clique no link **Selecionar**.</span><span class="sxs-lookup"><span data-stu-id="651b9-240">Click on the **Select** link.</span></span> <span data-ttu-id="651b9-241">O estilo de linha é alterado.</span><span class="sxs-lookup"><span data-stu-id="651b9-241">The row style changes.</span></span>

### <a name="add-courses-taught-by-selected-instructor"></a><span data-ttu-id="651b9-242">Adicionar cursos ministrados pelo instrutor selecionado</span><span class="sxs-lookup"><span data-stu-id="651b9-242">Add courses taught by selected instructor</span></span>

<span data-ttu-id="651b9-243">Atualize o método `OnGetAsync` em *Pages/Instructors/Index.cshtml.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="651b9-243">Update the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_OnGetAsync&highlight=1,8,16-999)]

<span data-ttu-id="651b9-244">Adicione `public int CourseID { get; set; }`</span><span class="sxs-lookup"><span data-stu-id="651b9-244">Add `public int CourseID { get; set; }`</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_1&highlight=12)]

<span data-ttu-id="651b9-245">Examine a consulta atualizada:</span><span class="sxs-lookup"><span data-stu-id="651b9-245">Examine the updated query:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="651b9-246">A consulta anterior adiciona as entidades `Department`.</span><span class="sxs-lookup"><span data-stu-id="651b9-246">The preceding query adds the `Department` entities.</span></span>

<span data-ttu-id="651b9-247">O código a seguir é executado quando o instrutor é selecionado (`id != null`).</span><span class="sxs-lookup"><span data-stu-id="651b9-247">The following code executes when an instructor is selected (`id != null`).</span></span> <span data-ttu-id="651b9-248">O instrutor selecionado é recuperado da lista de instrutores no modelo de exibição.</span><span class="sxs-lookup"><span data-stu-id="651b9-248">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="651b9-249">Em seguida, a propriedade `Courses` do modelo de exibição é carregada com as entidades `Course` da propriedade de navegação `CourseAssignments` desse instrutor.</span><span class="sxs-lookup"><span data-stu-id="651b9-249">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ID)]

<span data-ttu-id="651b9-250">O método `Where` retorna uma coleção.</span><span class="sxs-lookup"><span data-stu-id="651b9-250">The `Where` method returns a collection.</span></span> <span data-ttu-id="651b9-251">No método `Where` anterior, uma única entidade `Instructor` é retornada.</span><span class="sxs-lookup"><span data-stu-id="651b9-251">In the preceding `Where` method, only a single `Instructor` entity is returned.</span></span> <span data-ttu-id="651b9-252">O método `Single` converte a coleção em uma única entidade `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="651b9-252">The `Single` method converts the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="651b9-253">A entidade `Instructor` fornece acesso à propriedade `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="651b9-253">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="651b9-254">`CourseAssignments` fornece acesso às entidades `Course` relacionadas.</span><span class="sxs-lookup"><span data-stu-id="651b9-254">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![Instrutor para Cursos m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="651b9-256">O método `Single` é usado em uma coleção quando a coleção tem apenas um item.</span><span class="sxs-lookup"><span data-stu-id="651b9-256">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="651b9-257">O método `Single` gera uma exceção se a coleção está vazia ou se há mais de um item.</span><span class="sxs-lookup"><span data-stu-id="651b9-257">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="651b9-258">Uma alternativa é `SingleOrDefault`, que retorna um valor padrão (nulo, nesse caso) se a coleção está vazia.</span><span class="sxs-lookup"><span data-stu-id="651b9-258">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span> <span data-ttu-id="651b9-259">O uso de `SingleOrDefault` é uma coleção vazia:</span><span class="sxs-lookup"><span data-stu-id="651b9-259">Using `SingleOrDefault` on an empty collection:</span></span>

* <span data-ttu-id="651b9-260">Resulta em uma exceção (da tentativa de encontrar uma propriedade `Courses` em uma referência nula).</span><span class="sxs-lookup"><span data-stu-id="651b9-260">Results in an exception (from trying to find a `Courses` property on a null reference).</span></span>
* <span data-ttu-id="651b9-261">A mensagem de exceção indica menos claramente a causa do problema.</span><span class="sxs-lookup"><span data-stu-id="651b9-261">The exception message would less clearly indicate the cause of the problem.</span></span>

<span data-ttu-id="651b9-262">O seguinte código popula a propriedade `Enrollments` do modelo de exibição quando um curso é selecionado:</span><span class="sxs-lookup"><span data-stu-id="651b9-262">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_courseID)]

<span data-ttu-id="651b9-263">Adicione a seguinte marcação ao final do Razor Page *Pages/Courses/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="651b9-263">Add the following markup to the end of the *Pages/Instructors/Index.cshtml* Razor Page:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=60-102&highlight=7-999)]

<span data-ttu-id="651b9-264">A marcação anterior exibe uma lista de cursos relacionados a um instrutor quando um instrutor é selecionado.</span><span class="sxs-lookup"><span data-stu-id="651b9-264">The preceding markup displays a list of courses related to an instructor when an instructor is selected.</span></span>

<span data-ttu-id="651b9-265">Teste o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="651b9-265">Test the app.</span></span> <span data-ttu-id="651b9-266">Clique em um link **Selecionar** na página Instrutores.</span><span class="sxs-lookup"><span data-stu-id="651b9-266">Click on a **Select** link on the instructors page.</span></span>

![Página Índice de Instrutores – instrutor selecionado](read-related-data/_static/instructors-index-instructor-selected.png)

### <a name="show-student-data"></a><span data-ttu-id="651b9-268">Mostrar dados de alunos</span><span class="sxs-lookup"><span data-stu-id="651b9-268">Show student data</span></span>

<span data-ttu-id="651b9-269">Nesta seção, o aplicativo é atualizado para mostrar os dados de alunos de um curso selecionado.</span><span class="sxs-lookup"><span data-stu-id="651b9-269">In this section, the app is updated to show the student data for a selected course.</span></span>

<span data-ttu-id="651b9-270">Atualize a consulta no método `OnGetAsync` em *Pages/Instructors/Index.cshtml.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="651b9-270">Update the query in the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="651b9-271">Atualize *Pages/Instructors/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="651b9-271">Update *Pages/Instructors/Index.cshtml*.</span></span> <span data-ttu-id="651b9-272">Adicione a seguinte marcação ao final do arquivo:</span><span class="sxs-lookup"><span data-stu-id="651b9-272">Add the following markup to the end of the file:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=103-)]

<span data-ttu-id="651b9-273">A marcação anterior exibe uma lista dos alunos registrados no curso selecionado.</span><span class="sxs-lookup"><span data-stu-id="651b9-273">The preceding markup displays a list of the students who are enrolled in the selected course.</span></span>

<span data-ttu-id="651b9-274">Atualize a página e selecione um instrutor.</span><span class="sxs-lookup"><span data-stu-id="651b9-274">Refresh the page and select an instructor.</span></span> <span data-ttu-id="651b9-275">Selecione um curso para ver a lista de alunos registrados e suas notas.</span><span class="sxs-lookup"><span data-stu-id="651b9-275">Select a course to see the list of enrolled students and their grades.</span></span>

![Página Índice de Instrutores – instrutor e curso selecionados](read-related-data/_static/instructors-index.png)

## <a name="using-single"></a><span data-ttu-id="651b9-277">Usando Single</span><span class="sxs-lookup"><span data-stu-id="651b9-277">Using Single</span></span>

<span data-ttu-id="651b9-278">O método `Single` pode passar a condição `Where` em vez de chamar o método `Where` separadamente:</span><span class="sxs-lookup"><span data-stu-id="651b9-278">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="651b9-279">A abordagem `Single` anterior não oferece nenhum benefício em relação ao uso de `Where`.</span><span class="sxs-lookup"><span data-stu-id="651b9-279">The preceding `Single` approach provides no benefits over using `Where`.</span></span> <span data-ttu-id="651b9-280">Alguns desenvolvedores preferem o estilo de abordagem `Single`.</span><span class="sxs-lookup"><span data-stu-id="651b9-280">Some developers prefer the `Single` approach style.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="651b9-281">Carregamento explícito</span><span class="sxs-lookup"><span data-stu-id="651b9-281">Explicit loading</span></span>

<span data-ttu-id="651b9-282">O código atual especifica o carregamento adiantado para `Enrollments` e `Students`:</span><span class="sxs-lookup"><span data-stu-id="651b9-282">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="651b9-283">Suponha que os usuários raramente desejem ver registros em um curso.</span><span class="sxs-lookup"><span data-stu-id="651b9-283">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="651b9-284">Nesse caso, uma otimização será carregar apenas os dados de registro se eles forem solicitados.</span><span class="sxs-lookup"><span data-stu-id="651b9-284">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="651b9-285">Nesta seção, o `OnGetAsync` é atualizado para usar o carregamento explícito de `Enrollments` e `Students`.</span><span class="sxs-lookup"><span data-stu-id="651b9-285">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="651b9-286">Atualize o `OnGetAsync` com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="651b9-286">Update the `OnGetAsync` with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexXp.cshtml.cs?name=snippet_OnGetAsync&highlight=9-13,29-35)]

<span data-ttu-id="651b9-287">O código anterior remove as chamadas do método *ThenInclude* para dados de registro e de alunos.</span><span class="sxs-lookup"><span data-stu-id="651b9-287">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="651b9-288">Se um curso é selecionado, o código realçado recupera:</span><span class="sxs-lookup"><span data-stu-id="651b9-288">If a course is selected, the highlighted code retrieves:</span></span>

* <span data-ttu-id="651b9-289">As entidades `Enrollment` para o curso selecionado.</span><span class="sxs-lookup"><span data-stu-id="651b9-289">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="651b9-290">As entidades `Student` para cada `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="651b9-290">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="651b9-291">Observe que o código anterior comenta `.AsNoTracking()`.</span><span class="sxs-lookup"><span data-stu-id="651b9-291">Notice the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="651b9-292">As propriedades de navegação apenas podem ser carregadas de forma explícita para entidades controladas.</span><span class="sxs-lookup"><span data-stu-id="651b9-292">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="651b9-293">Teste o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="651b9-293">Test the app.</span></span> <span data-ttu-id="651b9-294">De uma perspectiva dos usuários, o aplicativo se comporta de forma idêntica à versão anterior.</span><span class="sxs-lookup"><span data-stu-id="651b9-294">From a users perspective, the app behaves identically to the previous version.</span></span>

<span data-ttu-id="651b9-295">O próximo tutorial mostra como atualizar os dados relacionados.</span><span class="sxs-lookup"><span data-stu-id="651b9-295">The next tutorial shows how to update related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="651b9-296">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="651b9-296">Additional resources</span></span>

* [<span data-ttu-id="651b9-297">Versão do YouTube deste tutorial (parte 1)</span><span class="sxs-lookup"><span data-stu-id="651b9-297">YouTube version of this tutorial (part1)</span></span>](https://www.youtube.com/watch?v=PzKimUDmrvE)
* [<span data-ttu-id="651b9-298">Versão do YouTube deste tutorial (parte 2)</span><span class="sxs-lookup"><span data-stu-id="651b9-298">YouTube version of this tutorial (part2)</span></span>](https://www.youtube.com/watch?v=xvDDrIHv5ko)

>[!div class="step-by-step"]
><span data-ttu-id="651b9-299">[Anterior](xref:data/ef-rp/complex-data-model)
>[Próximo](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="651b9-299">[Previous](xref:data/ef-rp/complex-data-model)
[Next](xref:data/ef-rp/update-related-data)</span></span>
