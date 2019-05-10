---
title: Páginas Razor com o EF Core no ASP.NET Core – Atualizar dados relacionados – 7 de 8
author: rick-anderson
description: Neste tutorial, você atualizará dados relacionados pela atualização dos campos de chave estrangeira e das propriedades de navegação.
ms.author: riande
ms.date: 11/15/2017
uid: data/ef-rp/update-related-data
ms.openlocfilehash: b996e7f9c8e422060f6378a6004a53906e2af7c7
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087093"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---update-related-data---7-of-8"></a>Páginas Razor com o EF Core no ASP.NET Core – Atualizar dados relacionados – 7 de 8

Por [Tom Dykstra](https://github.com/tdykstra) e [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

Este tutorial demonstra como atualizar dados relacionados. Caso tenha problemas que não consiga resolver, [baixe ou exiba o aplicativo concluído.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Instruções de download](xref:index#how-to-download-a-sample).

As ilustrações a seguir mostram algumas das páginas concluídas.

![Página Editar Curso](update-related-data/_static/course-edit.png)
![Página Editar Instrutor](update-related-data/_static/instructor-edit-courses.png)

Examine e teste as páginas de cursos Criar e Editar. Crie um novo curso. O departamento é selecionado por sua chave primária (um inteiro), não pelo nome. Edite o novo curso. Quando concluir o teste, exclua o novo curso.

## <a name="create-a-base-class-to-share-common-code"></a>Criar uma classe base para compartilhar um código comum

As páginas Cursos/Criar e Cursos/Editar precisam de uma lista de nomes de departamentos. Crie a classe base *Pages/Courses/DepartmentNamePageModel.cshtml.cs* para as páginas Criar e Editar:

[!code-csharp[](intro/samples/cu/Pages/Courses/DepartmentNamePageModel.cshtml.cs?highlight=9,11,20-21)]

O código anterior cria uma [SelectList](/dotnet/api/microsoft.aspnetcore.mvc.rendering.selectlist?view=aspnetcore-2.0) para conter a lista de nomes de departamentos. Se `selectedDepartment` for especificado, esse departamento estará selecionado na `SelectList`.

As classes de modelo da página Criar e Editar serão derivadas de `DepartmentNamePageModel`.

## <a name="customize-the-courses-pages"></a>Personalizar as páginas Courses

Quando uma nova entidade de curso é criada, ela precisa ter uma relação com um departamento existente. Para adicionar um departamento durante a criação de um curso, a classe base para Create e Edit contém uma lista suspensa para seleção do departamento. A lista suspensa define a propriedade `Course.DepartmentID` de FK (chave estrangeira). O EF Core usa a `Course.DepartmentID` FK para carregar a propriedade de navegação `Department`.

![Criar curso](update-related-data/_static/ddl.png)

Atualize o modelo da página Criar com o seguinte código:

[!code-csharp[](intro/samples/cu/Pages/Courses/Create.cshtml.cs?highlight=7,18,32-999)]

O código anterior:

* Deriva de `DepartmentNamePageModel`.
* Usa `TryUpdateModelAsync` para impedir o [excesso de postagem](xref:data/ef-rp/crud#overposting).
* Substitui `ViewData["DepartmentID"]` por `DepartmentNameSL` (da classe base).

`ViewData["DepartmentID"]` é substituído pelo `DepartmentNameSL` fortemente tipado. Modelos fortemente tipados são preferíveis aos fracamente tipados. Para obter mais informações, consulte [Dados fracamente tipados (ViewData e ViewBag)](xref:mvc/views/overview#VD_VB).

### <a name="update-the-courses-create-page"></a>Atualizar a página Criar Cursos

Atualize *Pages/Courses/Create.cshtml* com a seguinte marcação:

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?highlight=29-34)]

A marcação anterior faz as seguintes alterações:

* Altera a legenda de **DepartmentID** para **Departamento**.
* Substitui `"ViewBag.DepartmentID"` por `DepartmentNameSL` (da classe base).
* Adiciona a opção "Selecionar Departamento". Essa alteração renderiza "Selecionar Departamento", em vez do departamento primeiro.
* Adiciona uma mensagem de validação quando o departamento não está selecionado.

A Página do Razor usa a opção [Selecionar Auxiliar de Marcação](xref:mvc/views/working-with-forms#the-select-tag-helper):

[!code-cshtml[](intro/samples/cu/Pages/Courses/Create.cshtml?range=28-35&highlight=3-6)]

Teste a página Criar. A página Criar exibe o nome do departamento em vez de a ID do departamento.

### <a name="update-the-courses-edit-page"></a>Atualize a página Editar Cursos.

Atualize o modelo de página de edição com o seguinte código:

[!code-csharp[](intro/samples/cu/Pages/Courses/Edit.cshtml.cs?highlight=8,28,35,36,40,47-999)]

As alterações são semelhantes às feitas no modelo da página Criar. No código anterior, `PopulateDepartmentsDropDownList` passa a ID do departamento, que seleciona o departamento especificado na lista suspensa.

Atualize *Pages/Courses/Edit.cshtml* com a seguinte marcação:

[!code-cshtml[](intro/samples/cu/Pages/Courses/Edit.cshtml?highlight=17-20,32-35)]

A marcação anterior faz as seguintes alterações:

* Exibe a ID do curso. Geralmente, a PK (chave primária) de uma entidade não é exibida. Em geral, PKs não têm sentido para os usuários. Nesse caso, o PK é o número do curso.
* Altera a legenda de **DepartmentID** para **Departamento**.
* Substitui `"ViewBag.DepartmentID"` por `DepartmentNameSL` (da classe base).

A página contém um campo oculto (`<input type="hidden">`) para o número do curso. A adição de um auxiliar de marcação `<label>` com `asp-for="Course.CourseID"` não elimina a necessidade do campo oculto. `<input type="hidden">` é necessário para que o número seja incluído nos dados postados quando o usuário clicar em **Salvar**.

Teste o código atualizado. Crie, edite e exclua um curso.

## <a name="add-asnotracking-to-the-details-and-delete-page-models"></a>Adicionar AsNoTracking aos modelos de página Detalhes e Excluir

[AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking?view=efcore-2.0#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) pode melhorar o desempenho quando o acompanhamento não é necessário. Adicione `AsNoTracking` ao modelo de página Excluir e Detalhes. O seguinte código mostra o modelo de página Excluir atualizado:

[!code-csharp[](intro/samples/cu/Pages/Courses/Delete.cshtml.cs?name=snippet&highlight=21,23,40,41)]

Atualize o método `OnGetAsync` no arquivo *Pages/Courses/Details.cshtml.cs*:

[!code-csharp[](intro/samples/cu/Pages/Courses/Details.cshtml.cs?name=snippet)]

### <a name="modify-the-delete-and-details-pages"></a>Modificar as páginas Excluir e Detalhes

Atualize a página Excluir do Razor com a seguinte marcação:

[!code-cshtml[](intro/samples/cu/Pages/Courses/Delete.cshtml?highlight=15-20)]

Faça as mesmas alterações na página Detalhes.

### <a name="test-the-course-pages"></a>Testar as páginas Curso

Teste as páginas Criar, Editar, Detalhes e Excluir.

## <a name="update-the-instructor-pages"></a>Atualizar as páginas do instrutor

As seções a seguir atualizam as páginas do instrutor.

### <a name="add-office-location"></a>Adicionar local do escritório

Ao editar um registro de instrutor, é recomendável atualizar a atribuição de escritório do instrutor. A entidade `Instructor` tem uma relação um para zero ou um com a entidade `OfficeAssignment`. O código do instrutor precisa manipular:

* Se o usuário limpar a atribuição de escritório, exclua a entidade `OfficeAssignment`.
* Se o usuário inserir uma atribuição de escritório e ela estava vazia, crie uma nova entidade `OfficeAssignment`.
* Se o usuário alterar a atribuição de escritório, atualize a entidade `OfficeAssignment`.

Atualize o modelo de página Editar Instrutores com o seguinte código:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit1.cshtml.cs?name=snippet&highlight=20-23,32,39-999)]

O código anterior:

* Obtém a entidade `Instructor` atual do banco de dados usando o carregamento adiantado para a propriedade de navegação `OfficeAssignment`.
* Atualiza a entidade `Instructor` recuperada com valores do associador de modelos. `TryUpdateModel` impede o [excesso de postagem](xref:data/ef-rp/crud#overposting).
* Se o local do escritório estiver em branco, `Instructor.OfficeAssignment` será definido como nulo. Quando `Instructor.OfficeAssignment` é nulo, a linha relacionada na tabela `OfficeAssignment` é excluída.

### <a name="update-the-instructor-edit-page"></a>Atualizar a página Editar Instrutor

Atualize *Pages/Instructors/Edit.cshtml* com o local do escritório:

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit1.cshtml?highlight=29-33)]

Verifique se você pode alterar o local do escritório de um instrutor.

## <a name="add-course-assignments-to-the-instructor-edit-page"></a>Adicionar atribuições de Curso à página Editar Instrutor

Os instrutores podem ministrar a quantidade de cursos que desejarem. Nesta seção, você adiciona a capacidade de alterar as atribuições de curso. A seguinte imagem mostra a página Editar Instrutor atualizada:

![Página Editar Instrutor com cursos](update-related-data/_static/instructor-edit-courses.png)

`Course` e `Instructor` têm uma relação muitos para muitos. Para adicionar e remover relações, adicione e remova entidades do conjunto de entidades de junção `CourseAssignments`.

As caixas de seleção permitem alterações em cursos aos quais um instrutor é atribuído. Uma caixa de seleção é exibida para cada curso no banco de dados. Os cursos aos quais o instrutor é atribuído estão marcados. O usuário pode marcar ou desmarcar as caixas de seleção para alterar as atribuições de curso. Se a quantidade de cursos for muito maior:

* Provavelmente, você usará outra interface do usuário para exibir os cursos.
* O método de manipulação de uma entidade de junção para criar ou excluir relações não será alterado.

### <a name="add-classes-to-support-create-and-edit-instructor-pages"></a>Adicionar classes para dar suporte às páginas Criar e Editar Instrutor

Crie *SchoolViewModels/AssignedCourseData.cs* com o seguinte código:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

A classe `AssignedCourseData` contém dados para criar as caixas de seleção para os cursos atribuídos por um instrutor.

Crie a classe base *Pages/Instructors/InstructorCoursesPageModel.cshtml.cs*:

[!code-csharp[](intro/samples/cu/Pages/Instructors/InstructorCoursesPageModel.cshtml.cs)]

A `InstructorCoursesPageModel` é a classe base que será usada para os modelos de página Editar e Criar. `PopulateAssignedCourseData` lê todas as entidades `Course` para popular `AssignedCourseDataList`. Para cada curso, o código define a `CourseID`, o título e se o instrutor está ou não atribuído ao curso. Um [HashSet](/dotnet/api/system.collections.generic.hashset-1) é usado para criar pesquisas eficientes.

### <a name="instructors-edit-page-model"></a>Modelo de página Editar Instrutor

Atualize o modelo de página Editar Instrutor com o seguinte código:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Edit.cshtml.cs?name=snippet&highlight=1,20-24,30,34,41-999)]

O código anterior manipula as alterações de atribuição de escritório.

Atualize a Exibição do Razor do instrutor:

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Edit.cshtml?highlight=34-59)]

<a id="notepad"></a>
> [!NOTE]
> Quando você cola o código no Visual Studio, as quebras de linha são alteradas de uma forma que divide o código. Pressione Ctrl+Z uma vez para desfazer a formatação automática. A tecla de atalho Ctrl+Z corrige as quebras de linha para que elas se pareçam com o que você vê aqui. O recuo não precisa ser perfeito, mas cada uma das linhas `@</tr><tr>`, `@:<td>`, `@:</td>` e `@:</tr>` precisa estar em uma única linha, conforme mostrado. Com o bloco de novo código selecionado, pressione Tab três vezes para alinhar o novo código com o código existente. Vote ou examine o status deste bug [com este link](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).

Esse código anterior cria uma tabela HTML que contém três colunas. Cada coluna tem uma caixa de seleção e uma legenda que contém o número e o título do curso. Todas as caixas de seleção têm o mesmo nome ("selectedCourses"). O uso do mesmo nome instrui o associador de modelos a tratá-las como um grupo. O atributo de valor de cada caixa de seleção é definido como `CourseID`. Quando a página é postada, o associador de modelos passa uma matriz que consiste nos valores `CourseID` para apenas as caixas de seleção marcadas.

Quando as caixas de seleção são inicialmente renderizadas, os cursos atribuídos ao instrutor têm atributos marcados.

Execute o aplicativo e teste a página Editar Instrutor atualizada. Altere algumas atribuições de curso. As alterações são refletidas na página Índice.

Observação: A abordagem usada aqui para editar os dados de curso do instrutor funciona bem quando há uma quantidade limitada de cursos. Para coleções muito maiores, uma interface do usuário e um método de atualização diferentes são mais utilizáveis e eficientes.

### <a name="update-the-instructors-create-page"></a>Atualizar a página Criar Instrutor

Atualize o modelo de página Criar instrutor com o seguinte código:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Create.cshtml.cs)]

O código anterior é semelhante ao código de *Pages/Instructors/Edit.cshtml.cs*.

Atualize a página Criar instrutor do Razor com a seguinte marcação:

[!code-cshtml[](intro/samples/cu/Pages/Instructors/Create.cshtml?highlight=32-62)]

Teste a página Criar Instrutor.

## <a name="update-the-delete-page"></a>Atualizar a página Excluir

Atualize o modelo da página Excluir com o seguinte código:

[!code-csharp[](intro/samples/cu/Pages/Instructors/Delete.cshtml.cs?highlight=5,40-999)]

O código anterior faz as seguintes alterações:

* Usa o carregamento adiantado para a propriedade de navegação `CourseAssignments`. `CourseAssignments` deve ser incluído ou eles não são excluídos quando o instrutor é excluído. Para evitar a necessidade de lê-las, configure a exclusão em cascata no banco de dados.

* Se o instrutor a ser excluído é atribuído como administrador de qualquer departamento, remove a atribuição de instrutor desse departamento.

## <a name="additional-resources"></a>Recursos adicionais

* [Versão do YouTube deste tutorial (Parte 1)](https://www.youtube.com/watch?v=Csh6gkmwc9E)
* [Versão do YouTube deste tutorial (Parte 2)](https://www.youtube.com/watch?v=mOAankB_Zgc)

> [!div class="step-by-step"]
> [Anterior](xref:data/ef-rp/read-related-data)
> [Próximo](xref:data/ef-rp/concurrency)
