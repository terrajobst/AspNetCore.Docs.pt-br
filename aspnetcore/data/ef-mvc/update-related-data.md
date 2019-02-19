---
title: 'Tutorial: Atualizar dados relacionados - ASP.NET MVC com EF Core'
description: Neste tutorial, você atualizará dados relacionados pela atualização dos campos de chave estrangeira e das propriedades de navegação.
author: rick-anderson
ms.author: tdykstra
ms.custom: mvc
ms.date: 02/05/2019
ms.topic: tutorial
uid: data/ef-mvc/update-related-data
ms.openlocfilehash: ac94f2e2876c2d8d571a451e4641787ffe37b3d2
ms.sourcegitcommit: 5e3797a02ff3c48bb8cb9ad4320bfd169ebe8aba
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56103027"
---
# <a name="tutorial-update-related-data---aspnet-mvc-with-ef-core"></a>Tutorial: Atualizar dados relacionados - ASP.NET MVC com EF Core

No tutorial anterior, você exibiu dados relacionados; neste tutorial, você atualizará dados relacionados pela atualização dos campos de chave estrangeira e das propriedades de navegação.

As ilustrações a seguir mostram algumas das páginas com as quais você trabalhará.

![Página Editar Curso](update-related-data/_static/course-edit.png)

![Página Editar Instrutor](update-related-data/_static/instructor-edit-courses.png)

Neste tutorial, você:

> [!div class="checklist"]
> * Personalizar as páginas Cursos
> * Adicionar a página Editar Instrutores
> * Adicionar cursos à página Editar
> * Atualizar a página Excluir
> * Adicionar o local do escritório e cursos à página Criar

## <a name="prerequisites"></a>Pré-requisitos

* [Ler dados relacionados com o EF Core para um aplicativo Web ASP.NET Core MVC](read-related-data.md)

## <a name="customize-courses-pages"></a>Personalizar as páginas Cursos

Quando uma nova entidade de curso é criada, ela precisa ter uma relação com um departamento existente. Para facilitar isso, o código gerado por scaffolding inclui métodos do controlador e exibições Criar e Editar que incluem uma lista suspensa para seleção do departamento. A lista suspensa define a propriedade de chave estrangeira `Course.DepartmentID`, e isso é tudo o que o Entity Framework precisa para carregar a propriedade de navegação `Department` com a entidade Department apropriada. Você usará o código gerado por scaffolding, mas o alterará ligeiramente para adicionar tratamento de erro e classificação à lista suspensa.

Em *CoursesController.cs*, exclua os quatro métodos Create e Edit e substitua-os pelo seguinte código:

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreateGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_CreatePost)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_EditPost)]

Após o método HttpPost `Edit`, crie um novo método que carrega informações de departamento para a lista suspensa.

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_Departments)]

O método `PopulateDepartmentsDropDownList` obtém uma lista de todos os departamentos classificados por nome, cria uma coleção `SelectList` para uma lista suspensa e passa a coleção para a exibição em `ViewBag`. O método aceita o parâmetro `selectedDepartment` opcional que permite que o código de chamada especifique o item que será selecionado quando a lista suspensa for renderizada. A exibição passará o nome "DepartmentID" para o auxiliar de marcação `<select>` e o auxiliar então saberá que deve examinar o objeto `ViewBag` em busca de uma `SelectList` chamada "DepartmentID".

O método HttpGet `Create` chama o método `PopulateDepartmentsDropDownList` sem definir o item selecionado, porque um novo curso do departamento ainda não foi estabelecido:

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=3&name=snippet_CreateGet)]

O método HttpGet `Edit` define o item selecionado, com base na ID do departamento já atribuído ao curso que está sendo editado:

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=15&name=snippet_EditGet)]

Os métodos HttpPost para `Create` e `Edit` também incluem o código que define o item selecionado quando eles exibem novamente a página após um erro. Isso garante que quando a página for exibida novamente para mostrar a mensagem de erro, qualquer que tenha sido o departamento selecionado permaneça selecionado.

### <a name="add-asnotracking-to-details-and-delete-methods"></a>Adicionar .AsNoTracking aos métodos Details e Delete

Para otimizar o desempenho das páginas Detalhes do Curso e Excluir, adicione chamadas `AsNoTracking` aos métodos HttpGet `Details` e `Delete`.

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_Details)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?highlight=10&name=snippet_DeleteGet)]

### <a name="modify-the-course-views"></a>Modificar as exibições Curso

Em *Views/Courses/Create.cshtml*, adicione uma opção "Selecionar Departamento" à lista suspensa **Departamento**, altere a legenda de **DepartmentID** para **Departamento** e adicione uma mensagem de validação.

[!code-html[](intro/samples/cu/Views/Courses/Create.cshtml?highlight=2-6&range=29-34)]

Em *Views/Courses/Edit.cshtml*, faça a mesma alteração no campo Departamento que você acabou de fazer em *Create.cshtml*.

Também em *Views/Courses/Edit.cshtml*, adicione um campo de número de curso antes do campo **Título**. Como o número de curso é a chave primária, ele é exibido, mas não pode ser alterado.

[!code-html[](intro/samples/cu/Views/Courses/Edit.cshtml?range=15-18)]

Já existe um campo oculto (`<input type="hidden">`) para o número de curso na exibição Editar. A adição de um auxiliar de marcação `<label>` não elimina a necessidade do campo oculto, porque ele não faz com que o número de curso seja incluído nos dados postados quando o usuário clica em **Salvar** na página **Editar**.

Em *Views/Courses/Delete.cshtml*, adicione um campo de número de curso na parte superior e altere a ID do departamento para o nome do departamento.

[!code-html[](intro/samples/cu/Views/Courses/Delete.cshtml?highlight=14-19,36)]

Em *Views/Courses/Details.cshtml*, faça a mesma alteração que você acabou de fazer para *Delete.cshtml*.

### <a name="test-the-course-pages"></a>Testar as páginas Curso

Execute o aplicativo, selecione a guia **Cursos**, clique em **Criar Novo** e insira dados para um novo curso:

![Página Criar Curso](update-related-data/_static/course-create.png)

Clique em **Criar**. A página Índice de Cursos é exibida com o novo curso adicionado à lista. O nome do departamento na lista de páginas de Índice é obtido da propriedade de navegação, mostrando que a relação foi estabelecida corretamente.

Clique em **Editar** em um curso na página Índice de Cursos.

![Página Editar Curso](update-related-data/_static/course-edit.png)

Altere dados na página e clique em **Salvar**. A página Índice de Cursos é exibida com os dados de cursos atualizados.

## <a name="add-instructors-edit-page"></a>Adicionar a página Editar Instrutores

Quando você edita um registro de instrutor, deseja poder atualizar a atribuição de escritório do instrutor. A entidade Instructor tem uma relação um para zero ou um com a entidade OfficeAssignment, o que significa que o código deve manipular as seguintes situações:

* Se o usuário apagar a atribuição de escritório e ela originalmente tinha um valor, exclua a entidade OfficeAssignment.

* Se o usuário inserir um valor de atribuição de escritório e ele originalmente estava vazio, crie uma nova entidade OfficeAssignment.

* Se o usuário alterar o valor de uma atribuição de escritório, altere o valor em uma entidade OfficeAssignment existente.

### <a name="update-the-instructors-controller"></a>Atualizar o controlador Instrutores

Em *InstructorsController.cs*, altere o código no método HttpGet `Edit` para que ele carregue a propriedade de navegação `OfficeAssignment` da entidade Instructor e chame `AsNoTracking`:

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=9,10&name=snippet_EditGetOA)]

Substitua o método HttpPost `Edit` pelo seguinte código para manipular atualizações de atribuição de escritório:

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EditPostOA)]

O código faz o seguinte:

-  Altera o nome do método para `EditPost` porque a assinatura agora é a mesma do método HttpGet `Edit` (o atributo `ActionName` especifica que a URL `/Edit/` ainda é usada).

-  Obtém a entidade Instructor atual do banco de dados usando o carregamento adiantado para a propriedade de navegação `OfficeAssignment`. Isso é o mesmo que você fez no método HttpGet `Edit`.

-  Atualiza a entidade Instructor recuperada com valores do associador de modelos. A sobrecarga `TryUpdateModel` permite que você adicione à lista de permissões as propriedades que você deseja incluir. Isso impede o excesso de postagem, conforme explicado no [segundo tutorial](crud.md).

    <!-- Snippets don't play well with <ul> [!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=241-244)] -->

    ```csharp
    if (await TryUpdateModelAsync<Instructor>(
        instructorToUpdate,
        "",
        i => i.FirstMidName, i => i.LastName, i => i.HireDate, i => i.OfficeAssignment))
    ```

-   Se o local do escritório estiver em branco, a propriedade Instructor.OfficeAssignment será definida como nula para que a linha relacionada na tabela OfficeAssignment seja excluída.

    <!-- Snippets don't play well with <ul>  "intro/samples/cu/Controllers/InstructorsController.cs"} -->

    ```csharp
    if (String.IsNullOrWhiteSpace(instructorToUpdate.OfficeAssignment?.Location))
    {
        instructorToUpdate.OfficeAssignment = null;
    }
    ```

- Salva as alterações no banco de dados.

### <a name="update-the-instructor-edit-view"></a>Atualizar a exibição Editar Instrutor

Em *Views/Instructors/Edit.cshtml*, adicione um novo campo para editar o local do escritório, ao final, antes do botão **Salvar**:

[!code-html[](intro/samples/cu/Views/Instructors/Edit.cshtml?range=30-34)]

Execute o aplicativo, selecione a guia **Instrutores** e, em seguida, clique em **Editar** em um instrutor. Altere o **Local do Escritório** e clique em **Salvar**.

![Página Editar Instrutor](update-related-data/_static/instructor-edit-office.png)

## <a name="add-courses-to-edit-page"></a>Adicionar cursos à página Editar

Os instrutores podem ministrar a quantidade de cursos que desejarem. Agora, você aprimorará a página Editar Instrutor adicionando a capacidade de alterar as atribuições de curso usando um grupo de caixas de seleção, conforme mostrado na seguinte captura de tela:

![Página Editar Instrutor com cursos](update-related-data/_static/instructor-edit-courses.png)

A relação entre as entidades Course e Instructor é muitos para muitos. Para adicionar e remover relações, adicione e remova entidades bidirecionalmente no conjunto de entidades de junção CourseAssignments.

A interface do usuário que permite alterar a quais cursos um instrutor é atribuído é um grupo de caixas de seleção. Uma caixa de seleção é exibida para cada curso no banco de dados, e aqueles aos quais o instrutor está atribuído no momento são marcados. O usuário pode marcar ou desmarcar as caixas de seleção para alterar as atribuições de curso. Se a quantidade de cursos for muito maior, provavelmente, você desejará usar outro método de apresentação dos dados na exibição, mas usará o mesmo método de manipulação de uma entidade de junção para criar ou excluir relações.

### <a name="update-the-instructors-controller"></a>Atualizar o controlador Instrutores

Para fornecer dados à exibição para a lista de caixas de seleção, você usará uma classe de modelo de exibição.

Crie *AssignedCourseData.cs* na pasta *SchoolViewModels* e substitua o código existente pelo seguinte código:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/AssignedCourseData.cs)]

Em *InstructorsController.cs*, substitua o método HttpGet `Edit` pelo código a seguir. As alterações são realçadas.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=10,17,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36&name=snippet_EditGetCourses)]

O código adiciona o carregamento adiantado à propriedade de navegação `Courses` e chama o novo método `PopulateAssignedCourseData` para fornecer informações para a matriz de caixa de seleção usando a classe de modelo de exibição `AssignedCourseData`.

O código no método `PopulateAssignedCourseData` lê todas as entidades Course para carregar uma lista de cursos usando a classe de modelo de exibição. Para cada curso, o código verifica se o curso existe na propriedade de navegação `Courses` do instrutor. Para criar uma pesquisa eficiente ao verificar se um curso é atribuído ao instrutor, os cursos atribuídos ao instrutor são colocados em uma coleção `HashSet`. A propriedade `Assigned` está definida como verdadeiro para os cursos aos quais instrutor é atribuído. A exibição usará essa propriedade para determinar quais caixas de seleção precisam ser exibidas como selecionadas. Por fim, a lista é passada para a exibição em `ViewData`.

Em seguida, adicione o código que é executado quando o usuário clica em **Salvar**. Substitua o método `EditPost` pelo código a seguir e adicione um novo método que atualiza a propriedade de navegação `Courses` da entidade Instructor.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=1,3,12,13,25,39-40&name=snippet_EditPostCourses)]

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=1-31)]

A assinatura do método agora é diferente do método HttpGet `Edit` e, portanto, o nome do método é alterado de `EditPost` para `Edit` novamente.

Como a exibição não tem uma coleção de entidades Course, o associador de modelos não pode atualizar automaticamente a propriedade de navegação `CourseAssignments`. Em vez de usar o associador de modelos para atualizar a propriedade de navegação `CourseAssignments`, faça isso no novo método `UpdateInstructorCourses`. Portanto, você precisa excluir a propriedade `CourseAssignments` do model binding. Isso não exige nenhuma alteração no código que chama `TryUpdateModel`, porque você está usando a sobrecarga da lista de permissões e `CourseAssignments` não está na lista de inclusões.

Se nenhuma caixa de seleção foi marcada, o código em `UpdateInstructorCourses` inicializa a propriedade de navegação `CourseAssignments` com uma coleção vazia e retorna:

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_UpdateCourses&highlight=3-7)]

Em seguida, o código executa um loop em todos os cursos no banco de dados e verifica cada curso em relação àqueles atribuídos no momento ao instrutor e em relação àqueles que foram selecionados na exibição. Para facilitar pesquisas eficientes, as últimas duas coleções são armazenadas em objetos `HashSet`.

Se a caixa de seleção para um curso foi marcada, mas o curso não está na propriedade de navegação `Instructor.CourseAssignments`, o curso é adicionado à coleção na propriedade de navegação.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=14-20&name=snippet_UpdateCourses)]

Se a caixa de seleção para um curso não foi marcada, mas o curso está na propriedade de navegação `Instructor.CourseAssignments`, o curso é removido da propriedade de navegação.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=21-29&name=snippet_UpdateCourses)]

### <a name="update-the-instructor-views"></a>Atualizar as exibições Instrutor

Em *Views/Instructors/Edit.cshtml*, adicione um campo **Cursos** com uma matriz de caixas de seleção, adicionando o código a seguir imediatamente após os elementos `div` para o campo **Escritório** e antes do elemento `div` para o botão **Salvar**.

<a id="notepad"></a>
> [!NOTE]
> Quando você colar o código no Visual Studio, as quebras de linha serão alteradas de uma forma que divide o código.  Pressione Ctrl+Z uma vez para desfazer a formatação automática.  Isso corrigirá as quebras de linha para que elas se pareçam com o que você vê aqui. O recuo não precisa ser perfeito, mas cada uma das linhas `@</tr><tr>`, `@:<td>`, `@:</td>` e `@:</tr>` precisa estar em uma única linha, conforme mostrado, ou você receberá um erro de tempo de execução. Com o bloco de novo código selecionado, pressione Tab três vezes para alinhar o novo código com o código existente. Verifique o status deste problema [aqui](https://developercommunity.visualstudio.com/content/problem/147795/razor-editor-malforms-pasted-markup-and-creates-in.html).

[!code-html[](intro/samples/cu/Views/Instructors/Edit.cshtml?range=35-61)]

Esse código cria uma tabela HTML que contém três colunas. Em cada coluna há uma caixa de seleção, seguida de uma legenda que consiste no número e título do curso. Todas as caixas de seleção têm o mesmo nome ("selectedCourses"), o que informa ao associador de modelos de que elas devem ser tratadas como um grupo. O atributo de valor de cada caixa de seleção é definido com o valor de `CourseID`. Quando a página é postada, o associador de modelos passa uma matriz para o controlador que consiste nos valores `CourseID` para apenas as caixas de seleção marcadas.

Quando as caixas de seleção são inicialmente renderizadas, aquelas que se destinam aos cursos atribuídos ao instrutor têm atributos marcados, que os seleciona (exibe-os como marcados).

Execute o aplicativo, selecione a guia **Instrutores** e clique em **Editar** em um instrutor para ver a página **Editar**.

![Página Editar Instrutor com cursos](update-related-data/_static/instructor-edit-courses.png)

Altere algumas atribuições de curso e clique em Salvar. As alterações feitas são refletidas na página Índice.

> [!NOTE]
> A abordagem usada aqui para editar os dados de curso do instrutor funciona bem quando há uma quantidade limitada de cursos. Para coleções muito maiores, uma interface do usuário e um método de atualização diferentes são necessários.

## <a name="update-delete-page"></a>Atualizar a página Excluir

Em *InstructorsController.cs*, exclua o método `DeleteConfirmed` e insira o código a seguir em seu lugar.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?highlight=5-7,9-12&name=snippet_DeleteConfirmed)]

Este código faz as seguintes alterações:

* Executa o carregamento adiantado para a propriedade de navegação `CourseAssignments`.  Você precisa incluir isso ou o EF não reconhecerá as entidades `CourseAssignment` relacionadas e não as excluirá.  Para evitar a necessidade de lê-las aqui, você pode configurar a exclusão em cascata no banco de dados.

* Se o instrutor a ser excluído é atribuído como administrador de qualquer departamento, remove a atribuição de instrutor desse departamento.

## <a name="add-office-location-and-courses-to-create-page"></a>Adicionar o local do escritório e cursos à página Criar

Em *InstructorsController.cs*, exclua os métodos HttpGet e HttpPost `Create` e, em seguida, adicione o seguinte código em seu lugar:

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Create&highlight=3-5,12,14-22,29)]

Esse código é semelhante ao que você viu nos métodos `Edit`, exceto que inicialmente nenhum curso está selecionado. O método HttpGet `Create` chama o método `PopulateAssignedCourseData`, não porque pode haver cursos selecionados, mas para fornecer uma coleção vazia para o loop `foreach` na exibição (caso contrário, o código de exibição gera uma exceção de referência nula).

O método HttpPost `Create` adiciona cada curso selecionado à propriedade de navegação `CourseAssignments` antes que ele verifica se há erros de validação e adiciona o novo instrutor ao banco de dados. Os cursos são adicionados, mesmo se há erros de modelo, de modo que quando houver erros de modelo (por exemplo, o usuário inseriu uma data inválida) e a página for exibida novamente com uma mensagem de erro, as seleções de cursos que foram feitas sejam restauradas automaticamente.

Observe que para poder adicionar cursos à propriedade de navegação `CourseAssignments`, é necessário inicializar a propriedade como uma coleção vazia:

```csharp
instructor.CourseAssignments = new List<CourseAssignment>();
```

Como alternativa a fazer isso no código do controlador, faça isso no modelo Instrutor alterando o getter de propriedade para criar automaticamente a coleção se ela não existir, conforme mostrado no seguinte exemplo:

```csharp
private ICollection<CourseAssignment> _courseAssignments;
public ICollection<CourseAssignment> CourseAssignments
{
    get
    {
        return _courseAssignments ?? (_courseAssignments = new List<CourseAssignment>());
    }
    set
    {
        _courseAssignments = value;
    }
}
```

Se você modificar a propriedade `CourseAssignments` dessa forma, poderá remover o código de inicialização de propriedade explícita no controlador.

Em *Views/Instructor/Create.cshtml*, adicione uma caixa de texto de local do escritório e caixas de seleção para cursos antes do botão Enviar. Como no caso da página Editar, [corrija a formatação se o Visual Studio reformatar o código quando você o colar](#notepad).

[!code-html[](intro/samples/cu/Views/Instructors/Create.cshtml?range=29-61)]

Faça o teste executando o aplicativo e criando um instrutor.

## <a name="handling-transactions"></a>Manipulando transações

Conforme explicado no [tutorial do CRUD](crud.md), o Entity Framework implementa transações de forma implícita. Para cenários em que você precisa de mais controle – por exemplo, se desejar incluir operações feitas fora do Entity Framework em uma transação –, consulte [Transações](/ef/core/saving/transactions).

## <a name="get-the-code"></a>Obter o código

[Baixe ou exiba o aplicativo concluído.](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você:

> [!div class="checklist"]
> * Personalizou as páginas Cursos
> * Adicionou a página Editar Instrutores
> * Adicionou cursos à página Editar
> * Atualizou a página Excluir
> * Adicionou o local do escritório e cursos à página Criar

Vá para o próximo artigo para saber como lidar com conflitos de simultaneidade.
> [!div class="nextstepaction"]
> [Tratar conflitos de simultaneidade](concurrency.md)
