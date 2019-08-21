---
title: Páginas Razor com o EF Core no ASP.NET Core – Ler dados relacionados – 6 de 8
author: rick-anderson
description: Neste tutorial, você lê e exibe dados relacionados – ou seja, os dados que o Entity Framework carrega nas propriedades de navegação.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
uid: data/ef-rp/read-related-data
ms.openlocfilehash: a4864a749ed9c839c2a22f179759a3f28db525ee
ms.sourcegitcommit: 776367717e990bdd600cb3c9148ffb905d56862d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68914201"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---read-related-data---6-of-8"></a><span data-ttu-id="bdcce-103">Páginas Razor com o EF Core no ASP.NET Core – Ler dados relacionados – 6 de 8</span><span class="sxs-lookup"><span data-stu-id="bdcce-103">Razor Pages with EF Core in ASP.NET Core - Read Related Data - 6 of 8</span></span>

<span data-ttu-id="bdcce-104">Por [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="bdcce-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="bdcce-105">Este tutorial mostra como ler e exibir dados relacionados.</span><span class="sxs-lookup"><span data-stu-id="bdcce-105">This tutorial shows how to read and display related data.</span></span> <span data-ttu-id="bdcce-106">Dados relacionados são dados que o EF Core carrega nas propriedades de navegação.</span><span class="sxs-lookup"><span data-stu-id="bdcce-106">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="bdcce-107">As seguintes ilustrações mostram as páginas concluídas para este tutorial:</span><span class="sxs-lookup"><span data-stu-id="bdcce-107">The following illustrations show the completed pages for this tutorial:</span></span>

![Página Índice de Cursos](read-related-data/_static/courses-index30.png)

![Página Índice de Instrutores](read-related-data/_static/instructors-index30.png)

## <a name="eager-explicit-and-lazy-loading"></a><span data-ttu-id="bdcce-110">Carregamento adiantado, explícito e lento</span><span class="sxs-lookup"><span data-stu-id="bdcce-110">Eager, explicit, and lazy loading</span></span>

<span data-ttu-id="bdcce-111">Há várias maneiras pelas quais o EF Core pode carregar dados relacionados nas propriedades de navegação de uma entidade:</span><span class="sxs-lookup"><span data-stu-id="bdcce-111">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="bdcce-112">[Carregamento adiantado](/ef/core/querying/related-data#eager-loading).</span><span class="sxs-lookup"><span data-stu-id="bdcce-112">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="bdcce-113">O carregamento adiantado é quando uma consulta para um tipo de entidade também carrega entidades relacionadas.</span><span class="sxs-lookup"><span data-stu-id="bdcce-113">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="bdcce-114">Quando uma entidade é lida, seus dados relacionados são recuperados.</span><span class="sxs-lookup"><span data-stu-id="bdcce-114">When an entity is read, its related data is retrieved.</span></span> <span data-ttu-id="bdcce-115">Normalmente, isso resulta em uma única consulta de junção que recupera todos os dados necessários.</span><span class="sxs-lookup"><span data-stu-id="bdcce-115">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="bdcce-116">O EF Core emitirá várias consultas para alguns tipos de carregamento adiantado.</span><span class="sxs-lookup"><span data-stu-id="bdcce-116">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="bdcce-117">A emissão de várias consultas pode ser mais eficiente do que uma única consulta gigante.</span><span class="sxs-lookup"><span data-stu-id="bdcce-117">Issuing multiple queries can be more efficient than a giant single query.</span></span> <span data-ttu-id="bdcce-118">O carregamento adiantado é especificado com os métodos `Include` e `ThenInclude`.</span><span class="sxs-lookup"><span data-stu-id="bdcce-118">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![Exemplo de carregamento adiantado](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="bdcce-120">O carregamento adiantado envia várias consultas quando a navegação de coleção é incluída:</span><span class="sxs-lookup"><span data-stu-id="bdcce-120">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="bdcce-121">Uma consulta para a consulta principal</span><span class="sxs-lookup"><span data-stu-id="bdcce-121">One query for the main query</span></span> 
  * <span data-ttu-id="bdcce-122">Uma consulta para cada "borda" de coleção na árvore de carregamento.</span><span class="sxs-lookup"><span data-stu-id="bdcce-122">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="bdcce-123">Separe consultas com `Load`: os dados podem ser recuperados em consultas separadas e o EF Core "corrige" as propriedades de navegação.</span><span class="sxs-lookup"><span data-stu-id="bdcce-123">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="bdcce-124">"Correção" significa que o EF Core preenche automaticamente as propriedades de navegação.</span><span class="sxs-lookup"><span data-stu-id="bdcce-124">"Fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="bdcce-125">A separação de consultas com `Load` é mais parecida com o carregamento explícito do que com o carregamento adiantado.</span><span class="sxs-lookup"><span data-stu-id="bdcce-125">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![Exemplo de consultas separadas](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="bdcce-127">Observação: o EF Core corrige automaticamente as propriedades de navegação para outras entidades que foram carregadas anteriormente na instância do contexto.</span><span class="sxs-lookup"><span data-stu-id="bdcce-127">Note: EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="bdcce-128">Mesmo se os dados de uma propriedade de navegação *não* foram incluídos de forma explícita, a propriedade ainda pode ser populada se algumas ou todas as entidades relacionadas foram carregadas anteriormente.</span><span class="sxs-lookup"><span data-stu-id="bdcce-128">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="bdcce-129">[Carregamento explícito](/ef/core/querying/related-data#explicit-loading).</span><span class="sxs-lookup"><span data-stu-id="bdcce-129">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="bdcce-130">Quando a entidade é lida pela primeira vez, os dados relacionados não são recuperados.</span><span class="sxs-lookup"><span data-stu-id="bdcce-130">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="bdcce-131">Um código precisa ser escrito para recuperar os dados relacionados quando eles forem necessários.</span><span class="sxs-lookup"><span data-stu-id="bdcce-131">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="bdcce-132">O carregamento explícito com consultas separadas resulta no envio de várias consultas ao banco de dados.</span><span class="sxs-lookup"><span data-stu-id="bdcce-132">Explicit loading with separate queries results in multiple queries sent to the database.</span></span> <span data-ttu-id="bdcce-133">Com o carregamento explícito, o código especifica as propriedades de navegação a serem carregadas.</span><span class="sxs-lookup"><span data-stu-id="bdcce-133">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="bdcce-134">Use o método `Load` para fazer o carregamento explícito.</span><span class="sxs-lookup"><span data-stu-id="bdcce-134">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="bdcce-135">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="bdcce-135">For example:</span></span>

  ![Exemplo de carregamento explícito](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="bdcce-137">[Carregamento lento](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="bdcce-137">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="bdcce-138">[O carregamento lento foi adicionado ao EF Core na versão 2.1](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="bdcce-138">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="bdcce-139">Quando a entidade é lida pela primeira vez, os dados relacionados não são recuperados.</span><span class="sxs-lookup"><span data-stu-id="bdcce-139">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="bdcce-140">Na primeira vez que uma propriedade de navegação é acessada, os dados necessários para essa propriedade de navegação são recuperados automaticamente.</span><span class="sxs-lookup"><span data-stu-id="bdcce-140">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="bdcce-141">Uma consulta é enviada para o banco de dados sempre que uma propriedade de navegação é acessada pela primeira vez.</span><span class="sxs-lookup"><span data-stu-id="bdcce-141">A query is sent to the database each time a navigation property is accessed for the first time.</span></span>

## <a name="create-course-pages"></a><span data-ttu-id="bdcce-142">Criar páginas do Curso</span><span class="sxs-lookup"><span data-stu-id="bdcce-142">Create Course pages</span></span>

<span data-ttu-id="bdcce-143">A entidade `Course` inclui uma propriedade de navegação que contém a entidade `Department` relacionada.</span><span class="sxs-lookup"><span data-stu-id="bdcce-143">The `Course` entity includes a navigation property that contains the related `Department` entity.</span></span>

![Course.Department](read-related-data/_static/dep-crs.png)

<span data-ttu-id="bdcce-145">Para exibir o nome do departamento atribuído para um curso:</span><span class="sxs-lookup"><span data-stu-id="bdcce-145">To display the name of the assigned department for a course:</span></span>

* <span data-ttu-id="bdcce-146">Carregue a entidade relacionada `Department` na propriedade de navegação `Course.Department`.</span><span class="sxs-lookup"><span data-stu-id="bdcce-146">Load the related `Department` entity into the `Course.Department` navigation property.</span></span>
* <span data-ttu-id="bdcce-147">Obtenha o nome da propriedade `Department` da entidade `Name`.</span><span class="sxs-lookup"><span data-stu-id="bdcce-147">Get the name from the `Department` entity's `Name` property.</span></span>

<a name="scaffold"></a>

### <a name="scaffold-course-pages"></a><span data-ttu-id="bdcce-148">Aplicar scaffold às páginas do curso</span><span class="sxs-lookup"><span data-stu-id="bdcce-148">Scaffold Course pages</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="bdcce-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bdcce-149">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="bdcce-150">Siga as instruções em [páginas do aluno do Scaffold](xref:data/ef-rp/intro#scaffold-student-pages) com as seguintes exceções:</span><span class="sxs-lookup"><span data-stu-id="bdcce-150">Follow the instructions in [Scaffold Student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="bdcce-151">Crie uma pasta *Pages/Courses*.</span><span class="sxs-lookup"><span data-stu-id="bdcce-151">Create a *Pages/Courses* folder.</span></span>
  * <span data-ttu-id="bdcce-152">Use `Course` para a classe de modelo.</span><span class="sxs-lookup"><span data-stu-id="bdcce-152">Use `Course` for the model class.</span></span>
  * <span data-ttu-id="bdcce-153">Use a classe de contexto existente, em vez de criar uma nova.</span><span class="sxs-lookup"><span data-stu-id="bdcce-153">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="bdcce-154">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bdcce-154">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="bdcce-155">Crie uma pasta *Pages/Courses*.</span><span class="sxs-lookup"><span data-stu-id="bdcce-155">Create a *Pages/Courses* folder.</span></span>

* <span data-ttu-id="bdcce-156">Execute o comando a seguir para aplicar scaffold das páginas do Curso.</span><span class="sxs-lookup"><span data-stu-id="bdcce-156">Run the following command to scaffold the Course pages.</span></span>

  <span data-ttu-id="bdcce-157">**No Windows:**</span><span class="sxs-lookup"><span data-stu-id="bdcce-157">**On Windows:**</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

  <span data-ttu-id="bdcce-158">**No Linux ou macOS:**</span><span class="sxs-lookup"><span data-stu-id="bdcce-158">**On Linux or macOS:**</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages/Courses --referenceScriptLibraries
  ```

---

* <span data-ttu-id="bdcce-159">Abra *Pages/Courses/Index.cshtml.cs* e examine o método `OnGetAsync`.</span><span class="sxs-lookup"><span data-stu-id="bdcce-159">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="bdcce-160">O mecanismo de scaffolding especificou o carregamento adiantado para a propriedade de navegação `Department`.</span><span class="sxs-lookup"><span data-stu-id="bdcce-160">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="bdcce-161">O método `Include` especifica o carregamento adiantado.</span><span class="sxs-lookup"><span data-stu-id="bdcce-161">The `Include` method specifies eager loading.</span></span>

* <span data-ttu-id="bdcce-162">Execute o aplicativo e selecione o link **Cursos**.</span><span class="sxs-lookup"><span data-stu-id="bdcce-162">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="bdcce-163">A coluna de departamento exibe a `DepartmentID`, que não é útil.</span><span class="sxs-lookup"><span data-stu-id="bdcce-163">The department column displays the `DepartmentID`, which isn't useful.</span></span>

### <a name="display-the-department-name"></a><span data-ttu-id="bdcce-164">Exibir o nome do departamento</span><span class="sxs-lookup"><span data-stu-id="bdcce-164">Display the department name</span></span>

<span data-ttu-id="bdcce-165">Atualize Pages/Courses/Index.cshtml.cs com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="bdcce-165">Update Pages/Courses/Index.cshtml.cs with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Index.cshtml.cs?highlight=18,22,24)]

<span data-ttu-id="bdcce-166">O código anterior altera a propriedade `Course` para `Courses` e adiciona `AsNoTracking`.</span><span class="sxs-lookup"><span data-stu-id="bdcce-166">The preceding code changes the `Course` property to `Courses` and adds `AsNoTracking`.</span></span> <span data-ttu-id="bdcce-167">`AsNoTracking` melhora o desempenho porque as entidades retornadas não são controladas.</span><span class="sxs-lookup"><span data-stu-id="bdcce-167">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="bdcce-168">As entidades não precisam ser controladas porque não são atualizadas no contexto atual.</span><span class="sxs-lookup"><span data-stu-id="bdcce-168">The entities don't need to be tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="bdcce-169">Atualize *Pages/Courses/Index.cshtml* com o seguinte código.</span><span class="sxs-lookup"><span data-stu-id="bdcce-169">Update *Pages/Courses/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Index.cshtml?highlight=5,8,16-18,20,23,26,32,35-37,45)]

<span data-ttu-id="bdcce-170">As seguintes alterações foram feitas na biblioteca gerada por código em scaffolding:</span><span class="sxs-lookup"><span data-stu-id="bdcce-170">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="bdcce-171">O nome da propriedade `Course` foi alterado para `Courses`.</span><span class="sxs-lookup"><span data-stu-id="bdcce-171">Changed the `Course` property name to `Courses`.</span></span>
* <span data-ttu-id="bdcce-172">Adicionou uma coluna **Número** que mostra o valor da propriedade `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="bdcce-172">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="bdcce-173">Por padrão, as chaves primárias não são geradas por scaffolding porque normalmente não têm sentido para os usuários finais.</span><span class="sxs-lookup"><span data-stu-id="bdcce-173">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="bdcce-174">No entanto, nesse caso, a chave primária é significativa.</span><span class="sxs-lookup"><span data-stu-id="bdcce-174">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="bdcce-175">Alterou a coluna **Departamento** para que ela exiba o nome de departamento.</span><span class="sxs-lookup"><span data-stu-id="bdcce-175">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="bdcce-176">O código exibe a propriedade `Name` da entidade `Department` que é carregada na propriedade de navegação `Department`:</span><span class="sxs-lookup"><span data-stu-id="bdcce-176">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="bdcce-177">Execute o aplicativo e selecione a guia **Cursos** para ver a lista com nomes de departamentos.</span><span class="sxs-lookup"><span data-stu-id="bdcce-177">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Página Índice de Cursos](read-related-data/_static/courses-index30.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="bdcce-179">Carregando dados relacionados com Select</span><span class="sxs-lookup"><span data-stu-id="bdcce-179">Loading related data with Select</span></span>

<span data-ttu-id="bdcce-180">O método `OnGetAsync` carrega dados relacionados com o método `Include`.</span><span class="sxs-lookup"><span data-stu-id="bdcce-180">The `OnGetAsync` method loads related data with the `Include` method.</span></span> <span data-ttu-id="bdcce-181">O método `Select` é uma alternativa que carrega apenas os dados relacionados necessários.</span><span class="sxs-lookup"><span data-stu-id="bdcce-181">The `Select` method is an alternative that loads only the related data needed.</span></span> <span data-ttu-id="bdcce-182">Para itens únicos, como o `Department.Name`, ele usa um SQL INNER JOIN.</span><span class="sxs-lookup"><span data-stu-id="bdcce-182">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="bdcce-183">Para coleções, ele usa outro acesso ao banco de dados, assim como o operador `Include` em coleções.</span><span class="sxs-lookup"><span data-stu-id="bdcce-183">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="bdcce-184">O seguinte código carrega dados relacionados com o método `Select`:</span><span class="sxs-lookup"><span data-stu-id="bdcce-184">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=6)]

<span data-ttu-id="bdcce-185">O `CourseViewModel`:</span><span class="sxs-lookup"><span data-stu-id="bdcce-185">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="bdcce-186">Consulte [IndexSelect.cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) e [IndexSelect.cshtml.cs](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs) para obter um exemplo completo.</span><span class="sxs-lookup"><span data-stu-id="bdcce-186">See [IndexSelect.cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-instructor-pages"></a><span data-ttu-id="bdcce-187">Criar as páginas de Instrutor</span><span class="sxs-lookup"><span data-stu-id="bdcce-187">Create Instructor pages</span></span>

<span data-ttu-id="bdcce-188">Esta seção aplica scaffold a páginas do Instrutor e adiciona Cursos e Inscrições relacionados à página Índice de Instrutores.</span><span class="sxs-lookup"><span data-stu-id="bdcce-188">This section scaffolds Instructor pages and adds related Courses and Enrollments to the Instructors Index page.</span></span>

<a name="IP"></a>
<span data-ttu-id="bdcce-189">![Página Índice de Instrutores](read-related-data/_static/instructors-index30.png)</span><span class="sxs-lookup"><span data-stu-id="bdcce-189">![Instructors Index page](read-related-data/_static/instructors-index30.png)</span></span>

<span data-ttu-id="bdcce-190">Essa página lê e exibe dados relacionados das seguintes maneiras:</span><span class="sxs-lookup"><span data-stu-id="bdcce-190">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="bdcce-191">A lista de instrutores exibe dados relacionados da entidade `OfficeAssignment` (Office na imagem anterior).</span><span class="sxs-lookup"><span data-stu-id="bdcce-191">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="bdcce-192">As entidades `Instructor` e `OfficeAssignment` estão em uma relação um para zero ou um.</span><span class="sxs-lookup"><span data-stu-id="bdcce-192">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="bdcce-193">O carregamento adiantado é usado para as entidades `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="bdcce-193">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="bdcce-194">O carregamento adiantado costuma ser mais eficiente quando os dados relacionados precisam ser exibidos.</span><span class="sxs-lookup"><span data-stu-id="bdcce-194">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="bdcce-195">Nesse caso, as atribuições de escritório para os instrutores são exibidas.</span><span class="sxs-lookup"><span data-stu-id="bdcce-195">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="bdcce-196">Quando o usuário seleciona um instrutor, as entidades `Course` relacionadas são exibidas.</span><span class="sxs-lookup"><span data-stu-id="bdcce-196">When the user selects an instructor, related `Course` entities are displayed.</span></span> <span data-ttu-id="bdcce-197">As entidades `Instructor` e `Course` estão em uma relação muitos para muitos.</span><span class="sxs-lookup"><span data-stu-id="bdcce-197">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="bdcce-198">O carregamento adiantado é usado para entidades `Course` e suas entidades `Department` relacionadas.</span><span class="sxs-lookup"><span data-stu-id="bdcce-198">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="bdcce-199">Nesse caso, consultas separadas podem ser mais eficientes porque somente os cursos para o instrutor selecionado são necessários.</span><span class="sxs-lookup"><span data-stu-id="bdcce-199">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="bdcce-200">Este exemplo mostra como usar o carregamento adiantado para propriedades de navegação em entidades que estão nas propriedades de navegação.</span><span class="sxs-lookup"><span data-stu-id="bdcce-200">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="bdcce-201">Quando o usuário seleciona um curso, dados relacionados da entidade `Enrollments` são exibidos.</span><span class="sxs-lookup"><span data-stu-id="bdcce-201">When the user selects a course, related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="bdcce-202">Na imagem anterior, o nome do aluno e a nota são exibidos.</span><span class="sxs-lookup"><span data-stu-id="bdcce-202">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="bdcce-203">As entidades `Course` e `Enrollment` estão em uma relação um-para-muitos.</span><span class="sxs-lookup"><span data-stu-id="bdcce-203">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model"></a><span data-ttu-id="bdcce-204">Criar um modelo de exibição</span><span class="sxs-lookup"><span data-stu-id="bdcce-204">Create a view model</span></span>

<span data-ttu-id="bdcce-205">A página Instrutores mostra dados de três tabelas diferentes.</span><span class="sxs-lookup"><span data-stu-id="bdcce-205">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="bdcce-206">É necessário um modelo de exibição que inclui três propriedades que representam as três tabelas.</span><span class="sxs-lookup"><span data-stu-id="bdcce-206">A view model is needed that includes three properties representing the three tables.</span></span>

<span data-ttu-id="bdcce-207">Crie *SchoolViewModels/InstructorIndexData.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="bdcce-207">Create *SchoolViewModels/InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-instructor-pages"></a><span data-ttu-id="bdcce-208">Aplicar scaffold às páginas do Instrutor</span><span class="sxs-lookup"><span data-stu-id="bdcce-208">Scaffold Instructor pages</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="bdcce-209">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bdcce-209">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="bdcce-210">Siga as instruções em [Aplicar scaffold às páginas do aluno](xref:data/ef-rp/intro#scaffold-student-pages) com as seguintes exceções:</span><span class="sxs-lookup"><span data-stu-id="bdcce-210">Follow the instructions in [Scaffold the student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="bdcce-211">Crie uma pasta *Pages/Instructors*.</span><span class="sxs-lookup"><span data-stu-id="bdcce-211">Create a *Pages/Instructors* folder.</span></span>
  * <span data-ttu-id="bdcce-212">Use `Instructor` para a classe de modelo.</span><span class="sxs-lookup"><span data-stu-id="bdcce-212">Use `Instructor` for the model class.</span></span>
  * <span data-ttu-id="bdcce-213">Use a classe de contexto existente, em vez de criar uma nova.</span><span class="sxs-lookup"><span data-stu-id="bdcce-213">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="bdcce-214">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bdcce-214">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="bdcce-215">Crie uma pasta *Pages/Instructors*.</span><span class="sxs-lookup"><span data-stu-id="bdcce-215">Create a *Pages/Instructors* folder.</span></span>

* <span data-ttu-id="bdcce-216">Execute o comando a seguir para aplicar scaffold das páginas do instrutor.</span><span class="sxs-lookup"><span data-stu-id="bdcce-216">Run the following command to scaffold the Instructor pages.</span></span>

  <span data-ttu-id="bdcce-217">**No Windows:**</span><span class="sxs-lookup"><span data-stu-id="bdcce-217">**On Windows:**</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

  <span data-ttu-id="bdcce-218">**No Linux ou macOS:**</span><span class="sxs-lookup"><span data-stu-id="bdcce-218">**On Linux or macOS:**</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages/Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="bdcce-219">Para ver a aparência da página com scaffold antes de atualizá-la, execute o aplicativo e navegue até a página Instrutores.</span><span class="sxs-lookup"><span data-stu-id="bdcce-219">To see what the scaffolded page looks like before you update it, run the app and navigate to the Instructors page.</span></span>

<span data-ttu-id="bdcce-220">Atualize *Pages/Instructors/Index.cshtml.cs* pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="bdcce-220">Update *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,19-53)]

<span data-ttu-id="bdcce-221">O método `OnGetAsync` aceita dados de rota opcionais para a ID do instrutor selecionado.</span><span class="sxs-lookup"><span data-stu-id="bdcce-221">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="bdcce-222">Examine a consulta no arquivo *Pages/Instructors/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="bdcce-222">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading)]

<span data-ttu-id="bdcce-223">O código especifica o carregamento adiantado para as seguintes propriedades de navegação:</span><span class="sxs-lookup"><span data-stu-id="bdcce-223">The code specifies eager loading for the following navigation properties:</span></span>

* `Instructor.OfficeAssignment`
* `Instructor.CourseAssignments`
  * `CourseAssignments.Course`
    * `Course.Department`
    * `Course.Enrollments`
      * `Enrollment.Student`

<span data-ttu-id="bdcce-224">Observe a repetição dos métodos `Include` e `ThenInclude` para `CourseAssignments` e `Course`.</span><span class="sxs-lookup"><span data-stu-id="bdcce-224">Notice the repetition of `Include` and `ThenInclude` methods for `CourseAssignments` and `Course`.</span></span> <span data-ttu-id="bdcce-225">Essa repetição é necessária para especificar o carregamento adiantado para duas propriedades de navegação da entidade `Course`.</span><span class="sxs-lookup"><span data-stu-id="bdcce-225">This repetition is necessary to specify eager loading for two navigation properties of the `Course` entity.</span></span>

<span data-ttu-id="bdcce-226">O código a seguir é executado quando o instrutor é selecionado (`id != null`).</span><span class="sxs-lookup"><span data-stu-id="bdcce-226">The following code executes when an instructor is selected (`id != null`).</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectInstructor)]

<span data-ttu-id="bdcce-227">O instrutor selecionado é recuperado da lista de instrutores no modelo de exibição.</span><span class="sxs-lookup"><span data-stu-id="bdcce-227">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="bdcce-228">Em seguida, a propriedade `Courses` do modelo de exibição é carregada com as entidades `Course` da propriedade de navegação `CourseAssignments` desse instrutor.</span><span class="sxs-lookup"><span data-stu-id="bdcce-228">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

<span data-ttu-id="bdcce-229">O método `Where` retorna uma coleção.</span><span class="sxs-lookup"><span data-stu-id="bdcce-229">The `Where` method returns a collection.</span></span> <span data-ttu-id="bdcce-230">Mas, neste caso, o filtro selecionará uma única entidade.</span><span class="sxs-lookup"><span data-stu-id="bdcce-230">But in this case, the filter will select a single entity.</span></span> <span data-ttu-id="bdcce-231">Portanto, o método `Single` é chamado para converter a coleção em uma única entidade `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="bdcce-231">so the `Single` method is called to convert the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="bdcce-232">A entidade `Instructor` fornece acesso à propriedade `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="bdcce-232">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="bdcce-233">`CourseAssignments` fornece acesso às entidades `Course` relacionadas.</span><span class="sxs-lookup"><span data-stu-id="bdcce-233">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![Instrutor para Cursos m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="bdcce-235">O método `Single` é usado em uma coleção quando a coleção tem apenas um item.</span><span class="sxs-lookup"><span data-stu-id="bdcce-235">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="bdcce-236">O método `Single` gera uma exceção se a coleção está vazia ou se há mais de um item.</span><span class="sxs-lookup"><span data-stu-id="bdcce-236">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="bdcce-237">Uma alternativa é `SingleOrDefault`, que retorna um valor padrão (nulo, nesse caso) se a coleção está vazia.</span><span class="sxs-lookup"><span data-stu-id="bdcce-237">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span>

<span data-ttu-id="bdcce-238">O seguinte código popula a propriedade `Enrollments` do modelo de exibição quando um curso é selecionado:</span><span class="sxs-lookup"><span data-stu-id="bdcce-238">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectCourse)]

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="bdcce-239">Atualizar a página Índice de instrutores</span><span class="sxs-lookup"><span data-stu-id="bdcce-239">Update the instructors Index page</span></span>

<span data-ttu-id="bdcce-240">Atualize *Pages/Instructors/Index.cshtml* com o seguinte código.</span><span class="sxs-lookup"><span data-stu-id="bdcce-240">Update *Pages/Instructors/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Index.cshtml?highlight=1,5,8,16-21,25-32,43-57,67-102,104-126)]

<span data-ttu-id="bdcce-241">O código anterior faz as seguintes alterações:</span><span class="sxs-lookup"><span data-stu-id="bdcce-241">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="bdcce-242">Atualiza a diretiva `page` de `@page` para `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="bdcce-242">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="bdcce-243">`"{id:int?}"` é um modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="bdcce-243">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="bdcce-244">O modelo de rota altera cadeias de consulta de inteiro na URL para dados de rota.</span><span class="sxs-lookup"><span data-stu-id="bdcce-244">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="bdcce-245">Por exemplo, clicar no link **Selecionar** de um o instrutor apenas com a diretiva `@page` produz uma URL semelhante à seguinte:</span><span class="sxs-lookup"><span data-stu-id="bdcce-245">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `https://localhost:5001/Instructors?id=2`

  <span data-ttu-id="bdcce-246">Quando a diretiva de página é `@page "{id:int?}"`, a URL é:</span><span class="sxs-lookup"><span data-stu-id="bdcce-246">When the page directive is `@page "{id:int?}"`, the URL is:</span></span>

  `https://localhost:5001/Instructors/2`

* <span data-ttu-id="bdcce-247">Adiciona uma coluna **Escritório** que exibirá `item.OfficeAssignment.Location` somente se `item.OfficeAssignment` não for nulo.</span><span class="sxs-lookup"><span data-stu-id="bdcce-247">Adds an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="bdcce-248">Como essa é uma relação um para zero ou um, pode não haver uma entidade OfficeAssignment relacionada.</span><span class="sxs-lookup"><span data-stu-id="bdcce-248">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="bdcce-249">Adiciona uma coluna **Cursos** que exibe os cursos ministrados por cada instrutor.</span><span class="sxs-lookup"><span data-stu-id="bdcce-249">Adds a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="bdcce-250">Consulte [Transição de linha explícita com `@:`](xref:mvc/views/razor#explicit-line-transition-with-) para obter mais informações sobre essa sintaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="bdcce-250">See [Explicit Line Transition with `@:`](xref:mvc/views/razor#explicit-line-transition-with-) for more about this razor syntax.</span></span>

* <span data-ttu-id="bdcce-251">Adiciona um código que adiciona dinamicamente `class="success"` ao elemento `tr` do instrutor e do curso selecionados.</span><span class="sxs-lookup"><span data-stu-id="bdcce-251">Adds code that dynamically adds `class="success"` to the `tr` element of the selected instructor and course.</span></span> <span data-ttu-id="bdcce-252">Isso define uma cor da tela de fundo para a linha selecionada usando uma classe Bootstrap.</span><span class="sxs-lookup"><span data-stu-id="bdcce-252">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="bdcce-253">Adiciona um novo hiperlink rotulado **Selecionar**.</span><span class="sxs-lookup"><span data-stu-id="bdcce-253">Adds a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="bdcce-254">Este link envia a ID do instrutor selecionado para o método `Index` e define uma cor da tela de fundo.</span><span class="sxs-lookup"><span data-stu-id="bdcce-254">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

* <span data-ttu-id="bdcce-255">Adiciona uma tabela de cursos para o Instrutor selecionado.</span><span class="sxs-lookup"><span data-stu-id="bdcce-255">Adds a table of courses for the selected Instructor.</span></span>

* <span data-ttu-id="bdcce-256">Adiciona uma tabela de inscrições de alunos para o curso selecionado.</span><span class="sxs-lookup"><span data-stu-id="bdcce-256">Adds a table of student enrollments for the selected course.</span></span>

<span data-ttu-id="bdcce-257">Execute o aplicativo e selecione a guia **Instrutores**. A página exibe o `Location` (escritório) da entidade `OfficeAssignment` relacionada.</span><span class="sxs-lookup"><span data-stu-id="bdcce-257">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="bdcce-258">Se `OfficeAssignment` for nulo, uma célula de tabela vazia será exibida.</span><span class="sxs-lookup"><span data-stu-id="bdcce-258">If `OfficeAssignment` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="bdcce-259">Clique no link **Selecionar** para um instrutor.</span><span class="sxs-lookup"><span data-stu-id="bdcce-259">Click on the **Select** link for an instructor.</span></span> <span data-ttu-id="bdcce-260">As alterações de estilo de linha e os cursos atribuídos a esse instrutor são exibidos.</span><span class="sxs-lookup"><span data-stu-id="bdcce-260">The row style changes and courses assigned to that instructor are displayed.</span></span>

<span data-ttu-id="bdcce-261">Selecione um curso para ver a lista de alunos registrados e suas notas.</span><span class="sxs-lookup"><span data-stu-id="bdcce-261">Select a course to see the list of enrolled students and their grades.</span></span>

![Página Índice de Instrutores – instrutor e curso selecionados](read-related-data/_static/instructors-index30.png)

## <a name="using-single"></a><span data-ttu-id="bdcce-263">Usando Single</span><span class="sxs-lookup"><span data-stu-id="bdcce-263">Using Single</span></span>

<span data-ttu-id="bdcce-264">O método `Single` pode passar a condição `Where` em vez de chamar o método `Where` separadamente:</span><span class="sxs-lookup"><span data-stu-id="bdcce-264">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="bdcce-265">O uso de `Single` com uma condição Where é uma questão de preferência pessoal.</span><span class="sxs-lookup"><span data-stu-id="bdcce-265">Use of `Single` with a Where condition is a matter of personal preference.</span></span> <span data-ttu-id="bdcce-266">Não oferece nenhum benefício sobre o uso do método `Where`.</span><span class="sxs-lookup"><span data-stu-id="bdcce-266">It provides no benefits over using the `Where` method.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="bdcce-267">Carregamento explícito</span><span class="sxs-lookup"><span data-stu-id="bdcce-267">Explicit loading</span></span>

<span data-ttu-id="bdcce-268">O código atual especifica o carregamento adiantado para `Enrollments` e `Students`:</span><span class="sxs-lookup"><span data-stu-id="bdcce-268">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading&highlight=6-9)]

<span data-ttu-id="bdcce-269">Suponha que os usuários raramente desejem ver registros em um curso.</span><span class="sxs-lookup"><span data-stu-id="bdcce-269">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="bdcce-270">Nesse caso, uma otimização será carregar apenas os dados de registro se eles forem solicitados.</span><span class="sxs-lookup"><span data-stu-id="bdcce-270">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="bdcce-271">Nesta seção, o `OnGetAsync` é atualizado para usar o carregamento explícito de `Enrollments` e `Students`.</span><span class="sxs-lookup"><span data-stu-id="bdcce-271">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="bdcce-272">Atualize *Pages/Instructors/Index.cshtml.cs* pelo seguinte código.</span><span class="sxs-lookup"><span data-stu-id="bdcce-272">Update *Pages/Instructors/Index.cshtml.cs* with the following code.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Index.cshtml.cs?highlight=31-35,52-56)]

<span data-ttu-id="bdcce-273">O código anterior remove as chamadas do método *ThenInclude* para dados de registro e de alunos.</span><span class="sxs-lookup"><span data-stu-id="bdcce-273">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="bdcce-274">Se um curso for selecionado, o código de carregamento explícito recuperará:</span><span class="sxs-lookup"><span data-stu-id="bdcce-274">If a course is selected, the explicit loading code retrieves:</span></span>

* <span data-ttu-id="bdcce-275">As entidades `Enrollment` para o curso selecionado.</span><span class="sxs-lookup"><span data-stu-id="bdcce-275">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="bdcce-276">As entidades `Student` para cada `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="bdcce-276">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="bdcce-277">Observe que o código anterior comenta `.AsNoTracking()`.</span><span class="sxs-lookup"><span data-stu-id="bdcce-277">Notice that the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="bdcce-278">As propriedades de navegação apenas podem ser carregadas de forma explícita para entidades controladas.</span><span class="sxs-lookup"><span data-stu-id="bdcce-278">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="bdcce-279">Teste o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="bdcce-279">Test the app.</span></span> <span data-ttu-id="bdcce-280">De uma perspectiva dos usuários, o aplicativo se comporta de forma idêntica à versão anterior.</span><span class="sxs-lookup"><span data-stu-id="bdcce-280">From a user's perspective, the app behaves identically to the previous version.</span></span>

## <a name="next-steps"></a><span data-ttu-id="bdcce-281">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="bdcce-281">Next steps</span></span>

<span data-ttu-id="bdcce-282">O próximo tutorial mostra como atualizar os dados relacionados.</span><span class="sxs-lookup"><span data-stu-id="bdcce-282">The next tutorial shows how to update related data.</span></span>

>[!div class="step-by-step"]
><span data-ttu-id="bdcce-283">[Tutorial anterior](xref:data/ef-rp/complex-data-model)
>[Próximo tutorial](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="bdcce-283">[Previous tutorial](xref:data/ef-rp/complex-data-model)
[Next tutorial](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="bdcce-284">Neste tutorial, os dados relacionados são lidos e exibidos.</span><span class="sxs-lookup"><span data-stu-id="bdcce-284">In this tutorial, related data is read and displayed.</span></span> <span data-ttu-id="bdcce-285">Dados relacionados são dados que o EF Core carrega nas propriedades de navegação.</span><span class="sxs-lookup"><span data-stu-id="bdcce-285">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="bdcce-286">Caso tenha problemas que não consiga resolver, [baixe ou exiba o aplicativo concluído.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span><span class="sxs-lookup"><span data-stu-id="bdcce-286">If you run into problems you can't solve, [download or view the completed app.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="bdcce-287">[Instruções de download](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="bdcce-287">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="bdcce-288">As seguintes ilustrações mostram as páginas concluídas para este tutorial:</span><span class="sxs-lookup"><span data-stu-id="bdcce-288">The following illustrations show the completed pages for this tutorial:</span></span>

![Página Índice de Cursos](read-related-data/_static/courses-index.png)

![Página Índice de Instrutores](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a><span data-ttu-id="bdcce-291">Carregamento adiantado, explícito e lento de dados relacionados</span><span class="sxs-lookup"><span data-stu-id="bdcce-291">Eager, explicit, and lazy Loading of related data</span></span>

<span data-ttu-id="bdcce-292">Há várias maneiras pelas quais o EF Core pode carregar dados relacionados nas propriedades de navegação de uma entidade:</span><span class="sxs-lookup"><span data-stu-id="bdcce-292">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="bdcce-293">[Carregamento adiantado](/ef/core/querying/related-data#eager-loading).</span><span class="sxs-lookup"><span data-stu-id="bdcce-293">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="bdcce-294">O carregamento adiantado é quando uma consulta para um tipo de entidade também carrega entidades relacionadas.</span><span class="sxs-lookup"><span data-stu-id="bdcce-294">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="bdcce-295">Quando a entidade é lida, seus dados relacionados são recuperados.</span><span class="sxs-lookup"><span data-stu-id="bdcce-295">When the entity is read, its related data is retrieved.</span></span> <span data-ttu-id="bdcce-296">Normalmente, isso resulta em uma única consulta de junção que recupera todos os dados necessários.</span><span class="sxs-lookup"><span data-stu-id="bdcce-296">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="bdcce-297">O EF Core emitirá várias consultas para alguns tipos de carregamento adiantado.</span><span class="sxs-lookup"><span data-stu-id="bdcce-297">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="bdcce-298">A emissão de várias consultas pode ser mais eficiente do que era o caso para algumas consultas no EF6 quando havia uma única consulta.</span><span class="sxs-lookup"><span data-stu-id="bdcce-298">Issuing multiple queries can be more efficient than was the case for some queries in EF6 where there was a single query.</span></span> <span data-ttu-id="bdcce-299">O carregamento adiantado é especificado com os métodos `Include` e `ThenInclude`.</span><span class="sxs-lookup"><span data-stu-id="bdcce-299">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![Exemplo de carregamento adiantado](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="bdcce-301">O carregamento adiantado envia várias consultas quando a navegação de coleção é incluída:</span><span class="sxs-lookup"><span data-stu-id="bdcce-301">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="bdcce-302">Uma consulta para a consulta principal</span><span class="sxs-lookup"><span data-stu-id="bdcce-302">One query for the main query</span></span> 
  * <span data-ttu-id="bdcce-303">Uma consulta para cada "borda" de coleção na árvore de carregamento.</span><span class="sxs-lookup"><span data-stu-id="bdcce-303">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="bdcce-304">Separe consultas com `Load`: os dados podem ser recuperados em consultas separadas e o EF Core "corrige" as propriedades de navegação.</span><span class="sxs-lookup"><span data-stu-id="bdcce-304">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="bdcce-305">"Correção" significa que o EF Core popula automaticamente as propriedades de navegação.</span><span class="sxs-lookup"><span data-stu-id="bdcce-305">"fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="bdcce-306">A separação de consultas com `Load` é mais parecida com o carregamento explícito do que com o carregamento adiantado.</span><span class="sxs-lookup"><span data-stu-id="bdcce-306">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![Exemplo de consultas separadas](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="bdcce-308">Observação: o EF Core corrige automaticamente as propriedades de navegação para outras entidades que foram carregadas anteriormente na instância do contexto.</span><span class="sxs-lookup"><span data-stu-id="bdcce-308">Note: EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="bdcce-309">Mesmo se os dados de uma propriedade de navegação *não* foram incluídos de forma explícita, a propriedade ainda pode ser populada se algumas ou todas as entidades relacionadas foram carregadas anteriormente.</span><span class="sxs-lookup"><span data-stu-id="bdcce-309">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="bdcce-310">[Carregamento explícito](/ef/core/querying/related-data#explicit-loading).</span><span class="sxs-lookup"><span data-stu-id="bdcce-310">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="bdcce-311">Quando a entidade é lida pela primeira vez, os dados relacionados não são recuperados.</span><span class="sxs-lookup"><span data-stu-id="bdcce-311">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="bdcce-312">Um código precisa ser escrito para recuperar os dados relacionados quando eles forem necessários.</span><span class="sxs-lookup"><span data-stu-id="bdcce-312">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="bdcce-313">O carregamento explícito com consultas separadas resulta no envio de várias consultas ao BD.</span><span class="sxs-lookup"><span data-stu-id="bdcce-313">Explicit loading with separate queries results in multiple queries sent to the DB.</span></span> <span data-ttu-id="bdcce-314">Com o carregamento explícito, o código especifica as propriedades de navegação a serem carregadas.</span><span class="sxs-lookup"><span data-stu-id="bdcce-314">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="bdcce-315">Use o método `Load` para fazer o carregamento explícito.</span><span class="sxs-lookup"><span data-stu-id="bdcce-315">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="bdcce-316">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="bdcce-316">For example:</span></span>

  ![Exemplo de carregamento explícito](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="bdcce-318">[Carregamento lento](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="bdcce-318">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="bdcce-319">[O carregamento lento foi adicionado ao EF Core na versão 2.1](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="bdcce-319">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="bdcce-320">Quando a entidade é lida pela primeira vez, os dados relacionados não são recuperados.</span><span class="sxs-lookup"><span data-stu-id="bdcce-320">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="bdcce-321">Na primeira vez que uma propriedade de navegação é acessada, os dados necessários para essa propriedade de navegação são recuperados automaticamente.</span><span class="sxs-lookup"><span data-stu-id="bdcce-321">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="bdcce-322">Uma consulta é enviada para o BD sempre que uma propriedade de navegação é acessada pela primeira vez.</span><span class="sxs-lookup"><span data-stu-id="bdcce-322">A query is sent to the DB each time a navigation property is accessed for the first time.</span></span>

* <span data-ttu-id="bdcce-323">O operador `Select` carrega somente os dados relacionados necessários.</span><span class="sxs-lookup"><span data-stu-id="bdcce-323">The `Select` operator loads only the related data needed.</span></span>

## <a name="create-a-course-page-that-displays-department-name"></a><span data-ttu-id="bdcce-324">Criar uma página Course que exibe o nome do departamento</span><span class="sxs-lookup"><span data-stu-id="bdcce-324">Create a Course page that displays department name</span></span>

<span data-ttu-id="bdcce-325">A entidade Course inclui uma propriedade de navegação que contém a entidade `Department`.</span><span class="sxs-lookup"><span data-stu-id="bdcce-325">The Course entity includes a navigation property that contains the `Department` entity.</span></span> <span data-ttu-id="bdcce-326">A entidade `Department` contém o departamento ao qual o curso é atribuído.</span><span class="sxs-lookup"><span data-stu-id="bdcce-326">The `Department` entity contains the department that the course is assigned to.</span></span>

<span data-ttu-id="bdcce-327">Para exibir o nome do departamento atribuído em uma lista de cursos:</span><span class="sxs-lookup"><span data-stu-id="bdcce-327">To display the name of the assigned department in a list of courses:</span></span>

* <span data-ttu-id="bdcce-328">Obtenha a propriedade `Name` da entidade `Department`.</span><span class="sxs-lookup"><span data-stu-id="bdcce-328">Get the `Name` property from the `Department` entity.</span></span>
* <span data-ttu-id="bdcce-329">A entidade `Department` é obtida da propriedade de navegação `Course.Department`.</span><span class="sxs-lookup"><span data-stu-id="bdcce-329">The `Department` entity comes from the `Course.Department` navigation property.</span></span>

![Course.Department](read-related-data/_static/dep-crs.png)

<a name="scaffold"></a>

### <a name="scaffold-the-course-model"></a><span data-ttu-id="bdcce-331">Gerar o modelo Curso por scaffolding</span><span class="sxs-lookup"><span data-stu-id="bdcce-331">Scaffold the Course model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="bdcce-332">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bdcce-332">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="bdcce-333">Siga as instruções em [Gere um modelo de aluno por scaffold](xref:data/ef-rp/intro#scaffold-student-pages) e use `Course` para a classe de modelo.</span><span class="sxs-lookup"><span data-stu-id="bdcce-333">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-student-pages) and use `Course` for the model class.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="bdcce-334">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bdcce-334">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="bdcce-335">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="bdcce-335">Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

---

<span data-ttu-id="bdcce-336">O comando anterior gera o modelo `Course` por scaffolding.</span><span class="sxs-lookup"><span data-stu-id="bdcce-336">The preceding command scaffolds the `Course` model.</span></span> <span data-ttu-id="bdcce-337">Abra o projeto no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="bdcce-337">Open the project in Visual Studio.</span></span>

<span data-ttu-id="bdcce-338">Abra *Pages/Courses/Index.cshtml.cs* e examine o método `OnGetAsync`.</span><span class="sxs-lookup"><span data-stu-id="bdcce-338">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="bdcce-339">O mecanismo de scaffolding especificou o carregamento adiantado para a propriedade de navegação `Department`.</span><span class="sxs-lookup"><span data-stu-id="bdcce-339">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="bdcce-340">O método `Include` especifica o carregamento adiantado.</span><span class="sxs-lookup"><span data-stu-id="bdcce-340">The `Include` method specifies eager loading.</span></span>

<span data-ttu-id="bdcce-341">Execute o aplicativo e selecione o link **Cursos**.</span><span class="sxs-lookup"><span data-stu-id="bdcce-341">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="bdcce-342">A coluna de departamento exibe a `DepartmentID`, que não é útil.</span><span class="sxs-lookup"><span data-stu-id="bdcce-342">The department column displays the `DepartmentID`, which isn't useful.</span></span>

<span data-ttu-id="bdcce-343">Atualize o método `OnGetAsync` pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="bdcce-343">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod)]

<span data-ttu-id="bdcce-344">O código anterior adiciona `AsNoTracking`.</span><span class="sxs-lookup"><span data-stu-id="bdcce-344">The preceding code adds `AsNoTracking`.</span></span> <span data-ttu-id="bdcce-345">`AsNoTracking` melhora o desempenho porque as entidades retornadas não são controladas.</span><span class="sxs-lookup"><span data-stu-id="bdcce-345">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="bdcce-346">As entidades não são controladas porque elas não são atualizadas no contexto atual.</span><span class="sxs-lookup"><span data-stu-id="bdcce-346">The entities are not tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="bdcce-347">Atualize *Pages/Courses/Index.cshtml* com a seguinte marcação realçada:</span><span class="sxs-lookup"><span data-stu-id="bdcce-347">Update *Pages/Courses/Index.cshtml* with the following highlighted markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

<span data-ttu-id="bdcce-348">As seguintes alterações foram feitas na biblioteca gerada por código em scaffolding:</span><span class="sxs-lookup"><span data-stu-id="bdcce-348">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="bdcce-349">Alterou o cabeçalho de Índice para Cursos.</span><span class="sxs-lookup"><span data-stu-id="bdcce-349">Changed the heading from Index to Courses.</span></span>
* <span data-ttu-id="bdcce-350">Adicionou uma coluna **Número** que mostra o valor da propriedade `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="bdcce-350">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="bdcce-351">Por padrão, as chaves primárias não são geradas por scaffolding porque normalmente não têm sentido para os usuários finais.</span><span class="sxs-lookup"><span data-stu-id="bdcce-351">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="bdcce-352">No entanto, nesse caso, a chave primária é significativa.</span><span class="sxs-lookup"><span data-stu-id="bdcce-352">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="bdcce-353">Alterou a coluna **Departamento** para que ela exiba o nome de departamento.</span><span class="sxs-lookup"><span data-stu-id="bdcce-353">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="bdcce-354">O código exibe a propriedade `Name` da entidade `Department` que é carregada na propriedade de navegação `Department`:</span><span class="sxs-lookup"><span data-stu-id="bdcce-354">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="bdcce-355">Execute o aplicativo e selecione a guia **Cursos** para ver a lista com nomes de departamentos.</span><span class="sxs-lookup"><span data-stu-id="bdcce-355">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Página Índice de Cursos](read-related-data/_static/courses-index.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="bdcce-357">Carregando dados relacionados com Select</span><span class="sxs-lookup"><span data-stu-id="bdcce-357">Loading related data with Select</span></span>

<span data-ttu-id="bdcce-358">O método `OnGetAsync` carrega dados relacionados com o método `Include`:</span><span class="sxs-lookup"><span data-stu-id="bdcce-358">The `OnGetAsync` method loads related data with the `Include` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="bdcce-359">O operador `Select` carrega somente os dados relacionados necessários.</span><span class="sxs-lookup"><span data-stu-id="bdcce-359">The `Select` operator loads only the related data needed.</span></span> <span data-ttu-id="bdcce-360">Para itens únicos, como o `Department.Name`, ele usa um SQL INNER JOIN.</span><span class="sxs-lookup"><span data-stu-id="bdcce-360">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="bdcce-361">Para coleções, ele usa outro acesso ao banco de dados, assim como o operador `Include` em coleções.</span><span class="sxs-lookup"><span data-stu-id="bdcce-361">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="bdcce-362">O seguinte código carrega dados relacionados com o método `Select`:</span><span class="sxs-lookup"><span data-stu-id="bdcce-362">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="bdcce-363">O `CourseViewModel`:</span><span class="sxs-lookup"><span data-stu-id="bdcce-363">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="bdcce-364">Consulte [IndexSelect.cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) e [IndexSelect.cshtml.cs](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) para obter um exemplo completo.</span><span class="sxs-lookup"><span data-stu-id="bdcce-364">See [IndexSelect.cshtml](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a><span data-ttu-id="bdcce-365">Criar uma página Instrutores que mostra Cursos e Registros</span><span class="sxs-lookup"><span data-stu-id="bdcce-365">Create an Instructors page that shows Courses and Enrollments</span></span>

<span data-ttu-id="bdcce-366">Nesta seção, a página Instrutores é criada.</span><span class="sxs-lookup"><span data-stu-id="bdcce-366">In this section, the Instructors page is created.</span></span>

<a name="IP"></a>
<span data-ttu-id="bdcce-367">![Página Índice de Instrutores](read-related-data/_static/instructors-index.png)</span><span class="sxs-lookup"><span data-stu-id="bdcce-367">![Instructors Index page](read-related-data/_static/instructors-index.png)</span></span>

<span data-ttu-id="bdcce-368">Essa página lê e exibe dados relacionados das seguintes maneiras:</span><span class="sxs-lookup"><span data-stu-id="bdcce-368">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="bdcce-369">A lista de instrutores exibe dados relacionados da entidade `OfficeAssignment` (Office na imagem anterior).</span><span class="sxs-lookup"><span data-stu-id="bdcce-369">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="bdcce-370">As entidades `Instructor` e `OfficeAssignment` estão em uma relação um para zero ou um.</span><span class="sxs-lookup"><span data-stu-id="bdcce-370">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="bdcce-371">O carregamento adiantado é usado para as entidades `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="bdcce-371">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="bdcce-372">O carregamento adiantado costuma ser mais eficiente quando os dados relacionados precisam ser exibidos.</span><span class="sxs-lookup"><span data-stu-id="bdcce-372">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="bdcce-373">Nesse caso, as atribuições de escritório para os instrutores são exibidas.</span><span class="sxs-lookup"><span data-stu-id="bdcce-373">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="bdcce-374">Quando o usuário seleciona um instrutor (Pedro na imagem anterior), as entidades `Course` relacionadas são exibidas.</span><span class="sxs-lookup"><span data-stu-id="bdcce-374">When the user selects an instructor (Harui in the preceding image), related `Course` entities are displayed.</span></span> <span data-ttu-id="bdcce-375">As entidades `Instructor` e `Course` estão em uma relação muitos para muitos.</span><span class="sxs-lookup"><span data-stu-id="bdcce-375">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="bdcce-376">O carregamento adiantado é usado para entidades `Course` e suas entidades `Department` relacionadas.</span><span class="sxs-lookup"><span data-stu-id="bdcce-376">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="bdcce-377">Nesse caso, consultas separadas podem ser mais eficientes porque somente os cursos para o instrutor selecionado são necessários.</span><span class="sxs-lookup"><span data-stu-id="bdcce-377">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="bdcce-378">Este exemplo mostra como usar o carregamento adiantado para propriedades de navegação em entidades que estão nas propriedades de navegação.</span><span class="sxs-lookup"><span data-stu-id="bdcce-378">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="bdcce-379">Quando o usuário seleciona um curso (Química na imagem anterior), os dados relacionados da entidade `Enrollments` são exibidos.</span><span class="sxs-lookup"><span data-stu-id="bdcce-379">When the user selects a course (Chemistry in the preceding image), related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="bdcce-380">Na imagem anterior, o nome do aluno e a nota são exibidos.</span><span class="sxs-lookup"><span data-stu-id="bdcce-380">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="bdcce-381">As entidades `Course` e `Enrollment` estão em uma relação um-para-muitos.</span><span class="sxs-lookup"><span data-stu-id="bdcce-381">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model-for-the-instructor-index-view"></a><span data-ttu-id="bdcce-382">Criar um modelo de exibição para a exibição Índice de Instrutor</span><span class="sxs-lookup"><span data-stu-id="bdcce-382">Create a view model for the Instructor Index view</span></span>

<span data-ttu-id="bdcce-383">A página Instrutores mostra dados de três tabelas diferentes.</span><span class="sxs-lookup"><span data-stu-id="bdcce-383">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="bdcce-384">É criado um modelo de exibição que inclui as três entidades que representam as três tabelas.</span><span class="sxs-lookup"><span data-stu-id="bdcce-384">A view model is created that includes the three entities representing the three tables.</span></span>

<span data-ttu-id="bdcce-385">Na pasta *SchoolViewModels*, crie *InstructorIndexData.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="bdcce-385">In the *SchoolViewModels* folder, create *InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-the-instructor-model"></a><span data-ttu-id="bdcce-386">Gerar o modelo Instrutor por scaffolding</span><span class="sxs-lookup"><span data-stu-id="bdcce-386">Scaffold the Instructor model</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="bdcce-387">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bdcce-387">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="bdcce-388">Siga as instruções em [Gere um modelo de aluno por scaffold](xref:data/ef-rp/intro#scaffold-student-pages) e use `Instructor` para a classe de modelo.</span><span class="sxs-lookup"><span data-stu-id="bdcce-388">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-student-pages) and use `Instructor` for the model class.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="bdcce-389">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bdcce-389">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="bdcce-390">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="bdcce-390">Run the following command:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="bdcce-391">O comando anterior gera o modelo `Instructor` por scaffolding.</span><span class="sxs-lookup"><span data-stu-id="bdcce-391">The preceding command scaffolds the `Instructor` model.</span></span> 
<span data-ttu-id="bdcce-392">Execute o aplicativo e navegue para a página Instrutores.</span><span class="sxs-lookup"><span data-stu-id="bdcce-392">Run the app and navigate to the instructors page.</span></span>

<span data-ttu-id="bdcce-393">Substitua *Pages/Instructors/Index.cshtml.cs* pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="bdcce-393">Replace *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,18-99)]

<span data-ttu-id="bdcce-394">O método `OnGetAsync` aceita dados de rota opcionais para a ID do instrutor selecionado.</span><span class="sxs-lookup"><span data-stu-id="bdcce-394">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="bdcce-395">Examine a consulta no arquivo *Pages/Instructors/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="bdcce-395">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="bdcce-396">A consulta tem duas inclusões:</span><span class="sxs-lookup"><span data-stu-id="bdcce-396">The query has two includes:</span></span>

* <span data-ttu-id="bdcce-397">`OfficeAssignment`: mostrado na [exibição de instrutores](#IP).</span><span class="sxs-lookup"><span data-stu-id="bdcce-397">`OfficeAssignment`: Displayed in the [instructors view](#IP).</span></span>
* <span data-ttu-id="bdcce-398">`CourseAssignments`: que mostra os cursos ministrados.</span><span class="sxs-lookup"><span data-stu-id="bdcce-398">`CourseAssignments`: Which brings in the courses taught.</span></span>

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="bdcce-399">Atualizar a página Índice de instrutores</span><span class="sxs-lookup"><span data-stu-id="bdcce-399">Update the instructors Index page</span></span>

<span data-ttu-id="bdcce-400">Atualize *Pages/Instructors/Index.cshtml* com a seguinte marcação:</span><span class="sxs-lookup"><span data-stu-id="bdcce-400">Update *Pages/Instructors/Index.cshtml* with the following markup:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=1-65&highlight=1,5,8,16-21,25-32,43-57)]

<span data-ttu-id="bdcce-401">A marcação anterior faz as seguintes alterações:</span><span class="sxs-lookup"><span data-stu-id="bdcce-401">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="bdcce-402">Atualiza a diretiva `page` de `@page` para `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="bdcce-402">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="bdcce-403">`"{id:int?}"` é um modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="bdcce-403">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="bdcce-404">O modelo de rota altera cadeias de consulta de inteiro na URL para dados de rota.</span><span class="sxs-lookup"><span data-stu-id="bdcce-404">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="bdcce-405">Por exemplo, clicar no link **Selecionar** de um o instrutor apenas com a diretiva `@page` produz uma URL semelhante à seguinte:</span><span class="sxs-lookup"><span data-stu-id="bdcce-405">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `http://localhost:1234/Instructors?id=2`

  <span data-ttu-id="bdcce-406">Quando a diretiva de página é `@page "{id:int?}"`, a URL anterior é:</span><span class="sxs-lookup"><span data-stu-id="bdcce-406">When the page directive is `@page "{id:int?}"`, the previous URL is:</span></span>

  `http://localhost:1234/Instructors/2`

* <span data-ttu-id="bdcce-407">O título de página é **Instrutores**.</span><span class="sxs-lookup"><span data-stu-id="bdcce-407">Page title is **Instructors**.</span></span>
* <span data-ttu-id="bdcce-408">Adicionou uma coluna **Office** que exibe `item.OfficeAssignment.Location` somente se `item.OfficeAssignment` não é nulo.</span><span class="sxs-lookup"><span data-stu-id="bdcce-408">Added an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="bdcce-409">Como essa é uma relação um para zero ou um, pode não haver uma entidade OfficeAssignment relacionada.</span><span class="sxs-lookup"><span data-stu-id="bdcce-409">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="bdcce-410">Adicionou uma coluna **Courses** que exibe os cursos ministrados por cada instrutor.</span><span class="sxs-lookup"><span data-stu-id="bdcce-410">Added a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="bdcce-411">Consulte [Transição de linha explícita com `@:`](xref:mvc/views/razor#explicit-line-transition-with-) para saber mais sobre essa sintaxe Razor.</span><span class="sxs-lookup"><span data-stu-id="bdcce-411">See [Explicit line transition with `@:`](xref:mvc/views/razor#explicit-line-transition-with-) for more about this razor syntax.</span></span>

* <span data-ttu-id="bdcce-412">Adicionou um código que adiciona `class="success"` dinamicamente ao elemento `tr` do instrutor selecionado.</span><span class="sxs-lookup"><span data-stu-id="bdcce-412">Added code that dynamically adds `class="success"` to the `tr` element of the selected instructor.</span></span> <span data-ttu-id="bdcce-413">Isso define uma cor da tela de fundo para a linha selecionada usando uma classe Bootstrap.</span><span class="sxs-lookup"><span data-stu-id="bdcce-413">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="bdcce-414">Adicionou um novo hiperlink rotulado **Selecionar**.</span><span class="sxs-lookup"><span data-stu-id="bdcce-414">Added a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="bdcce-415">Este link envia a ID do instrutor selecionado para o método `Index` e define uma cor da tela de fundo.</span><span class="sxs-lookup"><span data-stu-id="bdcce-415">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

<span data-ttu-id="bdcce-416">Execute o aplicativo e selecione a guia **Instrutores**. A página exibe o `Location` (escritório) da entidade `OfficeAssignment` relacionada.</span><span class="sxs-lookup"><span data-stu-id="bdcce-416">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="bdcce-417">Se OfficeAssignment é nulo, uma célula de tabela vazia é exibida.</span><span class="sxs-lookup"><span data-stu-id="bdcce-417">If OfficeAssignment\` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="bdcce-418">Clique no link **Selecionar**.</span><span class="sxs-lookup"><span data-stu-id="bdcce-418">Click on the **Select** link.</span></span> <span data-ttu-id="bdcce-419">O estilo de linha é alterado.</span><span class="sxs-lookup"><span data-stu-id="bdcce-419">The row style changes.</span></span>

### <a name="add-courses-taught-by-selected-instructor"></a><span data-ttu-id="bdcce-420">Adicionar cursos ministrados pelo instrutor selecionado</span><span class="sxs-lookup"><span data-stu-id="bdcce-420">Add courses taught by selected instructor</span></span>

<span data-ttu-id="bdcce-421">Atualize o método `OnGetAsync` em *Pages/Instructors/Index.cshtml.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="bdcce-421">Update the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_OnGetAsync&highlight=1,8,16-999)]

<span data-ttu-id="bdcce-422">Adicione `public int CourseID { get; set; }`</span><span class="sxs-lookup"><span data-stu-id="bdcce-422">Add `public int CourseID { get; set; }`</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_1&highlight=12)]

<span data-ttu-id="bdcce-423">Examine a consulta atualizada:</span><span class="sxs-lookup"><span data-stu-id="bdcce-423">Examine the updated query:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="bdcce-424">A consulta anterior adiciona as entidades `Department`.</span><span class="sxs-lookup"><span data-stu-id="bdcce-424">The preceding query adds the `Department` entities.</span></span>

<span data-ttu-id="bdcce-425">O código a seguir é executado quando o instrutor é selecionado (`id != null`).</span><span class="sxs-lookup"><span data-stu-id="bdcce-425">The following code executes when an instructor is selected (`id != null`).</span></span> <span data-ttu-id="bdcce-426">O instrutor selecionado é recuperado da lista de instrutores no modelo de exibição.</span><span class="sxs-lookup"><span data-stu-id="bdcce-426">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="bdcce-427">Em seguida, a propriedade `Courses` do modelo de exibição é carregada com as entidades `Course` da propriedade de navegação `CourseAssignments` desse instrutor.</span><span class="sxs-lookup"><span data-stu-id="bdcce-427">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ID)]

<span data-ttu-id="bdcce-428">O método `Where` retorna uma coleção.</span><span class="sxs-lookup"><span data-stu-id="bdcce-428">The `Where` method returns a collection.</span></span> <span data-ttu-id="bdcce-429">No método `Where` anterior, uma única entidade `Instructor` é retornada.</span><span class="sxs-lookup"><span data-stu-id="bdcce-429">In the preceding `Where` method, only a single `Instructor` entity is returned.</span></span> <span data-ttu-id="bdcce-430">O método `Single` converte a coleção em uma única entidade `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="bdcce-430">The `Single` method converts the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="bdcce-431">A entidade `Instructor` fornece acesso à propriedade `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="bdcce-431">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="bdcce-432">`CourseAssignments` fornece acesso às entidades `Course` relacionadas.</span><span class="sxs-lookup"><span data-stu-id="bdcce-432">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![Instrutor para Cursos m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="bdcce-434">O método `Single` é usado em uma coleção quando a coleção tem apenas um item.</span><span class="sxs-lookup"><span data-stu-id="bdcce-434">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="bdcce-435">O método `Single` gera uma exceção se a coleção está vazia ou se há mais de um item.</span><span class="sxs-lookup"><span data-stu-id="bdcce-435">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="bdcce-436">Uma alternativa é `SingleOrDefault`, que retorna um valor padrão (nulo, nesse caso) se a coleção está vazia.</span><span class="sxs-lookup"><span data-stu-id="bdcce-436">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span> <span data-ttu-id="bdcce-437">O uso de `SingleOrDefault` é uma coleção vazia:</span><span class="sxs-lookup"><span data-stu-id="bdcce-437">Using `SingleOrDefault` on an empty collection:</span></span>

* <span data-ttu-id="bdcce-438">Resulta em uma exceção (da tentativa de encontrar uma propriedade `Courses` em uma referência nula).</span><span class="sxs-lookup"><span data-stu-id="bdcce-438">Results in an exception (from trying to find a `Courses` property on a null reference).</span></span>
* <span data-ttu-id="bdcce-439">A mensagem de exceção indica menos claramente a causa do problema.</span><span class="sxs-lookup"><span data-stu-id="bdcce-439">The exception message would less clearly indicate the cause of the problem.</span></span>

<span data-ttu-id="bdcce-440">O seguinte código popula a propriedade `Enrollments` do modelo de exibição quando um curso é selecionado:</span><span class="sxs-lookup"><span data-stu-id="bdcce-440">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_courseID)]

<span data-ttu-id="bdcce-441">Adicione a seguinte marcação ao final do Razor Page *Pages/Courses/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="bdcce-441">Add the following markup to the end of the *Pages/Instructors/Index.cshtml* Razor Page:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=60-102&highlight=7-999)]

<span data-ttu-id="bdcce-442">A marcação anterior exibe uma lista de cursos relacionados a um instrutor quando um instrutor é selecionado.</span><span class="sxs-lookup"><span data-stu-id="bdcce-442">The preceding markup displays a list of courses related to an instructor when an instructor is selected.</span></span>

<span data-ttu-id="bdcce-443">Teste o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="bdcce-443">Test the app.</span></span> <span data-ttu-id="bdcce-444">Clique em um link **Selecionar** na página Instrutores.</span><span class="sxs-lookup"><span data-stu-id="bdcce-444">Click on a **Select** link on the instructors page.</span></span>

### <a name="show-student-data"></a><span data-ttu-id="bdcce-445">Mostrar dados de alunos</span><span class="sxs-lookup"><span data-stu-id="bdcce-445">Show student data</span></span>

<span data-ttu-id="bdcce-446">Nesta seção, o aplicativo é atualizado para mostrar os dados de alunos de um curso selecionado.</span><span class="sxs-lookup"><span data-stu-id="bdcce-446">In this section, the app is updated to show the student data for a selected course.</span></span>

<span data-ttu-id="bdcce-447">Atualize a consulta no método `OnGetAsync` em *Pages/Instructors/Index.cshtml.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="bdcce-447">Update the query in the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="bdcce-448">Atualize *Pages/Instructors/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="bdcce-448">Update *Pages/Instructors/Index.cshtml*.</span></span> <span data-ttu-id="bdcce-449">Adicione a seguinte marcação ao final do arquivo:</span><span class="sxs-lookup"><span data-stu-id="bdcce-449">Add the following markup to the end of the file:</span></span>

[!code-html[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=103-)]

<span data-ttu-id="bdcce-450">A marcação anterior exibe uma lista dos alunos registrados no curso selecionado.</span><span class="sxs-lookup"><span data-stu-id="bdcce-450">The preceding markup displays a list of the students who are enrolled in the selected course.</span></span>

<span data-ttu-id="bdcce-451">Atualize a página e selecione um instrutor.</span><span class="sxs-lookup"><span data-stu-id="bdcce-451">Refresh the page and select an instructor.</span></span> <span data-ttu-id="bdcce-452">Selecione um curso para ver a lista de alunos registrados e suas notas.</span><span class="sxs-lookup"><span data-stu-id="bdcce-452">Select a course to see the list of enrolled students and their grades.</span></span>

![Página Índice de Instrutores – instrutor e curso selecionados](read-related-data/_static/instructors-index.png)

## <a name="using-single"></a><span data-ttu-id="bdcce-454">Usando Single</span><span class="sxs-lookup"><span data-stu-id="bdcce-454">Using Single</span></span>

<span data-ttu-id="bdcce-455">O método `Single` pode passar a condição `Where` em vez de chamar o método `Where` separadamente:</span><span class="sxs-lookup"><span data-stu-id="bdcce-455">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="bdcce-456">A abordagem `Single` anterior não oferece nenhum benefício em relação ao uso de `Where`.</span><span class="sxs-lookup"><span data-stu-id="bdcce-456">The preceding `Single` approach provides no benefits over using `Where`.</span></span> <span data-ttu-id="bdcce-457">Alguns desenvolvedores preferem o estilo de abordagem `Single`.</span><span class="sxs-lookup"><span data-stu-id="bdcce-457">Some developers prefer the `Single` approach style.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="bdcce-458">Carregamento explícito</span><span class="sxs-lookup"><span data-stu-id="bdcce-458">Explicit loading</span></span>

<span data-ttu-id="bdcce-459">O código atual especifica o carregamento adiantado para `Enrollments` e `Students`:</span><span class="sxs-lookup"><span data-stu-id="bdcce-459">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="bdcce-460">Suponha que os usuários raramente desejem ver registros em um curso.</span><span class="sxs-lookup"><span data-stu-id="bdcce-460">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="bdcce-461">Nesse caso, uma otimização será carregar apenas os dados de registro se eles forem solicitados.</span><span class="sxs-lookup"><span data-stu-id="bdcce-461">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="bdcce-462">Nesta seção, o `OnGetAsync` é atualizado para usar o carregamento explícito de `Enrollments` e `Students`.</span><span class="sxs-lookup"><span data-stu-id="bdcce-462">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="bdcce-463">Atualize o `OnGetAsync` com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="bdcce-463">Update the `OnGetAsync` with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexXp.cshtml.cs?name=snippet_OnGetAsync&highlight=9-13,29-35)]

<span data-ttu-id="bdcce-464">O código anterior remove as chamadas do método *ThenInclude* para dados de registro e de alunos.</span><span class="sxs-lookup"><span data-stu-id="bdcce-464">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="bdcce-465">Se um curso é selecionado, o código realçado recupera:</span><span class="sxs-lookup"><span data-stu-id="bdcce-465">If a course is selected, the highlighted code retrieves:</span></span>

* <span data-ttu-id="bdcce-466">As entidades `Enrollment` para o curso selecionado.</span><span class="sxs-lookup"><span data-stu-id="bdcce-466">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="bdcce-467">As entidades `Student` para cada `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="bdcce-467">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="bdcce-468">Observe que o código anterior comenta `.AsNoTracking()`.</span><span class="sxs-lookup"><span data-stu-id="bdcce-468">Notice the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="bdcce-469">As propriedades de navegação apenas podem ser carregadas de forma explícita para entidades controladas.</span><span class="sxs-lookup"><span data-stu-id="bdcce-469">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="bdcce-470">Teste o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="bdcce-470">Test the app.</span></span> <span data-ttu-id="bdcce-471">De uma perspectiva dos usuários, o aplicativo se comporta de forma idêntica à versão anterior.</span><span class="sxs-lookup"><span data-stu-id="bdcce-471">From a users perspective, the app behaves identically to the previous version.</span></span>

<span data-ttu-id="bdcce-472">O próximo tutorial mostra como atualizar os dados relacionados.</span><span class="sxs-lookup"><span data-stu-id="bdcce-472">The next tutorial shows how to update related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bdcce-473">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="bdcce-473">Additional resources</span></span>

* [<span data-ttu-id="bdcce-474">Versão do YouTube deste tutorial (parte 1)</span><span class="sxs-lookup"><span data-stu-id="bdcce-474">YouTube version of this tutorial (part1)</span></span>](https://www.youtube.com/watch?v=PzKimUDmrvE)
* [<span data-ttu-id="bdcce-475">Versão do YouTube deste tutorial (parte 2)</span><span class="sxs-lookup"><span data-stu-id="bdcce-475">YouTube version of this tutorial (part2)</span></span>](https://www.youtube.com/watch?v=xvDDrIHv5ko)

>[!div class="step-by-step"]
><span data-ttu-id="bdcce-476">[Anterior](xref:data/ef-rp/complex-data-model)
>[Próximo](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="bdcce-476">[Previous](xref:data/ef-rp/complex-data-model)
[Next](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end