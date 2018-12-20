---
uid: mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application
title: Atualizando dados relacionados com o Entity Framework em um aplicativo ASP.NET MVC (6 de 10) | Microsoft Docs
author: tdykstra
description: Aplicativo web de exemplo Contoso University demonstra como criar aplicativos ASP.NET MVC 4 usando o Code First do Entity Framework 5 e o Visual Studio...
ms.author: riande
ms.date: 07/30/2013
ms.assetid: 7871dc05-2750-470f-8b4c-3a52511949bc
msc.legacyurl: /mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: ceb311f16575f7aedf0fd789a28e7313daeeaceb
ms.sourcegitcommit: 45ac74e400f9f2b7dbded66297730f6f14a4eb25
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2018
ms.locfileid: "41834984"
---
<a name="updating-related-data-with-the-entity-framework-in-an-aspnet-mvc-application-6-of-10"></a>Atualizando dados relacionados com o Entity Framework em um aplicativo ASP.NET MVC (6 de 10)
====================
por [Tom Dykstra](https://github.com/tdykstra)

[Baixe o projeto concluído](http://code.msdn.microsoft.com/Getting-Started-with-dd0e2ed8)

> Aplicativo web de exemplo Contoso University demonstra como criar aplicativos ASP.NET MVC 4 usando o Code First do Entity Framework 5 e o Visual Studio 2012. Para obter informações sobre a série de tutoriais, consulte [primeiro tutorial na série](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md). Você pode iniciar a série de tutoriais de início ou [baixar um projeto inicial para este capítulo](building-the-ef5-mvc4-chapter-downloads.md) e comece por aqui.
> 
> > [!NOTE] 
> > 
> > Se você enfrentar um problema que você não conseguir resolver, [baixar o capítulo concluído](building-the-ef5-mvc4-chapter-downloads.md) e tente reproduzir o problema. Em geral, você pode encontrar a solução ao problema comparando seu código com o código completo. Para alguns erros comuns e como resolvê-los, consulte [erros e soluções alternativas.](advanced-entity-framework-scenarios-for-an-mvc-web-application.md#errors)


No tutorial anterior, você exibiu dados relacionados; Neste tutorial, você atualizará dados relacionados. Para a maioria das relações, isso pode ser feito atualizando os campos de chave estrangeiros apropriados. Para relações muitos-para-muitos, o Entity Framework não expõe a tabela de junção diretamente, portanto, explicitamente, você deve adicionar e remover entidades de e para as propriedades de navegação apropriado.

As ilustrações a seguir mostram as páginas com as quais você trabalhará.

![Course_create_page](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image1.png)

![Instructor_edit_page_with_courses](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image2.png)

## <a name="customize-the-create-and-edit-pages-for-courses"></a>Personalizar as páginas Criar e Editar dos cursos

Quando uma nova entidade de curso é criada, ela precisa ter uma relação com um departamento existente. Para facilitar isso, o código gerado por scaffolding inclui métodos do controlador e exibições Criar e Editar que incluem uma lista suspensa para seleção do departamento. A lista suspensa define a `Course.DepartmentID` propriedade de chave estrangeira, e isso é tudo o que o Entity Framework precisa para carregar o `Department` propriedade de navegação com os devidos `Department` entidade. Você usará o código gerado por scaffolding, mas o alterará ligeiramente para adicionar tratamento de erro e classificação à lista suspensa.

Na *CourseController.cs*, exclua os quatro `Edit` e `Create` métodos e substituí-los com o código a seguir:

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample1.cs?highlight=3,10,13-14,21-27,34,41,44-45,52-58,62-68)]

O `PopulateDepartmentsDropDownList` método obtém uma lista de todos os departamentos classificados por nome, cria um `SelectList` coleção para uma lista suspensa e passa a coleção para a exibição em um `ViewBag` propriedade. O método aceita o parâmetro `selectedDepartment` opcional que permite que o código de chamada especifique o item que será selecionado quando a lista suspensa for renderizada. A exibição passará o nome `DepartmentID` para [as `DropDownList` auxiliar](../working-with-the-dropdownlist-box-and-jquery/using-the-dropdownlist-helper-with-aspnet-mvc.md), e, em seguida, saberá que o auxiliar deve para examinar o `ViewBag` do objeto para um `SelectList` chamado `DepartmentID`.

O `HttpGet` `Create` chamadas de método a `PopulateDepartmentsDropDownList` método sem definir o item selecionado, porque um novo curso do departamento ainda não foi estabelecido:

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample2.cs)]

O `HttpGet` `Edit` método define o item selecionado, com base na ID do departamento que já está atribuído ao curso que está sendo editado:

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample3.cs)]

O `HttpPost` métodos para ambos `Create` e `Edit` também incluem o código que define o item selecionado quando eles exibem novamente a página após um erro:

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample4.cs)]

Esse código garante que quando a página será reexibida para mostrar a mensagem de erro, qualquer departamento selecionado permaneça selecionado.

Na *Views\Course\Create.cshtml*, adicione o código realçado para criar um novo campo de número de curso antes do **título** campo. Conforme explicado em um tutorial anterior, os campos de chave primária não são gerado automaticamente por padrão, mas essa chave primária é significativa, para que você deseja que o usuário seja capaz de inserir o valor da chave.

[!code-cshtml[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample5.cshtml?highlight=17-23)]

Na *Views\Course\Edit.cshtml*, *Views\Course\Delete.cshtml*, e *Views\Course\Details.cshtml*, adicione um campo de número de curso antes do **título**  campo. Como é a chave primária, ele é exibido, mas ele não pode ser alterado.

[!code-cshtml[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample6.cshtml)]

Execute o **Create** página (exibir a página de índice do curso e clique em **criar novo**) e insira os dados para um novo curso:

![Course_create_page](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image3.png)

Clique em **Criar**. A página de índice de cursos é exibida com o novo curso adicionado à lista. O nome do departamento na lista de páginas de Índice é obtido da propriedade de navegação, mostrando que a relação foi estabelecida corretamente.

![Course_Index_page_showing_new_course](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image4.png)

Execute o **edite** página (exibir a página de índice do curso e clique em **editar** em um curso).

![Course_edit_page](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image5.png)

Altere dados na página e clique em **Salvar**. A página índice de cursos é exibida com os dados de curso atualizado.

## <a name="adding-an-edit-page-for-instructors"></a>Adicionando uma página Editar para instrutores

Quando você edita um registro de instrutor, deseja poder atualizar a atribuição de escritório do instrutor. O `Instructor` entidade tem uma relação um-para-zero-ou-um com o `OfficeAssignment` entidade, o que significa que você deve lidar com as seguintes situações:

- Se o usuário limpar a atribuição de escritório e ela originalmente tinha um valor, remova e excluir o `OfficeAssignment` entidade.
- Se o usuário insere um valor de atribuição de escritório e ele originalmente estava vazio, você deve criar um novo `OfficeAssignment` entidade.
- Se o usuário altera o valor de uma atribuição de escritório, você deve alterar o valor em existente `OfficeAssignment` entidade.

Abra *InstructorController.cs* e examine o `HttpGet` `Edit` método:

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample7.cs)]

O código gerado por scaffolding aqui não é o que você deseja. Configurar dados para uma lista suspensa, mas você precisa é de uma caixa de texto. Substitua esse método com o código a seguir:

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample8.cs)]

Este código remove a `ViewBag` instrução e adiciona o carregamento adiantado para associado `OfficeAssignment` entidade. Não é possível executar o carregamento adiantado com o `Find` método, portanto, o `Where` e `Single` métodos são usados em vez disso, para selecionar o instrutor.

Substitua os `HttpPost` `Edit` método com o código a seguir. que trata de atualizações de atribuição do office:

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample9.cs)]

O código faz o seguinte:

- Obtém a entidade `Instructor` atual do banco de dados usando o carregamento adiantado para a propriedade de navegação `OfficeAssignment`. Isso é o mesmo que você fez `HttpGet` `Edit` método.
- Atualiza a entidade `Instructor` recuperada com valores do associador de modelos. O [TryUpdateModel](https://msdn.microsoft.com/library/dd470908(v=vs.108).aspx) sobrecarga usada permite que você coloque na *lista de permissões* as propriedades que você deseja incluir. Isso impede o excesso de postagem, conforme explicado em [segundo tutorial](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application.md).

    [!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample10.cs)]
- Define se o local do escritório estiver em branco, o `Instructor.OfficeAssignment` propriedade como nulo, de modo que a linha relacionada no `OfficeAssignment` tabela será excluída.

    [!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample11.cs)]
- Salva as alterações no banco de dados.

Na *Views\Instructor\Edit.cshtml*após o `div` elementos para o **data de contratação** campo, adicione um novo campo para editar o local do escritório:

[!code-cshtml[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample12.cshtml)]

Execute a página (selecione o **instrutores** guia e, em seguida, clique em **editar** em um instrutor). Altere o **Local do Escritório** e clique em **Salvar**.

![Changing_the_office_location](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image6.png)

## <a name="adding-course-assignments-to-the-instructor-edit-page"></a>Adicionar atribuições de curso ao instrutor página Editar

Os instrutores podem ministrar a quantidade de cursos que desejarem. Agora, você aprimorará a página Editar Instrutor adicionando a capacidade de alterar as atribuições de curso usando um grupo de caixas de seleção, conforme mostrado na seguinte captura de tela:

![Instructor_edit_page_with_courses](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image7.png)

A relação entre o `Course` e `Instructor` entidades é muitos-para-muitos, o que significa que você não tem acesso direto à tabela de junção. Em vez disso, você irá adicionar e remover entidades de e para o `Instructor.Courses` propriedade de navegação.

A interface do usuário que permite alterar a quais cursos um instrutor é atribuído é um grupo de caixas de seleção. Uma caixa de seleção é exibida para cada curso no banco de dados, e aqueles aos quais o instrutor está atribuído no momento são marcados. O usuário pode marcar ou desmarcar as caixas de seleção para alterar as atribuições de curso. Se o número de cursos for muito maior, você provavelmente desejará usar um método diferente de apresentação dos dados no modo de exibição, mas você usaria o mesmo método de manipulação de propriedades de navegação para criar ou excluir relações.

Para fornecer dados à exibição para a lista de caixas de seleção, você usará uma classe de modelo de exibição. Crie *AssignedCourseData.cs* na *ViewModels* pasta e substitua o código existente pelo código a seguir:

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample13.cs)]

Na *InstructorController.cs*, substitua o `HttpGet` `Edit` método com o código a seguir. As alterações são realçadas.

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample14.cs?highlight=5,8,12-27)]

O código adiciona o carregamento adiantado à propriedade de navegação `Courses` e chama o novo método `PopulateAssignedCourseData` para fornecer informações para a matriz de caixa de seleção usando a classe de modelo de exibição `AssignedCourseData`.

O código a `PopulateAssignedCourseData` método lê todos `Course` entidades para carregar uma lista de cursos usando a exibição de classe de modelo. Para cada curso, o código verifica se o curso existe na propriedade de navegação `Courses` do instrutor. Para criar uma pesquisa eficiente ao verificar se um curso é atribuído ao instrutor, os cursos atribuídos ao instrutor são colocados em um [HashSet](https://msdn.microsoft.com/library/bb359438.aspx) coleção. O `Assigned` estiver definida como `true` para cursos o instrutor é atribuído. A exibição usará essa propriedade para determinar quais caixas de seleção precisam ser exibidas como selecionadas. Por fim, a lista é passada para a exibição em um `ViewBag` propriedade.

Em seguida, adicione o código que é executado quando o usuário clica em **Salvar**. Substitua os `HttpPost` `Edit` método com o código a seguir, que chama um novo método que atualiza o `Courses` propriedade de navegação do `Instructor` entidade. As alterações são realçadas.

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample15.cs?highlight=3,7,20,33,37-65)]

Como o modo de exibição não tem uma coleção de `Course` entidades, o associador de modelo não pode atualizar automaticamente o `Courses` propriedade de navegação. Em vez de usar o associador de modelo para atualizar a propriedade de navegação de cursos, você terá de fazer isso no novo `UpdateInstructorCourses` método. Portanto, você precisa excluir a propriedade `Courses` da associação de modelos. Isso não exige nenhuma alteração ao código que chama [TryUpdateModel](https://msdn.microsoft.com/library/dd470908(v=vs.98).aspx) porque você está usando o *lista de permissões* sobrecarga e `Courses` não está na lista de inclusão.

Se nenhuma seleção caixas tiverem sido selecionadas, o código na `UpdateInstructorCourses` inicializa o `Courses` propriedade de navegação com uma coleção vazia:

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample16.cs)]

Em seguida, o código executa um loop em todos os cursos no banco de dados e verifica cada curso em relação àqueles atribuídos no momento ao instrutor e em relação àqueles que foram selecionados na exibição. Para facilitar pesquisas eficientes, as últimas duas coleções são armazenadas em objetos `HashSet`.

Se a caixa de seleção para um curso foi marcada, mas o curso não está na propriedade de navegação `Instructor.Courses`, o curso é adicionado à coleção na propriedade de navegação.

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample17.cs)]

Se a caixa de seleção para um curso não foi marcada, mas o curso está na propriedade de navegação `Instructor.Courses`, o curso é removido da propriedade de navegação.

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample18.cs)]

No *Views\Instructor\Edit.cshtml*, adicione um **cursos** campo com uma matriz de caixas de seleção, adicionando a seguinte realçada de código imediatamente após o `div` elementos para o `OfficeAssignment` campo:

[!code-cshtml[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample19.cshtml?highlight=51-73)]

Esse código cria uma tabela HTML que contém três colunas. Em cada coluna há uma caixa de seleção, seguida de uma legenda que consiste no número e título do curso. Todas as caixas de seleção têm o mesmo nome ("selectedCourses"), que informa o associador de modelo que eles devem ser tratados como um grupo. O `value` atributo de cada caixa de seleção é definido como o valor da `CourseID.` quando a página é postada, o associador de modelos passa uma matriz para o controlador que consiste o `CourseID` valores para apenas as caixas de seleção que estão selecionados.

Quando as caixas de seleção são inicialmente renderizadas, aquelas que se destinam aos cursos atribuídos ao instrutor têm `checked` atributos, que seleciona (exibe-os como marcados).

Depois de alterar as atribuições de curso, você desejará ser capaz de verificar as alterações quando o site é retornado para o `Index` página. Portanto, você precisará adicionar uma coluna à tabela na página. Nesse caso, você não precisa usar o `ViewBag` do objeto, porque as informações que você deseja exibir já estão no `Courses` propriedade de navegação do `Instructor` entidade que você está passando para a página como o modelo.

Na *Views\Instructor\Index.cshtml*, adicione uma **cursos** título imediatamente após o **Office** título, conforme mostrado no exemplo a seguir:

[!code-html[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample20.html?highlight=7)]

Em seguida, adicione uma nova célula de detalhe imediatamente após a célula de detalhes de local do office:

[!code-cshtml[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample21.cshtml?highlight=19,50-57)]

Execute o **índice de instrutor** página para ver os cursos atribuídos a cada instrutor:

![Instructor_index_page](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image8.png)

Clique em **editar** em um instrutor para ver a página de edição.

![Instructor_edit_page_with_courses](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image9.png)

Altere algumas atribuições de curso e clique em **salvar**. As alterações feitas são refletidas na página Índice.

 Observação: A abordagem usada para editar os dados de curso do instrutor funciona bem quando há uma quantidade limitada de cursos. Para coleções muito maiores, uma interface do usuário e um método de atualização diferentes são necessários.  
 

## <a name="update-the-delete-method"></a>Atualize o método Delete

Altere o código no método HttpPost Delete para que o registro de atribuição do office (se houver) é excluído quando o instrutor é excluído:

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample22.cs?highlight=6,10)]


Se você tentar excluir um instrutor que é atribuído a um departamento como administrador, você obterá um erro de integridade referencial. Ver [a versão atual deste tutorial](../../getting-started/getting-started-with-ef-using-mvc/updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application.md) de código adicional que removerá automaticamente o instrutor de qualquer departamento em que o instrutor é atribuído como administrador.

## <a name="summary"></a>Resumo

Agora você concluiu esta introdução ao trabalhar com dados relacionados. Até agora esses tutoriais, você fez as operações de intervalo completo de CRUD, mas você ainda não lidou com problemas de simultaneidade. O próximo tutorial apresente o tópico da concorrência, explique as opções para lidar com isso e adicionar para o código CRUD que você já escreveu para o tipo de uma entidade de tratamento de simultaneidade.

Links para outros recursos do Entity Framework, pode ser encontrado no final da [o último tutorial desta série](advanced-entity-framework-scenarios-for-an-mvc-web-application.md).

> [!div class="step-by-step"]
> [Anterior](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application.md)
> [Próximo](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application.md)
