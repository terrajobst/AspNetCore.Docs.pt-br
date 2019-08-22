---
title: Páginas Razor com o EF Core no ASP.NET Core – Atualizar dados relacionados – 7 de 8
author: tdykstra
description: Neste tutorial, você atualizará dados relacionados pela atualização dos campos de chave estrangeira e das propriedades de navegação.
ms.author: riande
ms.date: 07/22/2019
uid: data/ef-rp/update-related-data
ms.openlocfilehash: bc237cf928d852b92c5c1984527129404f88018d
ms.sourcegitcommit: 257cc3fe8c1d61341aa3b07e5bc0fa3d1c1c1d1c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69583500"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---update-related-data---7-of-8"></a><span data-ttu-id="2fe26-103">Páginas Razor com o EF Core no ASP.NET Core – Atualizar dados relacionados – 7 de 8</span><span class="sxs-lookup"><span data-stu-id="2fe26-103">Razor Pages with EF Core in ASP.NET Core - Update Related Data - 7 of 8</span></span>

<span data-ttu-id="2fe26-104">Por [Tom Dykstra](https://github.com/tdykstra) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2fe26-104">By [Tom Dykstra](https://github.com/tdykstra), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2fe26-105">O tutorial mostra como atualizar os dados relacionados.</span><span class="sxs-lookup"><span data-stu-id="2fe26-105">This tutorial shows how to update related data.</span></span> <span data-ttu-id="2fe26-106">As ilustrações a seguir mostram algumas das páginas concluídas.</span><span class="sxs-lookup"><span data-stu-id="2fe26-106">The following illustrations show some of the completed pages.</span></span>

<span data-ttu-id="2fe26-107">![Página Editar Curso](update-related-data/_static/course-edit30.png)
![Página Editar Instrutor](update-related-data/_static/instructor-edit-courses30.png)</span><span class="sxs-lookup"><span data-stu-id="2fe26-107">![Course Edit page](update-related-data/_static/course-edit30.png)
![Instructor Edit page](update-related-data/_static/instructor-edit-courses30.png)</span></span>

## <a name="update-the-course-create-and-edit-pages"></a><span data-ttu-id="2fe26-108">Atualizar o curso criar e editar páginas</span><span class="sxs-lookup"><span data-stu-id="2fe26-108">Update the Course Create and Edit pages</span></span>

<span data-ttu-id="2fe26-109">O código com scaffold das páginas Criar e Editar do Curso tem uma lista suspensa Departamento que mostra a ID do Departamento (um número inteiro).</span><span class="sxs-lookup"><span data-stu-id="2fe26-109">The scaffolded code for the Course Create and Edit pages has a Department drop-down list that shows Department ID (an integer).</span></span> <span data-ttu-id="2fe26-110">A lista suspensa deve mostrar o nome do Departamento, portanto, ambas as páginas precisam de uma lista de nomes de departamento.</span><span class="sxs-lookup"><span data-stu-id="2fe26-110">The drop-down should show the Department name, so both of these pages need a list of department names.</span></span> <span data-ttu-id="2fe26-111">Para fornecer essa lista, use uma classe base para as páginas Criar e Editar.</span><span class="sxs-lookup"><span data-stu-id="2fe26-111">To provide that list, use a base class for the Create and Edit pages.</span></span>

### <a name="create-a-base-class-for-course-create-and-edit"></a><span data-ttu-id="2fe26-112">Criar uma classe base para Criar e Editar o Curso</span><span class="sxs-lookup"><span data-stu-id="2fe26-112">Create a base class for Course Create and Edit</span></span>

<span data-ttu-id="2fe26-113">Crie um arquivo *Pages/Courses/DepartmentNamePageModel.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="2fe26-113">Create a *Pages/Courses/DepartmentNamePageModel.cs* file with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/DepartmentNamePageModel.cs)]

<span data-ttu-id="2fe26-114">O código anterior cria uma [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) para conter a lista de nomes de departamentos.</span><span class="sxs-lookup"><span data-stu-id="2fe26-114">The preceding code creates a [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) to contain the list of department names.</span></span> <span data-ttu-id="2fe26-115">Se `selectedDepartment` for especificado, esse departamento estará selecionado na `SelectList`.</span><span class="sxs-lookup"><span data-stu-id="2fe26-115">If `selectedDepartment` is specified, that department is selected in the `SelectList`.</span></span>

<span data-ttu-id="2fe26-116">As classes de modelo da página Criar e Editar serão derivadas de `DepartmentNamePageModel`.</span><span class="sxs-lookup"><span data-stu-id="2fe26-116">The Create and Edit page model classes will derive from `DepartmentNamePageModel`.</span></span>

### <a name="update-the-course-create-page-model"></a><span data-ttu-id="2fe26-117">Atualizar o modelo de página Criar do Curso</span><span class="sxs-lookup"><span data-stu-id="2fe26-117">Update the Course Create page model</span></span>

<span data-ttu-id="2fe26-118">Um Curso é atribuído a um Departamento.</span><span class="sxs-lookup"><span data-stu-id="2fe26-118">A Course is assigned to a Department.</span></span> <span data-ttu-id="2fe26-119">A classe base para as páginas Criar e Editar fornece um `SelectList` para selecionar o departamento.</span><span class="sxs-lookup"><span data-stu-id="2fe26-119">The base class for the Create and Edit pages provides a `SelectList` for selecting the department.</span></span> <span data-ttu-id="2fe26-120">A lista suspensa que usa `SelectList` define a propriedade de FK (chave estrangeira) `Course.DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="2fe26-120">The drop-down list that uses the `SelectList` sets the `Course.DepartmentID` foreign key (FK) property.</span></span> <span data-ttu-id="2fe26-121">O EF Core usa a `Course.DepartmentID` FK para carregar a propriedade de navegação `Department`.</span><span class="sxs-lookup"><span data-stu-id="2fe26-121">EF Core uses the `Course.DepartmentID` FK to load the `Department` navigation property.</span></span>

![Criar curso](update-related-data/_static/ddl30.png)

<span data-ttu-id="2fe26-123">Atualize *Pages/Courses/Create.cshtml.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="2fe26-123">Update *Pages/Courses/Create.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Create.cshtml.cs?highlight=7,18,27-41)]

<span data-ttu-id="2fe26-124">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="2fe26-124">The preceding code:</span></span>

* <span data-ttu-id="2fe26-125">Deriva de `DepartmentNamePageModel`.</span><span class="sxs-lookup"><span data-stu-id="2fe26-125">Derives from `DepartmentNamePageModel`.</span></span>
* <span data-ttu-id="2fe26-126">Usa `TryUpdateModelAsync` para impedir o [excesso de postagem](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="2fe26-126">Uses `TryUpdateModelAsync` to prevent [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="2fe26-127">Remove `ViewData["DepartmentID"]`.</span><span class="sxs-lookup"><span data-stu-id="2fe26-127">Removes `ViewData["DepartmentID"]`.</span></span> <span data-ttu-id="2fe26-128">`DepartmentNameSL` da classe base é um modelo fortemente tipado e será usado pela página Razor.</span><span class="sxs-lookup"><span data-stu-id="2fe26-128">`DepartmentNameSL` from the base class is a strongly typed model and will be used by the Razor page.</span></span> <span data-ttu-id="2fe26-129">Modelos fortemente tipados são preferíveis aos fracamente tipados.</span><span class="sxs-lookup"><span data-stu-id="2fe26-129">Strongly typed models are preferred over weakly typed.</span></span> <span data-ttu-id="2fe26-130">Para obter mais informações, consulte [Dados fracamente tipados (ViewData e ViewBag)](xref:mvc/views/overview#VD_VB).</span><span class="sxs-lookup"><span data-stu-id="2fe26-130">For more information, see [Weakly typed data (ViewData and ViewBag)](xref:mvc/views/overview#VD_VB).</span></span>

### <a name="update-the-course-create-razor-page"></a><span data-ttu-id="2fe26-131">Atualizar a página do Razor Criar do Curso</span><span class="sxs-lookup"><span data-stu-id="2fe26-131">Update the Course Create Razor page</span></span>

<span data-ttu-id="2fe26-132">Atualize *Pages/Courses/Create.cshtml* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="2fe26-132">Update *Pages/Courses/Create.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Create.cshtml?highlight=29-34)]

<span data-ttu-id="2fe26-133">O código anterior faz as seguintes alterações:</span><span class="sxs-lookup"><span data-stu-id="2fe26-133">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="2fe26-134">Altera a legenda de **DepartmentID** para **Departamento**.</span><span class="sxs-lookup"><span data-stu-id="2fe26-134">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="2fe26-135">Substitui `"ViewBag.DepartmentID"` por `DepartmentNameSL` (da classe base).</span><span class="sxs-lookup"><span data-stu-id="2fe26-135">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>
* <span data-ttu-id="2fe26-136">Adiciona a opção "Selecionar Departamento".</span><span class="sxs-lookup"><span data-stu-id="2fe26-136">Adds the "Select Department" option.</span></span> <span data-ttu-id="2fe26-137">Essa alteração renderiza "Selecionar Departamento" na lista suspensa quando nenhum departamento foi selecionado ainda, em vez do primeiro departamento.</span><span class="sxs-lookup"><span data-stu-id="2fe26-137">This change renders "Select Department" in the drop-down when no department has been selected yet, rather than the first department.</span></span>
* <span data-ttu-id="2fe26-138">Adiciona uma mensagem de validação quando o departamento não está selecionado.</span><span class="sxs-lookup"><span data-stu-id="2fe26-138">Adds a validation message when the department isn't selected.</span></span>

<span data-ttu-id="2fe26-139">A Página do Razor usa a opção [Selecionar Auxiliar de Marcação](xref:mvc/views/working-with-forms#the-select-tag-helper):</span><span class="sxs-lookup"><span data-stu-id="2fe26-139">The Razor Page uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper):</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

<span data-ttu-id="2fe26-140">Teste a página Criar.</span><span class="sxs-lookup"><span data-stu-id="2fe26-140">Test the Create page.</span></span> <span data-ttu-id="2fe26-141">A página Criar exibe o nome do departamento em vez de a ID do departamento.</span><span class="sxs-lookup"><span data-stu-id="2fe26-141">The Create page displays the department name rather than the department ID.</span></span>

### <a name="update-the-course-edit-page-model"></a><span data-ttu-id="2fe26-142">Atualizar o modelo da página Editar do Curso</span><span class="sxs-lookup"><span data-stu-id="2fe26-142">Update the Course Edit page model</span></span>

<span data-ttu-id="2fe26-143">Atualiza *Pages/Courses/Edit.cshtml.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="2fe26-143">Update *Pages/Courses/Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40-66)]

<span data-ttu-id="2fe26-144">As alterações são semelhantes às feitas no modelo da página Criar.</span><span class="sxs-lookup"><span data-stu-id="2fe26-144">The changes are similar to those made in the Create page model.</span></span> <span data-ttu-id="2fe26-145">No código anterior, `PopulateDepartmentsDropDownList` passa a ID do departamento, que seleciona o departamento na lista suspensa.</span><span class="sxs-lookup"><span data-stu-id="2fe26-145">In the preceding code, `PopulateDepartmentsDropDownList` passes in the department ID, which selects that department in the drop-down list.</span></span>

### <a name="update-the-course-edit-razor-page"></a><span data-ttu-id="2fe26-146">Atualizar a página do Razor Editar do Curso</span><span class="sxs-lookup"><span data-stu-id="2fe26-146">Update the Course Edit Razor page</span></span>

<span data-ttu-id="2fe26-147">Atualize *Pages/Courses/Edit.cshtml* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="2fe26-147">Update *Pages/Courses/Edit.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

<span data-ttu-id="2fe26-148">O código anterior faz as seguintes alterações:</span><span class="sxs-lookup"><span data-stu-id="2fe26-148">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="2fe26-149">Exibe a ID do curso.</span><span class="sxs-lookup"><span data-stu-id="2fe26-149">Displays the course ID.</span></span> <span data-ttu-id="2fe26-150">Geralmente, a PK (chave primária) de uma entidade não é exibida.</span><span class="sxs-lookup"><span data-stu-id="2fe26-150">Generally the Primary Key (PK) of an entity isn't displayed.</span></span> <span data-ttu-id="2fe26-151">Em geral, PKs não têm sentido para os usuários.</span><span class="sxs-lookup"><span data-stu-id="2fe26-151">PKs are usually meaningless to users.</span></span> <span data-ttu-id="2fe26-152">Nesse caso, o PK é o número do curso.</span><span class="sxs-lookup"><span data-stu-id="2fe26-152">In this case, the PK is the course number.</span></span>
* <span data-ttu-id="2fe26-153">Altera a legenda da lista suspensa Departamento de **DepartmentID** para **Departamento**.</span><span class="sxs-lookup"><span data-stu-id="2fe26-153">Changes the caption for the Department drop-down from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="2fe26-154">Substitui `"ViewBag.DepartmentID"` por `DepartmentNameSL` (da classe base).</span><span class="sxs-lookup"><span data-stu-id="2fe26-154">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="2fe26-155">A página contém um campo oculto (`<input type="hidden">`) para o número do curso.</span><span class="sxs-lookup"><span data-stu-id="2fe26-155">The page contains a hidden field (`<input type="hidden">`) for the course number.</span></span> <span data-ttu-id="2fe26-156">A adição de um auxiliar de marcação `<label>` com `asp-for="Course.CourseID"` não elimina a necessidade do campo oculto.</span><span class="sxs-lookup"><span data-stu-id="2fe26-156">Adding a `<label>` tag helper with `asp-for="Course.CourseID"` doesn't eliminate the need for the hidden field.</span></span> <span data-ttu-id="2fe26-157">`<input type="hidden">` é necessário para que o número seja incluído nos dados postados quando o usuário clicar em **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="2fe26-157">`<input type="hidden">` is required for the course number to be included in the posted data when the user clicks **Save**.</span></span>

## <a name="update-the-course-details-and-delete-pages"></a><span data-ttu-id="2fe26-158">Atualizar as páginas Excluir e Detalhes do Curso</span><span class="sxs-lookup"><span data-stu-id="2fe26-158">Update the Course Details and Delete pages</span></span>

<span data-ttu-id="2fe26-159">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) pode melhorar o desempenho quando o acompanhamento não é necessário.</span><span class="sxs-lookup"><span data-stu-id="2fe26-159">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) can improve performance when tracking isn't required.</span></span>

### <a name="update-the-course-page-models"></a><span data-ttu-id="2fe26-160">Atualizar os modelos de página do Curso</span><span class="sxs-lookup"><span data-stu-id="2fe26-160">Update the Course page models</span></span>

<span data-ttu-id="2fe26-161">Atualize *Pages/Courses/Delete.cshtml* com o seguinte código para adicionar `AsNoTracking`:</span><span class="sxs-lookup"><span data-stu-id="2fe26-161">Update *Pages/Courses/Delete.cshtml.cs* with the following code to add `AsNoTracking`:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Delete.cshtml.cs?highlight=29)]

<span data-ttu-id="2fe26-162">Faça a mesma alteração no arquivo *Pages/Courses/details.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="2fe26-162">Make the same change in the *Pages/Courses/Details.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Details.cshtml.cs?highlight=28)]

### <a name="update-the-course-razor-pages"></a><span data-ttu-id="2fe26-163">Atualizar as páginas do Razor do Curso</span><span class="sxs-lookup"><span data-stu-id="2fe26-163">Update the Course Razor pages</span></span>

<span data-ttu-id="2fe26-164">Atualize *Pages/Courses/Delete.cshtml* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="2fe26-164">Update *Pages/Courses/Delete.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Delete.cshtml?highlight=15-20,37)]

<span data-ttu-id="2fe26-165">Faça as mesmas alterações na página Detalhes.</span><span class="sxs-lookup"><span data-stu-id="2fe26-165">Make the same changes to the Details page.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Details.cshtml?highlight=14-19,36)]

## <a name="test-the-course-pages"></a><span data-ttu-id="2fe26-166">Testar as páginas Curso</span><span class="sxs-lookup"><span data-stu-id="2fe26-166">Test the Course pages</span></span>

<span data-ttu-id="2fe26-167">Teste as páginas criar, editar, detalhes e excluir.</span><span class="sxs-lookup"><span data-stu-id="2fe26-167">Test the create, edit, details, and delete pages.</span></span>

## <a name="update-the-instructor-create-and-edit-pages"></a><span data-ttu-id="2fe26-168">Atualizar as páginas Criar e Editar do instrutor</span><span class="sxs-lookup"><span data-stu-id="2fe26-168">Update the instructor Create and Edit pages</span></span>

<span data-ttu-id="2fe26-169">Os instrutores podem ministrar a quantidade de cursos que desejarem.</span><span class="sxs-lookup"><span data-stu-id="2fe26-169">Instructors may teach any number of courses.</span></span> <span data-ttu-id="2fe26-170">A imagem a seguir mostra a página Editar do instrutor com uma matriz de caixas de seleção do curso.</span><span class="sxs-lookup"><span data-stu-id="2fe26-170">The following image shows the instructor Edit page with an array of course checkboxes.</span></span>

![Página Editar Instrutor com cursos](update-related-data/_static/instructor-edit-courses30.png)

<span data-ttu-id="2fe26-172">As caixas de seleção permitem alterações em cursos aos quais um instrutor é atribuído.</span><span class="sxs-lookup"><span data-stu-id="2fe26-172">The checkboxes enable changes to courses an instructor is assigned to.</span></span> <span data-ttu-id="2fe26-173">Uma caixa de seleção é exibida para cada curso no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="2fe26-173">A checkbox is displayed for every course in the database.</span></span> <span data-ttu-id="2fe26-174">Os cursos aos quais o instrutor é atribuído são selecionados.</span><span class="sxs-lookup"><span data-stu-id="2fe26-174">Courses that the instructor is assigned to are selected.</span></span> <span data-ttu-id="2fe26-175">O usuário pode marcar ou desmarcar as caixas de seleção para alterar as atribuições de curso.</span><span class="sxs-lookup"><span data-stu-id="2fe26-175">The user can select or clear checkboxes to change course assignments.</span></span> <span data-ttu-id="2fe26-176">Se o número de cursos fosse muito maior, uma interface do usuário diferente poderia funcionar melhor.</span><span class="sxs-lookup"><span data-stu-id="2fe26-176">If the number of courses were much greater, a different UI might work better.</span></span> <span data-ttu-id="2fe26-177">Porém, o método de gerenciar uma relação muitos para muitos mostrada aqui não mudaria.</span><span class="sxs-lookup"><span data-stu-id="2fe26-177">But the method of managing a many-to-many relationship shown here wouldn't change.</span></span> <span data-ttu-id="2fe26-178">Para criar ou excluir relacionamentos, você manipula uma entidade de junção.</span><span class="sxs-lookup"><span data-stu-id="2fe26-178">To create or delete relationships, you manipulate a join entity.</span></span>

### <a name="create-a-class-for-assigned-courses-data"></a><span data-ttu-id="2fe26-179">Criar uma classe para dados de cursos atribuídos</span><span class="sxs-lookup"><span data-stu-id="2fe26-179">Create a class for assigned courses data</span></span>

<span data-ttu-id="2fe26-180">Crie *SchoolViewModels/AssignedCourseData.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="2fe26-180">Create *SchoolViewModels/AssignedCourseData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/AssignedCourseData.cs)]

<span data-ttu-id="2fe26-181">A classe `AssignedCourseData` contém dados para criar as caixas de seleção para os cursos atribuídos a um instrutor.</span><span class="sxs-lookup"><span data-stu-id="2fe26-181">The `AssignedCourseData` class contains data to create the check boxes for courses assigned to an instructor.</span></span>

### <a name="create-an-instructor-page-model-base-class"></a><span data-ttu-id="2fe26-182">Criar uma classe base de modelo de página do Instrutor</span><span class="sxs-lookup"><span data-stu-id="2fe26-182">Create an Instructor page model base class</span></span>

<span data-ttu-id="2fe26-183">Crie a classe base *Pages/Instructors/InstructorCoursesPageModel.cs*:</span><span class="sxs-lookup"><span data-stu-id="2fe26-183">Create the *Pages/Instructors/InstructorCoursesPageModel.cs* base class:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_All)]

<span data-ttu-id="2fe26-184">A `InstructorCoursesPageModel` é a classe base que será usada para os modelos de página Editar e Criar.</span><span class="sxs-lookup"><span data-stu-id="2fe26-184">The `InstructorCoursesPageModel` is the base class you will use for the Edit and Create page models.</span></span> <span data-ttu-id="2fe26-185">`PopulateAssignedCourseData` lê todas as entidades `Course` para popular `AssignedCourseDataList`.</span><span class="sxs-lookup"><span data-stu-id="2fe26-185">`PopulateAssignedCourseData` reads all `Course` entities to populate `AssignedCourseDataList`.</span></span> <span data-ttu-id="2fe26-186">Para cada curso, o código define a `CourseID`, o título e se o instrutor está ou não atribuído ao curso.</span><span class="sxs-lookup"><span data-stu-id="2fe26-186">For each course, the code sets the `CourseID`, title, and whether or not the instructor is assigned to the course.</span></span> <span data-ttu-id="2fe26-187">Um [HashSet](/dotnet/api/system.collections.generic.hashset-1) é usado para pesquisas eficientes.</span><span class="sxs-lookup"><span data-stu-id="2fe26-187">A [HashSet](/dotnet/api/system.collections.generic.hashset-1) is used for efficient lookups.</span></span>

<span data-ttu-id="2fe26-188">Como a página do Razor não tem uma coleção de entidades Course, o associador de modelos não pode atualizar automaticamente a propriedade de navegação `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="2fe26-188">Since the Razor page doesn't have a collection of Course entities, the model binder can't automatically update the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="2fe26-189">Em vez de usar o associador de modelos para atualizar a propriedade de navegação `CourseAssignments`, faça isso no novo método `UpdateInstructorCourses`.</span><span class="sxs-lookup"><span data-stu-id="2fe26-189">Instead of using the model binder to update the `CourseAssignments` navigation property, you do that in the new `UpdateInstructorCourses` method.</span></span> <span data-ttu-id="2fe26-190">Portanto, você precisa excluir a propriedade `CourseAssignments` do model binding.</span><span class="sxs-lookup"><span data-stu-id="2fe26-190">Therefore you need to exclude the `CourseAssignments` property from model binding.</span></span> <span data-ttu-id="2fe26-191">Isso não exige nenhuma alteração no código que chama `TryUpdateModel`, porque você está usando a sobrecarga da lista de permissões e `CourseAssignments` não está na lista de inclusões.</span><span class="sxs-lookup"><span data-stu-id="2fe26-191">This doesn't require any change to the code that calls `TryUpdateModel` because you're using the whitelisting overload and `CourseAssignments` isn't in the include list.</span></span>

<span data-ttu-id="2fe26-192">Se nenhuma caixa de seleção foi marcada, o código em `UpdateInstructorCourses` inicializa a propriedade de navegação `CourseAssignments` com uma coleção vazia e retorna:</span><span class="sxs-lookup"><span data-stu-id="2fe26-192">If no check boxes were selected, the code in `UpdateInstructorCourses` initializes the `CourseAssignments` navigation property with an empty collection and returns:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_IfNull)]

<span data-ttu-id="2fe26-193">Em seguida, o código executa um loop em todos os cursos no banco de dados e verifica cada curso em relação àqueles atribuídos no momento ao instrutor e em relação àqueles que foram selecionados na página.</span><span class="sxs-lookup"><span data-stu-id="2fe26-193">The code then loops through all courses in the database and checks each course against the ones currently assigned to the instructor versus the ones that were selected in the page.</span></span> <span data-ttu-id="2fe26-194">Para facilitar pesquisas eficientes, as últimas duas coleções são armazenadas em objetos `HashSet`.</span><span class="sxs-lookup"><span data-stu-id="2fe26-194">To facilitate efficient lookups, the latter two collections are stored in `HashSet` objects.</span></span>

<span data-ttu-id="2fe26-195">Se a caixa de seleção para um curso foi marcada, mas o curso não está na propriedade de navegação `Instructor.CourseAssignments`, o curso é adicionado à coleção na propriedade de navegação.</span><span class="sxs-lookup"><span data-stu-id="2fe26-195">If the check box for a course was selected but the course isn't in the `Instructor.CourseAssignments` navigation property, the course is added to the collection in the navigation property.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_UpdateCourses)]

<span data-ttu-id="2fe26-196">Se a caixa de seleção para um curso não foi marcada, mas o curso está na propriedade de navegação `Instructor.CourseAssignments`, o curso é removido da propriedade de navegação.</span><span class="sxs-lookup"><span data-stu-id="2fe26-196">If the check box for a course wasn't selected, but the course is in the `Instructor.CourseAssignments` navigation property, the course is removed from the navigation property.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/InstructorCoursesPageModel.cs?name=snippet_UpdateCoursesElse)]

### <a name="handle-office-location"></a><span data-ttu-id="2fe26-197">Processar o local do escritório</span><span class="sxs-lookup"><span data-stu-id="2fe26-197">Handle office location</span></span>

<span data-ttu-id="2fe26-198">Outra relação que a página de edição precisa tratar é a relação de um para zero ou um que a entidade Instrutor tem com a entidade `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="2fe26-198">Another relationship the edit page has to handle is the one-to-zero-or-one relationship that the Instructor entity has with the `OfficeAssignment` entity.</span></span> <span data-ttu-id="2fe26-199">O código de edição do instrutor deve lidar com os seguintes cenários:</span><span class="sxs-lookup"><span data-stu-id="2fe26-199">The instructor edit code must handle the following scenarios:</span></span> 

* <span data-ttu-id="2fe26-200">Se o usuário limpar a atribuição de escritório, exclua a entidade `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="2fe26-200">If the user clears the office assignment, delete the `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="2fe26-201">Se o usuário inserir uma atribuição de escritório e ela estava vazia, crie uma nova entidade `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="2fe26-201">If the user enters an office assignment and it was empty, create a new `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="2fe26-202">Se o usuário alterar a atribuição de escritório, atualize a entidade `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="2fe26-202">If the user changes the office assignment, update the `OfficeAssignment` entity.</span></span>

### <a name="update-the-instructor-edit-page-model"></a><span data-ttu-id="2fe26-203">Atualizar o modelo de página Editar do Instrutor</span><span class="sxs-lookup"><span data-stu-id="2fe26-203">Update the Instructor Edit page model</span></span>

<span data-ttu-id="2fe26-204">Atualize *Pages/Instructors/Edit.cshtml.cs* com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="2fe26-204">Update *Pages/Instructors/Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Edit.cshtml.cs?name=snippet_All&highlight=9,28-32,38,42-77)]

<span data-ttu-id="2fe26-205">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="2fe26-205">The preceding code:</span></span>

* <span data-ttu-id="2fe26-206">Obtém a entidade `Instructor` atual do banco de dados usando o carregamento adiantado para as propriedades de navegação `OfficeAssignment`, `CourseAssignment` e `CourseAssignment.Course`.</span><span class="sxs-lookup"><span data-stu-id="2fe26-206">Gets the current `Instructor` entity from the database using eager loading for the `OfficeAssignment`, `CourseAssignment`, and `CourseAssignment.Course` navigation properties.</span></span>
* <span data-ttu-id="2fe26-207">Atualiza a entidade `Instructor` recuperada com valores do associador de modelos.</span><span class="sxs-lookup"><span data-stu-id="2fe26-207">Updates the retrieved `Instructor` entity with values from the model binder.</span></span> <span data-ttu-id="2fe26-208">`TryUpdateModel` impede o [excesso de postagem](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="2fe26-208">`TryUpdateModel` prevents [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="2fe26-209">Se o local do escritório estiver em branco, `Instructor.OfficeAssignment` será definido como nulo.</span><span class="sxs-lookup"><span data-stu-id="2fe26-209">If the office location is blank, sets `Instructor.OfficeAssignment` to null.</span></span> <span data-ttu-id="2fe26-210">Quando `Instructor.OfficeAssignment` é nulo, a linha relacionada na tabela `OfficeAssignment` é excluída.</span><span class="sxs-lookup"><span data-stu-id="2fe26-210">When `Instructor.OfficeAssignment` is null, the related row in the `OfficeAssignment` table is deleted.</span></span>
* <span data-ttu-id="2fe26-211">Chama `PopulateAssignedCourseData` em `OnGetAsync` para fornecer informações para as caixas de seleção usando a classe do modelo de exibição `AssignedCourseData`.</span><span class="sxs-lookup"><span data-stu-id="2fe26-211">Calls `PopulateAssignedCourseData` in `OnGetAsync` to provide information for the checkboxes using the `AssignedCourseData` view model class.</span></span>
* <span data-ttu-id="2fe26-212">Chama `UpdateInstructorCourses` em `OnPostAsync` para aplicar informações das caixas de seleção à entidade do instrutor que está sendo editada.</span><span class="sxs-lookup"><span data-stu-id="2fe26-212">Calls `UpdateInstructorCourses` in `OnPostAsync` to apply information from the checkboxes to the Instructor entity being edited.</span></span>
* <span data-ttu-id="2fe26-213">Chamará `PopulateAssignedCourseData` e `UpdateInstructorCourses` em `OnPostAsync` se `TryUpdateModel` falhar.</span><span class="sxs-lookup"><span data-stu-id="2fe26-213">Calls `PopulateAssignedCourseData` and `UpdateInstructorCourses` in `OnPostAsync` if `TryUpdateModel` fails.</span></span> <span data-ttu-id="2fe26-214">Essas chamadas de método restauram os dados de curso atribuídos inseridos na página quando são exibidos novamente com uma mensagem de erro.</span><span class="sxs-lookup"><span data-stu-id="2fe26-214">These method calls restore the assigned course data entered on the page when it is redisplayed with an error message.</span></span>

### <a name="update-the-instructor-edit-razor-page"></a><span data-ttu-id="2fe26-215">Atualizar a página do Razor Editar do Instrutor</span><span class="sxs-lookup"><span data-stu-id="2fe26-215">Update the Instructor Edit Razor page</span></span>

<span data-ttu-id="2fe26-216">Atualize *Pages/Instructors/Edit.cshtml* com o código a seguir:</span><span class="sxs-lookup"><span data-stu-id="2fe26-216">Update *Pages/Instructors/Edit.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Edit.cshtml?highlight=29-59)]

<span data-ttu-id="2fe26-217">Esse código anterior cria uma tabela HTML que contém três colunas.</span><span class="sxs-lookup"><span data-stu-id="2fe26-217">The preceding code creates an HTML table that has three columns.</span></span> <span data-ttu-id="2fe26-218">Cada coluna tem uma caixa de seleção e uma legenda que contém o número e o título do curso.</span><span class="sxs-lookup"><span data-stu-id="2fe26-218">Each column has a checkbox and a caption containing the course number and title.</span></span> <span data-ttu-id="2fe26-219">Todas as caixas de seleção têm o mesmo nome ("selectedCourses").</span><span class="sxs-lookup"><span data-stu-id="2fe26-219">The checkboxes all have the same name ("selectedCourses").</span></span> <span data-ttu-id="2fe26-220">O uso do mesmo nome instrui o associador de modelos a tratá-las como um grupo.</span><span class="sxs-lookup"><span data-stu-id="2fe26-220">Using the same name informs the model binder to treat them as a group.</span></span> <span data-ttu-id="2fe26-221">O atributo de valor de cada caixa de seleção é definido como `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="2fe26-221">The value attribute of each checkbox is set to `CourseID`.</span></span> <span data-ttu-id="2fe26-222">Quando a página é postada, o associador de modelos passa uma matriz que consiste nos valores `CourseID` para apenas as caixas de seleção marcadas.</span><span class="sxs-lookup"><span data-stu-id="2fe26-222">When the page is posted, the model binder passes an array that consists of the `CourseID` values for only the checkboxes that are selected.</span></span>

<span data-ttu-id="2fe26-223">Quando as caixas de seleção são inicialmente renderizadas, os cursos atribuídos ao instrutor são selecionados.</span><span class="sxs-lookup"><span data-stu-id="2fe26-223">When the checkboxes are initially rendered, courses assigned to the instructor are selected.</span></span>

<span data-ttu-id="2fe26-224">Observação: A abordagem usada aqui para editar os dados de curso do instrutor funciona bem quando há uma quantidade limitada de cursos.</span><span class="sxs-lookup"><span data-stu-id="2fe26-224">Note: The approach taken here to edit instructor course data works well when there's a limited number of courses.</span></span> <span data-ttu-id="2fe26-225">Para coleções muito maiores, uma interface do usuário e um método de atualização diferentes são mais utilizáveis e eficientes.</span><span class="sxs-lookup"><span data-stu-id="2fe26-225">For collections that are much larger, a different UI and a different updating method would be more useable and efficient.</span></span>

<span data-ttu-id="2fe26-226">Execute o aplicativo e teste a página Editar de Instrutores atualizada.</span><span class="sxs-lookup"><span data-stu-id="2fe26-226">Run the app and test the updated Instructors Edit page.</span></span> <span data-ttu-id="2fe26-227">Altere algumas atribuições de curso.</span><span class="sxs-lookup"><span data-stu-id="2fe26-227">Change some course assignments.</span></span> <span data-ttu-id="2fe26-228">As alterações são refletidas na página Índice.</span><span class="sxs-lookup"><span data-stu-id="2fe26-228">The changes are reflected on the Index page.</span></span>

### <a name="update-the-instructor-create-page"></a><span data-ttu-id="2fe26-229">Atualizar a página Criar do Instrutor</span><span class="sxs-lookup"><span data-stu-id="2fe26-229">Update the Instructor Create page</span></span>

<span data-ttu-id="2fe26-230">Atualize o modelo de página Criar do Instrutor e a página do Razor com código semelhante ao da página Editar:</span><span class="sxs-lookup"><span data-stu-id="2fe26-230">Update the Instructor Create page model and Razor page with code similar to the Edit page:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Create.cshtml.cs)]

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Create.cshtml)]

<span data-ttu-id="2fe26-231">Teste a página Criar Instrutor.</span><span class="sxs-lookup"><span data-stu-id="2fe26-231">Test the instructor Create page.</span></span>

## <a name="update-the-instructor-delete-page"></a><span data-ttu-id="2fe26-232">Atualizar a página Excluir do Instrutor</span><span class="sxs-lookup"><span data-stu-id="2fe26-232">Update the Instructor Delete page</span></span>

<span data-ttu-id="2fe26-233">Atualize *Pages/Instructors/Delete.cshtml.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="2fe26-233">Update *Pages/Instructors/Delete.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Delete.cshtml.cs?highlight=45-61)]

<span data-ttu-id="2fe26-234">O código anterior faz as seguintes alterações:</span><span class="sxs-lookup"><span data-stu-id="2fe26-234">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="2fe26-235">Usa o carregamento adiantado para a propriedade de navegação `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="2fe26-235">Uses eager loading for the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="2fe26-236">`CourseAssignments` deve ser incluído ou eles não são excluídos quando o instrutor é excluído.</span><span class="sxs-lookup"><span data-stu-id="2fe26-236">`CourseAssignments` must be included or they aren't deleted when the instructor is deleted.</span></span> <span data-ttu-id="2fe26-237">Para evitar a necessidade de lê-las, configure a exclusão em cascata no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="2fe26-237">To avoid needing to read them, configure cascade delete in the database.</span></span>

* <span data-ttu-id="2fe26-238">Se o instrutor a ser excluído é atribuído como administrador de qualquer departamento, remove a atribuição de instrutor desse departamento.</span><span class="sxs-lookup"><span data-stu-id="2fe26-238">If the instructor to be deleted is assigned as administrator of any departments, removes the instructor assignment from those departments.</span></span>

<span data-ttu-id="2fe26-239">Execute o aplicativo e teste a página Excluir.</span><span class="sxs-lookup"><span data-stu-id="2fe26-239">Run the app and test the Delete page.</span></span>

## <a name="next-steps"></a><span data-ttu-id="2fe26-240">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="2fe26-240">Next steps</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="2fe26-241">[Tutorial anterior](xref:data/ef-rp/read-related-data)
> [Próximo tutorial](xref:data/ef-rp/concurrency)</span><span class="sxs-lookup"><span data-stu-id="2fe26-241">[Previous tutorial](xref:data/ef-rp/read-related-data)
[Next tutorial](xref:data/ef-rp/concurrency)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2fe26-242">Este tutorial demonstra como atualizar dados relacionados.</span><span class="sxs-lookup"><span data-stu-id="2fe26-242">This tutorial demonstrates updating related data.</span></span> <span data-ttu-id="2fe26-243">Caso tenha problemas que não consiga resolver, [baixe ou exiba o aplicativo concluído.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span><span class="sxs-lookup"><span data-stu-id="2fe26-243">If you run into problems you can't solve, [download or view the completed app.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="2fe26-244">[Instruções de download](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="2fe26-244">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="2fe26-245">As ilustrações a seguir mostram algumas das páginas concluídas.</span><span class="sxs-lookup"><span data-stu-id="2fe26-245">The following illustrations shows some of the completed pages.</span></span>

<span data-ttu-id="2fe26-246">![Página Editar Curso](update-related-data/_static/course-edit.png)
![Página Editar Instrutor](update-related-data/_static/instructor-edit-courses.png)</span><span class="sxs-lookup"><span data-stu-id="2fe26-246">![Course Edit page](update-related-data/_static/course-edit.png)
![Instructor Edit page](update-related-data/_static/instructor-edit-courses.png)</span></span>

<span data-ttu-id="2fe26-247">Examine e teste as páginas de cursos Criar e Editar.</span><span class="sxs-lookup"><span data-stu-id="2fe26-247">Examine and test the Create and Edit course pages.</span></span> <span data-ttu-id="2fe26-248">Crie um novo curso.</span><span class="sxs-lookup"><span data-stu-id="2fe26-248">Create a new course.</span></span> <span data-ttu-id="2fe26-249">O departamento é selecionado por sua chave primária (um inteiro), não pelo nome.</span><span class="sxs-lookup"><span data-stu-id="2fe26-249">The department is selected by its primary key (an integer), not its name.</span></span> <span data-ttu-id="2fe26-250">Edite o novo curso.</span><span class="sxs-lookup"><span data-stu-id="2fe26-250">Edit the new course.</span></span> <span data-ttu-id="2fe26-251">Quando concluir o teste, exclua o novo curso.</span><span class="sxs-lookup"><span data-stu-id="2fe26-251">When you have finished testing, delete the new course.</span></span>

## <a name="create-a-base-class-to-share-common-code"></a><span data-ttu-id="2fe26-252">Criar uma classe base para compartilhar um código comum</span><span class="sxs-lookup"><span data-stu-id="2fe26-252">Create a base class to share common code</span></span>

<span data-ttu-id="2fe26-253">As páginas Cursos/Criar e Cursos/Editar precisam de uma lista de nomes de departamentos.</span><span class="sxs-lookup"><span data-stu-id="2fe26-253">The Courses/Create and Courses/Edit pages each need a list of department names.</span></span> <span data-ttu-id="2fe26-254">Crie a classe base *Pages/Courses/DepartmentNamePageModel.cshtml.cs* para as páginas Criar e Editar:</span><span class="sxs-lookup"><span data-stu-id="2fe26-254">Create the *Pages/Courses/DepartmentNamePageModel.cshtml.cs* base class for the Create and Edit pages:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/DepartmentNamePageModel.cshtml.cs?highlight=9,11,20-21)]

<span data-ttu-id="2fe26-255">O código anterior cria uma [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) para conter a lista de nomes de departamentos.</span><span class="sxs-lookup"><span data-stu-id="2fe26-255">The preceding code creates a [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) to contain the list of department names.</span></span> <span data-ttu-id="2fe26-256">Se `selectedDepartment` for especificado, esse departamento estará selecionado na `SelectList`.</span><span class="sxs-lookup"><span data-stu-id="2fe26-256">If `selectedDepartment` is specified, that department is selected in the `SelectList`.</span></span>

<span data-ttu-id="2fe26-257">As classes de modelo da página Criar e Editar serão derivadas de `DepartmentNamePageModel`.</span><span class="sxs-lookup"><span data-stu-id="2fe26-257">The Create and Edit page model classes will derive from `DepartmentNamePageModel`.</span></span>

## <a name="customize-the-courses-pages"></a><span data-ttu-id="2fe26-258">Personalizar as páginas Courses</span><span class="sxs-lookup"><span data-stu-id="2fe26-258">Customize the Courses Pages</span></span>

<span data-ttu-id="2fe26-259">Quando uma nova entidade de curso é criada, ela precisa ter uma relação com um departamento existente.</span><span class="sxs-lookup"><span data-stu-id="2fe26-259">When a new course entity is created, it must have a relationship to an existing department.</span></span> <span data-ttu-id="2fe26-260">Para adicionar um departamento durante a criação de um curso, a classe base para Create e Edit contém uma lista suspensa para seleção do departamento.</span><span class="sxs-lookup"><span data-stu-id="2fe26-260">To add a department while creating a course, the base class for Create and Edit contains a drop-down list for selecting the department.</span></span> <span data-ttu-id="2fe26-261">A lista suspensa define a propriedade `Course.DepartmentID` de FK (chave estrangeira).</span><span class="sxs-lookup"><span data-stu-id="2fe26-261">The drop-down list sets the `Course.DepartmentID` foreign key (FK) property.</span></span> <span data-ttu-id="2fe26-262">O EF Core usa a `Course.DepartmentID` FK para carregar a propriedade de navegação `Department`.</span><span class="sxs-lookup"><span data-stu-id="2fe26-262">EF Core uses the `Course.DepartmentID` FK to load the `Department` navigation property.</span></span>

![Criar curso](update-related-data/_static/ddl.png)

<span data-ttu-id="2fe26-264">Atualize o modelo da página Criar com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="2fe26-264">Update the Create page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Create.cshtml.cs?highlight=7,18,32-999)]

<span data-ttu-id="2fe26-265">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="2fe26-265">The preceding code:</span></span>

* <span data-ttu-id="2fe26-266">Deriva de `DepartmentNamePageModel`.</span><span class="sxs-lookup"><span data-stu-id="2fe26-266">Derives from `DepartmentNamePageModel`.</span></span>
* <span data-ttu-id="2fe26-267">Usa `TryUpdateModelAsync` para impedir o [excesso de postagem](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="2fe26-267">Uses `TryUpdateModelAsync` to prevent [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="2fe26-268">Substitui `ViewData["DepartmentID"]` por `DepartmentNameSL` (da classe base).</span><span class="sxs-lookup"><span data-stu-id="2fe26-268">Replaces `ViewData["DepartmentID"]` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="2fe26-269">`ViewData["DepartmentID"]` é substituído pelo `DepartmentNameSL` fortemente tipado.</span><span class="sxs-lookup"><span data-stu-id="2fe26-269">`ViewData["DepartmentID"]` is replaced with the strongly typed `DepartmentNameSL`.</span></span> <span data-ttu-id="2fe26-270">Modelos fortemente tipados são preferíveis aos fracamente tipados.</span><span class="sxs-lookup"><span data-stu-id="2fe26-270">Strongly typed models are preferred over weakly typed.</span></span> <span data-ttu-id="2fe26-271">Para obter mais informações, consulte [Dados fracamente tipados (ViewData e ViewBag)](xref:mvc/views/overview#VD_VB).</span><span class="sxs-lookup"><span data-stu-id="2fe26-271">For more information, see [Weakly typed data (ViewData and ViewBag)](xref:mvc/views/overview#VD_VB).</span></span>

### <a name="update-the-courses-create-page"></a><span data-ttu-id="2fe26-272">Atualizar a página Criar Cursos</span><span class="sxs-lookup"><span data-stu-id="2fe26-272">Update the Courses Create page</span></span>

<span data-ttu-id="2fe26-273">Atualize *Pages/Courses/Create.cshtml* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="2fe26-273">Update *Pages/Courses/Create.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?highlight=29-34)]

<span data-ttu-id="2fe26-274">A marcação anterior faz as seguintes alterações:</span><span class="sxs-lookup"><span data-stu-id="2fe26-274">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="2fe26-275">Altera a legenda de **DepartmentID** para **Departamento**.</span><span class="sxs-lookup"><span data-stu-id="2fe26-275">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="2fe26-276">Substitui `"ViewBag.DepartmentID"` por `DepartmentNameSL` (da classe base).</span><span class="sxs-lookup"><span data-stu-id="2fe26-276">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>
* <span data-ttu-id="2fe26-277">Adiciona a opção "Selecionar Departamento".</span><span class="sxs-lookup"><span data-stu-id="2fe26-277">Adds the "Select Department" option.</span></span> <span data-ttu-id="2fe26-278">Essa alteração renderiza "Selecionar Departamento", em vez do departamento primeiro.</span><span class="sxs-lookup"><span data-stu-id="2fe26-278">This change renders "Select Department" rather than the first department.</span></span>
* <span data-ttu-id="2fe26-279">Adiciona uma mensagem de validação quando o departamento não está selecionado.</span><span class="sxs-lookup"><span data-stu-id="2fe26-279">Adds a validation message when the department isn't selected.</span></span>

<span data-ttu-id="2fe26-280">A Página do Razor usa a opção [Selecionar Auxiliar de Marcação](xref:mvc/views/working-with-forms#the-select-tag-helper):</span><span class="sxs-lookup"><span data-stu-id="2fe26-280">The Razor Page uses the [Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper):</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

<span data-ttu-id="2fe26-281">Teste a página Criar.</span><span class="sxs-lookup"><span data-stu-id="2fe26-281">Test the Create page.</span></span> <span data-ttu-id="2fe26-282">A página Criar exibe o nome do departamento em vez de a ID do departamento.</span><span class="sxs-lookup"><span data-stu-id="2fe26-282">The Create page displays the department name rather than the department ID.</span></span>

### <a name="update-the-courses-edit-page"></a><span data-ttu-id="2fe26-283">Atualize a página Editar Cursos.</span><span class="sxs-lookup"><span data-stu-id="2fe26-283">Update the Courses Edit page.</span></span>

<span data-ttu-id="2fe26-284">Substitua o código em *Pages/Courses/Edit.cshtml.cs* pelo seguinte código:</span><span class="sxs-lookup"><span data-stu-id="2fe26-284">Replace the code in *Pages/Courses/Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40,47-999)]

<span data-ttu-id="2fe26-285">As alterações são semelhantes às feitas no modelo da página Criar.</span><span class="sxs-lookup"><span data-stu-id="2fe26-285">The changes are similar to those made in the Create page model.</span></span> <span data-ttu-id="2fe26-286">No código anterior, `PopulateDepartmentsDropDownList` passa a ID do departamento, que seleciona o departamento especificado na lista suspensa.</span><span class="sxs-lookup"><span data-stu-id="2fe26-286">In the preceding code, `PopulateDepartmentsDropDownList` passes in the department ID, which select the department specified in the drop-down list.</span></span>

<span data-ttu-id="2fe26-287">Atualize *Pages/Courses/Edit.cshtml* com a seguinte marcação:</span><span class="sxs-lookup"><span data-stu-id="2fe26-287">Update *Pages/Courses/Edit.cshtml* with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

<span data-ttu-id="2fe26-288">A marcação anterior faz as seguintes alterações:</span><span class="sxs-lookup"><span data-stu-id="2fe26-288">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="2fe26-289">Exibe a ID do curso.</span><span class="sxs-lookup"><span data-stu-id="2fe26-289">Displays the course ID.</span></span> <span data-ttu-id="2fe26-290">Geralmente, a PK (chave primária) de uma entidade não é exibida.</span><span class="sxs-lookup"><span data-stu-id="2fe26-290">Generally the Primary Key (PK) of an entity isn't displayed.</span></span> <span data-ttu-id="2fe26-291">Em geral, PKs não têm sentido para os usuários.</span><span class="sxs-lookup"><span data-stu-id="2fe26-291">PKs are usually meaningless to users.</span></span> <span data-ttu-id="2fe26-292">Nesse caso, o PK é o número do curso.</span><span class="sxs-lookup"><span data-stu-id="2fe26-292">In this case, the PK is the course number.</span></span>
* <span data-ttu-id="2fe26-293">Altera a legenda de **DepartmentID** para **Departamento**.</span><span class="sxs-lookup"><span data-stu-id="2fe26-293">Changes the caption from **DepartmentID** to **Department**.</span></span>
* <span data-ttu-id="2fe26-294">Substitui `"ViewBag.DepartmentID"` por `DepartmentNameSL` (da classe base).</span><span class="sxs-lookup"><span data-stu-id="2fe26-294">Replaces `"ViewBag.DepartmentID"` with `DepartmentNameSL` (from the base class).</span></span>

<span data-ttu-id="2fe26-295">A página contém um campo oculto (`<input type="hidden">`) para o número do curso.</span><span class="sxs-lookup"><span data-stu-id="2fe26-295">The page contains a hidden field (`<input type="hidden">`) for the course number.</span></span> <span data-ttu-id="2fe26-296">A adição de um auxiliar de marcação `<label>` com `asp-for="Course.CourseID"` não elimina a necessidade do campo oculto.</span><span class="sxs-lookup"><span data-stu-id="2fe26-296">Adding a `<label>` tag helper with `asp-for="Course.CourseID"` doesn't eliminate the need for the hidden field.</span></span> <span data-ttu-id="2fe26-297">`<input type="hidden">` é necessário para que o número seja incluído nos dados postados quando o usuário clicar em **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="2fe26-297">`<input type="hidden">` is required for the course number to be included in the posted data when the user clicks **Save**.</span></span>

<span data-ttu-id="2fe26-298">Teste o código atualizado.</span><span class="sxs-lookup"><span data-stu-id="2fe26-298">Test the updated code.</span></span> <span data-ttu-id="2fe26-299">Crie, edite e exclua um curso.</span><span class="sxs-lookup"><span data-stu-id="2fe26-299">Create, edit, and delete a course.</span></span>

## <a name="add-asnotracking-to-the-details-and-delete-page-models"></a><span data-ttu-id="2fe26-300">Adicionar AsNoTracking aos modelos de página Detalhes e Excluir</span><span class="sxs-lookup"><span data-stu-id="2fe26-300">Add AsNoTracking to the Details and Delete page models</span></span>

<span data-ttu-id="2fe26-301">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) pode melhorar o desempenho quando o acompanhamento não é necessário.</span><span class="sxs-lookup"><span data-stu-id="2fe26-301">[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) can improve performance when tracking isn't required.</span></span> <span data-ttu-id="2fe26-302">Adicione `AsNoTracking` ao modelo de página Excluir e Detalhes.</span><span class="sxs-lookup"><span data-stu-id="2fe26-302">Add `AsNoTracking` to the Delete and Details page model.</span></span> <span data-ttu-id="2fe26-303">O seguinte código mostra o modelo de página Excluir atualizado:</span><span class="sxs-lookup"><span data-stu-id="2fe26-303">The following code shows the updated Delete page model:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Delete.cshtml.cs?name=snippet&highlight=21,23,40,41)]

<span data-ttu-id="2fe26-304">Atualize o método `OnGetAsync` no arquivo *Pages/Courses/Details.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="2fe26-304">Update the `OnGetAsync` method in the *Pages/Courses/Details.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Details.cshtml.cs?name=snippet)]

### <a name="modify-the-delete-and-details-pages"></a><span data-ttu-id="2fe26-305">Modificar as páginas Excluir e Detalhes</span><span class="sxs-lookup"><span data-stu-id="2fe26-305">Modify the Delete and Details pages</span></span>

<span data-ttu-id="2fe26-306">Atualize a página Excluir do Razor com a seguinte marcação:</span><span class="sxs-lookup"><span data-stu-id="2fe26-306">Update the Delete Razor page with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Delete.cshtml?highlight=15-20)]

<span data-ttu-id="2fe26-307">Faça as mesmas alterações na página Detalhes.</span><span class="sxs-lookup"><span data-stu-id="2fe26-307">Make the same changes to the Details page.</span></span>

### <a name="test-the-course-pages"></a><span data-ttu-id="2fe26-308">Testar as páginas Curso</span><span class="sxs-lookup"><span data-stu-id="2fe26-308">Test the Course pages</span></span>

<span data-ttu-id="2fe26-309">Teste as páginas Criar, Editar, Detalhes e Excluir.</span><span class="sxs-lookup"><span data-stu-id="2fe26-309">Test create, edit, details, and delete.</span></span>

## <a name="update-the-instructor-pages"></a><span data-ttu-id="2fe26-310">Atualizar as páginas do instrutor</span><span class="sxs-lookup"><span data-stu-id="2fe26-310">Update the instructor pages</span></span>

<span data-ttu-id="2fe26-311">As seções a seguir atualizam as páginas do instrutor.</span><span class="sxs-lookup"><span data-stu-id="2fe26-311">The following sections update the instructor pages.</span></span>

### <a name="add-office-location"></a><span data-ttu-id="2fe26-312">Adicionar local do escritório</span><span class="sxs-lookup"><span data-stu-id="2fe26-312">Add office location</span></span>

<span data-ttu-id="2fe26-313">Ao editar um registro de instrutor, é recomendável atualizar a atribuição de escritório do instrutor.</span><span class="sxs-lookup"><span data-stu-id="2fe26-313">When editing an instructor record, you may want to update the instructor's office assignment.</span></span> <span data-ttu-id="2fe26-314">A entidade `Instructor` tem uma relação um para zero ou um com a entidade `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="2fe26-314">The `Instructor` entity has a one-to-zero-or-one relationship with the `OfficeAssignment` entity.</span></span> <span data-ttu-id="2fe26-315">O código do instrutor precisa manipular:</span><span class="sxs-lookup"><span data-stu-id="2fe26-315">The instructor code must handle:</span></span>

* <span data-ttu-id="2fe26-316">Se o usuário limpar a atribuição de escritório, exclua a entidade `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="2fe26-316">If the user clears the office assignment, delete the `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="2fe26-317">Se o usuário inserir uma atribuição de escritório e ela estava vazia, crie uma nova entidade `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="2fe26-317">If the user enters an office assignment and it was empty, create a new `OfficeAssignment` entity.</span></span>
* <span data-ttu-id="2fe26-318">Se o usuário alterar a atribuição de escritório, atualize a entidade `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="2fe26-318">If the user changes the office assignment, update the `OfficeAssignment` entity.</span></span>

<span data-ttu-id="2fe26-319">Atualize o modelo de página Editar Instrutores com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="2fe26-319">Update the instructors Edit page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit1.cshtml.cs?name=snippet&highlight=20-23,32,39-999)]

<span data-ttu-id="2fe26-320">O código anterior:</span><span class="sxs-lookup"><span data-stu-id="2fe26-320">The preceding code:</span></span>

* <span data-ttu-id="2fe26-321">Obtém a entidade `Instructor` atual do banco de dados usando o carregamento adiantado para a propriedade de navegação `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="2fe26-321">Gets the current `Instructor` entity from the database using eager loading for the `OfficeAssignment` navigation property.</span></span>
* <span data-ttu-id="2fe26-322">Atualiza a entidade `Instructor` recuperada com valores do associador de modelos.</span><span class="sxs-lookup"><span data-stu-id="2fe26-322">Updates the retrieved `Instructor` entity with values from the model binder.</span></span> <span data-ttu-id="2fe26-323">`TryUpdateModel` impede o [excesso de postagem](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="2fe26-323">`TryUpdateModel` prevents [overposting](xref:data/ef-rp/crud#overposting).</span></span>
* <span data-ttu-id="2fe26-324">Se o local do escritório estiver em branco, `Instructor.OfficeAssignment` será definido como nulo.</span><span class="sxs-lookup"><span data-stu-id="2fe26-324">If the office location is blank, sets `Instructor.OfficeAssignment` to null.</span></span> <span data-ttu-id="2fe26-325">Quando `Instructor.OfficeAssignment` é nulo, a linha relacionada na tabela `OfficeAssignment` é excluída.</span><span class="sxs-lookup"><span data-stu-id="2fe26-325">When `Instructor.OfficeAssignment` is null, the related row in the `OfficeAssignment` table is deleted.</span></span>

### <a name="update-the-instructor-edit-page"></a><span data-ttu-id="2fe26-326">Atualizar a página Editar Instrutor</span><span class="sxs-lookup"><span data-stu-id="2fe26-326">Update the instructor Edit page</span></span>

<span data-ttu-id="2fe26-327">Atualize *Pages/Instructors/Edit.cshtml* com o local do escritório:</span><span class="sxs-lookup"><span data-stu-id="2fe26-327">Update *Pages/Instructors/Edit.cshtml* with the office location:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit1.cshtml?highlight=29-33)]

<span data-ttu-id="2fe26-328">Verifique se você pode alterar o local do escritório de um instrutor.</span><span class="sxs-lookup"><span data-stu-id="2fe26-328">Verify you can change an instructors office location.</span></span>

## <a name="add-course-assignments-to-the-instructor-edit-page"></a><span data-ttu-id="2fe26-329">Adicionar atribuições de Curso à página Editar Instrutor</span><span class="sxs-lookup"><span data-stu-id="2fe26-329">Add Course assignments to the instructor Edit page</span></span>

<span data-ttu-id="2fe26-330">Os instrutores podem ministrar a quantidade de cursos que desejarem.</span><span class="sxs-lookup"><span data-stu-id="2fe26-330">Instructors may teach any number of courses.</span></span> <span data-ttu-id="2fe26-331">Nesta seção, você adiciona a capacidade de alterar as atribuições de curso.</span><span class="sxs-lookup"><span data-stu-id="2fe26-331">In this section, you add the ability to change course assignments.</span></span> <span data-ttu-id="2fe26-332">A seguinte imagem mostra a página Editar Instrutor atualizada:</span><span class="sxs-lookup"><span data-stu-id="2fe26-332">The following image shows the updated instructor Edit page:</span></span>

![Página Editar Instrutor com cursos](update-related-data/_static/instructor-edit-courses.png)

<span data-ttu-id="2fe26-334">`Course` e `Instructor` têm uma relação muitos para muitos.</span><span class="sxs-lookup"><span data-stu-id="2fe26-334">`Course` and `Instructor` has a many-to-many relationship.</span></span> <span data-ttu-id="2fe26-335">Para adicionar e remover relações, adicione e remova entidades do conjunto de entidades de junção `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="2fe26-335">To add and remove relationships, you add and remove entities from the `CourseAssignments` join entity set.</span></span>

<span data-ttu-id="2fe26-336">As caixas de seleção permitem alterações em cursos aos quais um instrutor é atribuído.</span><span class="sxs-lookup"><span data-stu-id="2fe26-336">Check boxes enable changes to courses an instructor is assigned to.</span></span> <span data-ttu-id="2fe26-337">Uma caixa de seleção é exibida para cada curso no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="2fe26-337">A check box is displayed for every course in the database.</span></span> <span data-ttu-id="2fe26-338">Os cursos aos quais o instrutor é atribuído estão marcados.</span><span class="sxs-lookup"><span data-stu-id="2fe26-338">Courses that the instructor is assigned to are checked.</span></span> <span data-ttu-id="2fe26-339">O usuário pode marcar ou desmarcar as caixas de seleção para alterar as atribuições de curso.</span><span class="sxs-lookup"><span data-stu-id="2fe26-339">The user can select or clear check boxes to change course assignments.</span></span> <span data-ttu-id="2fe26-340">Se a quantidade de cursos for muito maior:</span><span class="sxs-lookup"><span data-stu-id="2fe26-340">If the number of courses were much greater:</span></span>

* <span data-ttu-id="2fe26-341">Provavelmente, você usará outra interface do usuário para exibir os cursos.</span><span class="sxs-lookup"><span data-stu-id="2fe26-341">You'd probably use a different user interface to display the courses.</span></span>
* <span data-ttu-id="2fe26-342">O método de manipulação de uma entidade de junção para criar ou excluir relações não será alterado.</span><span class="sxs-lookup"><span data-stu-id="2fe26-342">The method of manipulating a join entity to create or delete relationships wouldn't change.</span></span>

### <a name="add-classes-to-support-create-and-edit-instructor-pages"></a><span data-ttu-id="2fe26-343">Adicionar classes para dar suporte às páginas Criar e Editar Instrutor</span><span class="sxs-lookup"><span data-stu-id="2fe26-343">Add classes to support Create and Edit instructor pages</span></span>

<span data-ttu-id="2fe26-344">Crie *SchoolViewModels/AssignedCourseData.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="2fe26-344">Create *SchoolViewModels/AssignedCourseData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

<span data-ttu-id="2fe26-345">A classe `AssignedCourseData` contém dados para criar as caixas de seleção para os cursos atribuídos por um instrutor.</span><span class="sxs-lookup"><span data-stu-id="2fe26-345">The `AssignedCourseData` class contains data to create the check boxes for assigned courses by an instructor.</span></span>

<span data-ttu-id="2fe26-346">Crie a classe base *Pages/Instructors/InstructorCoursesPageModel.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="2fe26-346">Create the *Pages/Instructors/InstructorCoursesPageModel.cshtml.cs* base class:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/InstructorCoursesPageModel.cshtml.cs)]

<span data-ttu-id="2fe26-347">A `InstructorCoursesPageModel` é a classe base que será usada para os modelos de página Editar e Criar.</span><span class="sxs-lookup"><span data-stu-id="2fe26-347">The `InstructorCoursesPageModel` is the base class you will use for the Edit and Create page models.</span></span> <span data-ttu-id="2fe26-348">`PopulateAssignedCourseData` lê todas as entidades `Course` para popular `AssignedCourseDataList`.</span><span class="sxs-lookup"><span data-stu-id="2fe26-348">`PopulateAssignedCourseData` reads all `Course` entities to populate `AssignedCourseDataList`.</span></span> <span data-ttu-id="2fe26-349">Para cada curso, o código define a `CourseID`, o título e se o instrutor está ou não atribuído ao curso.</span><span class="sxs-lookup"><span data-stu-id="2fe26-349">For each course, the code sets the `CourseID`, title, and whether or not the instructor is assigned to the course.</span></span> <span data-ttu-id="2fe26-350">Um [HashSet](/dotnet/api/system.collections.generic.hashset-1) é usado para criar pesquisas eficientes.</span><span class="sxs-lookup"><span data-stu-id="2fe26-350">A [HashSet](/dotnet/api/system.collections.generic.hashset-1) is used to create efficient lookups.</span></span>

### <a name="instructors-edit-page-model"></a><span data-ttu-id="2fe26-351">Modelo de página Editar Instrutor</span><span class="sxs-lookup"><span data-stu-id="2fe26-351">Instructors Edit page model</span></span>

<span data-ttu-id="2fe26-352">Atualize o modelo de página Editar Instrutor com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="2fe26-352">Update the instructor Edit page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit.cshtml.cs?name=snippet&highlight=1,20-24,30,34,41-999)]

<span data-ttu-id="2fe26-353">O código anterior manipula as alterações de atribuição de escritório.</span><span class="sxs-lookup"><span data-stu-id="2fe26-353">The preceding code handles office assignment changes.</span></span>

<span data-ttu-id="2fe26-354">Atualize a Exibição do Razor do instrutor:</span><span class="sxs-lookup"><span data-stu-id="2fe26-354">Update the instructor Razor View:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit.cshtml?highlight=34-59)]

<a id="notepad"></a>
> [!NOTE]
> <span data-ttu-id="2fe26-355">Quando você cola o código no Visual Studio, as quebras de linha são alteradas de uma forma que divide o código.</span><span class="sxs-lookup"><span data-stu-id="2fe26-355">When you paste the code in Visual Studio, line breaks are changed in a way that breaks the code.</span></span> <span data-ttu-id="2fe26-356">Pressione Ctrl+Z uma vez para desfazer a formatação automática.</span><span class="sxs-lookup"><span data-stu-id="2fe26-356">Press Ctrl+Z one time to undo the automatic formatting.</span></span> <span data-ttu-id="2fe26-357">A tecla de atalho Ctrl+Z corrige as quebras de linha para que elas se pareçam com o que você vê aqui.</span><span class="sxs-lookup"><span data-stu-id="2fe26-357">Ctrl+Z fixes the line breaks so that they look like what you see here.</span></span> <span data-ttu-id="2fe26-358">O recuo não precisa ser perfeito, mas cada uma das linhas `@:</tr><tr>`, `@:<td>`, `@:</td>` e `@:</tr>` precisa estar em uma única linha, conforme mostrado.</span><span class="sxs-lookup"><span data-stu-id="2fe26-358">The indentation doesn't have to be perfect, but the `@:</tr><tr>`, `@:<td>`, `@:</td>`, and `@:</tr>` lines must each be on a single line as shown.</span></span> <span data-ttu-id="2fe26-359">Com o bloco de novo código selecionado, pressione Tab três vezes para alinhar o novo código com o código existente.</span><span class="sxs-lookup"><span data-stu-id="2fe26-359">With the block of new code selected, press Tab three times to line up the new code with the existing code.</span></span> <span data-ttu-id="2fe26-360">Vote ou examine o status deste bug [com este link](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).</span><span class="sxs-lookup"><span data-stu-id="2fe26-360">Vote on or review the status of this bug [with this link](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).</span></span>

<span data-ttu-id="2fe26-361">Esse código anterior cria uma tabela HTML que contém três colunas.</span><span class="sxs-lookup"><span data-stu-id="2fe26-361">The preceding code creates an HTML table that has three columns.</span></span> <span data-ttu-id="2fe26-362">Cada coluna tem uma caixa de seleção e uma legenda que contém o número e o título do curso.</span><span class="sxs-lookup"><span data-stu-id="2fe26-362">Each column has a check box and a caption containing the course number and title.</span></span> <span data-ttu-id="2fe26-363">Todas as caixas de seleção têm o mesmo nome ("selectedCourses").</span><span class="sxs-lookup"><span data-stu-id="2fe26-363">The check boxes all have the same name ("selectedCourses").</span></span> <span data-ttu-id="2fe26-364">O uso do mesmo nome instrui o associador de modelos a tratá-las como um grupo.</span><span class="sxs-lookup"><span data-stu-id="2fe26-364">Using the same name informs the model binder to treat them as a group.</span></span> <span data-ttu-id="2fe26-365">O atributo de valor de cada caixa de seleção é definido como `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="2fe26-365">The value attribute of each check box is set to `CourseID`.</span></span> <span data-ttu-id="2fe26-366">Quando a página é postada, o associador de modelos passa uma matriz que consiste nos valores `CourseID` para apenas as caixas de seleção marcadas.</span><span class="sxs-lookup"><span data-stu-id="2fe26-366">When the page is posted, the model binder passes an array that consists of the `CourseID` values for only the check boxes that are selected.</span></span>

<span data-ttu-id="2fe26-367">Quando as caixas de seleção são inicialmente renderizadas, os cursos atribuídos ao instrutor têm atributos marcados.</span><span class="sxs-lookup"><span data-stu-id="2fe26-367">When the check boxes are initially rendered, courses assigned to the instructor have checked attributes.</span></span>

<span data-ttu-id="2fe26-368">Execute o aplicativo e teste a página Editar Instrutor atualizada.</span><span class="sxs-lookup"><span data-stu-id="2fe26-368">Run the app and test the updated instructors Edit page.</span></span> <span data-ttu-id="2fe26-369">Altere algumas atribuições de curso.</span><span class="sxs-lookup"><span data-stu-id="2fe26-369">Change some course assignments.</span></span> <span data-ttu-id="2fe26-370">As alterações são refletidas na página Índice.</span><span class="sxs-lookup"><span data-stu-id="2fe26-370">The changes are reflected on the Index page.</span></span>

<span data-ttu-id="2fe26-371">Observação: A abordagem usada aqui para editar os dados de curso do instrutor funciona bem quando há uma quantidade limitada de cursos.</span><span class="sxs-lookup"><span data-stu-id="2fe26-371">Note: The approach taken here to edit instructor course data works well when there's a limited number of courses.</span></span> <span data-ttu-id="2fe26-372">Para coleções muito maiores, uma interface do usuário e um método de atualização diferentes são mais utilizáveis e eficientes.</span><span class="sxs-lookup"><span data-stu-id="2fe26-372">For collections that are much larger, a different UI and a different updating method would be more useable and efficient.</span></span>

### <a name="update-the-instructors-create-page"></a><span data-ttu-id="2fe26-373">Atualizar a página Criar Instrutor</span><span class="sxs-lookup"><span data-stu-id="2fe26-373">Update the instructors Create page</span></span>

<span data-ttu-id="2fe26-374">Atualize o modelo de página Criar instrutor com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="2fe26-374">Update the instructor Create page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Create.cshtml.cs)]

<span data-ttu-id="2fe26-375">O código anterior é semelhante ao código de *Pages/Instructors/Edit.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="2fe26-375">The preceding code is similar to the *Pages/Instructors/Edit.cshtml.cs* code.</span></span>

<span data-ttu-id="2fe26-376">Atualize a página Criar instrutor do Razor com a seguinte marcação:</span><span class="sxs-lookup"><span data-stu-id="2fe26-376">Update the instructor Create Razor page with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Create.cshtml?highlight=32-62)]

<span data-ttu-id="2fe26-377">Teste a página Criar Instrutor.</span><span class="sxs-lookup"><span data-stu-id="2fe26-377">Test the instructor Create page.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="2fe26-378">Atualizar a página Excluir</span><span class="sxs-lookup"><span data-stu-id="2fe26-378">Update the Delete page</span></span>

<span data-ttu-id="2fe26-379">Atualize o modelo da página Excluir com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="2fe26-379">Update the Delete page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Delete.cshtml.cs?highlight=5,40-999)]

<span data-ttu-id="2fe26-380">O código anterior faz as seguintes alterações:</span><span class="sxs-lookup"><span data-stu-id="2fe26-380">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="2fe26-381">Usa o carregamento adiantado para a propriedade de navegação `CourseAssignments`.</span><span class="sxs-lookup"><span data-stu-id="2fe26-381">Uses eager loading for the `CourseAssignments` navigation property.</span></span> <span data-ttu-id="2fe26-382">`CourseAssignments` deve ser incluído ou eles não são excluídos quando o instrutor é excluído.</span><span class="sxs-lookup"><span data-stu-id="2fe26-382">`CourseAssignments` must be included or they aren't deleted when the instructor is deleted.</span></span> <span data-ttu-id="2fe26-383">Para evitar a necessidade de lê-las, configure a exclusão em cascata no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="2fe26-383">To avoid needing to read them, configure cascade delete in the database.</span></span>

* <span data-ttu-id="2fe26-384">Se o instrutor a ser excluído é atribuído como administrador de qualquer departamento, remove a atribuição de instrutor desse departamento.</span><span class="sxs-lookup"><span data-stu-id="2fe26-384">If the instructor to be deleted is assigned as administrator of any departments, removes the instructor assignment from those departments.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2fe26-385">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="2fe26-385">Additional resources</span></span>

* [<span data-ttu-id="2fe26-386">Versão do YouTube deste tutorial (Parte 1)</span><span class="sxs-lookup"><span data-stu-id="2fe26-386">YouTube version of this tutorial (Part 1)</span></span>](https://www.youtube.com/watch?v=Csh6gkmwc9E)
* [<span data-ttu-id="2fe26-387">Versão do YouTube deste tutorial (Parte 2)</span><span class="sxs-lookup"><span data-stu-id="2fe26-387">YouTube version of this tutorial (Part 2)</span></span>](https://www.youtube.com/watch?v=mOAankB_Zgc)

> [!div class="step-by-step"]
> <span data-ttu-id="2fe26-388">[Anterior](xref:data/ef-rp/read-related-data)
> [Próximo](xref:data/ef-rp/concurrency)</span><span class="sxs-lookup"><span data-stu-id="2fe26-388">[Previous](xref:data/ef-rp/read-related-data)
[Next](xref:data/ef-rp/concurrency)</span></span>

::: moniker-end
