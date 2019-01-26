---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application
title: 'Tutorial: Atualizar dados relacionados com o EF em um aplicativo ASP.NET MVC'
description: Neste tutorial, você atualizará dados relacionados. Para a maioria das relações, isso pode ser feito atualizando os campos de chave estrangeira ou propriedades de navegação.
author: tdykstra
ms.author: riande
ms.date: 01/17/2019
ms.topic: tutorial
ms.assetid: 7ba88418-5d0a-437d-b6dc-7c3816d4ec07
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: daefbcea39dc341c6fc72eb344b05eb2dbf0c3ba
ms.sourcegitcommit: d5223cf6a2cf80b4f5dc54169b0e376d493d2d3a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54889841"
---
# <a name="tutorial-update-related-data-with-ef-in-an-aspnet-mvc-app"></a>Tutorial: Atualizar dados relacionados com o EF em um aplicativo ASP.NET MVC

No tutorial anterior, você exibiu dados relacionados. Neste tutorial, você atualizará dados relacionados. Para a maioria das relações, isso pode ser feito atualizando os campos de chave estrangeira ou propriedades de navegação. Para relações muitos-para-muitos, o Entity Framework não expõe a tabela de junção diretamente, para que você adiciona e remove entidades de e para as propriedades de navegação apropriado.

As ilustrações a seguir mostram algumas das páginas com as quais você trabalhará.

![Course_create_page](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image1.png)

![Instructor_edit_page_with_courses](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image2.png)

![Editar instrutor com cursos](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image3.png)

Neste tutorial, você:

> [!div class="checklist"]
> * Personalizar as páginas de cursos
> * Adicionar o office a página instrutores
> * Adicionar cursos à página instrutores
> * Atualizar DeleteConfirmed
> * Adicionar local de escritório e cursos para a página Criar

## <a name="prerequisites"></a>Pré-requisitos

* [Leitura de dados relacionados](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application.md)

## <a name="customize-courses-pages"></a>Personalizar as páginas de cursos

Quando uma nova entidade de curso é criada, ela precisa ter uma relação com um departamento existente. Para facilitar isso, o código gerado por scaffolding inclui métodos do controlador e exibições Criar e Editar que incluem uma lista suspensa para seleção do departamento. A lista suspensa define a `Course.DepartmentID` propriedade de chave estrangeira, e isso é tudo o que o Entity Framework precisa para carregar o `Department` propriedade de navegação com os devidos `Department` entidade. Você usará o código gerado por scaffolding, mas o alterará ligeiramente para adicionar tratamento de erro e classificação à lista suspensa.

Na *CourseController.cs*, exclua os quatro `Create` e `Edit` métodos e substituí-los com o código a seguir:

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample1.cs?highlight=3,11-12,19-25,40,44,46,48-78)]

Adicione o seguinte `using` instrução no início do arquivo:

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample2.cs)]

O `PopulateDepartmentsDropDownList` método obtém uma lista de todos os departamentos classificados por nome, cria um `SelectList` coleção para uma lista suspensa e passa a coleção para a exibição em um `ViewBag` propriedade. O método aceita o parâmetro `selectedDepartment` opcional que permite que o código de chamada especifique o item que será selecionado quando a lista suspensa for renderizada. A exibição passará o nome `DepartmentID` para o [DropDownList](../../older-versions/working-with-the-dropdownlist-box-and-jquery/using-the-dropdownlist-helper-with-aspnet-mvc.md) auxiliar e o auxiliar, em seguida, saberá que deve para examinar o `ViewBag` do objeto para um `SelectList` chamado `DepartmentID`.

O `HttpGet` `Create` chamadas de método a `PopulateDepartmentsDropDownList` método sem definir o item selecionado, porque um novo curso do departamento ainda não foi estabelecido:

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample3.cs)]

O `HttpGet` `Edit` método define o item selecionado, com base na ID do departamento que já está atribuído ao curso que está sendo editado:

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample4.cs?highlight=12)]

O `HttpPost` métodos para ambos `Create` e `Edit` também incluem o código que define o item selecionado quando eles exibem novamente a página após um erro:

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample5.cs?highlight=6)]

Esse código garante que quando a página será reexibida para mostrar a mensagem de erro, qualquer departamento selecionado permaneça selecionado.

As exibições curso já são gerados automaticamente com listas suspensas para o campo de departamento, mas não deseja que a legenda DepartmentID para esse campo, portanto, verifique realçado a seguir altera para o *Views\Course\Create.cshtml* arquivo Altere a legenda.

[!code-cshtml[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample6.cshtml?highlight=43)]

Fazer a mesma alteração no *Views\Course\Edit.cshtml*.

Normalmente o scaffolder de não criar o scaffolding de uma chave primária porque o valor da chave é gerado pelo banco de dados e não pode ser alterado e não é um valor significativo a ser exibido aos usuários. Para entidades Course, o scaffolder incluem uma caixa de texto para o `CourseID` campo porque ele entende que o `DatabaseGeneratedOption.None` atributo significa que o usuário deve ser capaz de inserir o valor de chave primária. Mas ela não entende que porque o número é significativo deseja vê-lo em outros modos, portanto, você precisará adicioná-lo manualmente.

Na *Views\Course\Edit.cshtml*, adicione um campo de número de curso antes do **título** campo. Como é a chave primária, ele é exibido, mas ele não pode ser alterado.

[!code-cshtml[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample7.cshtml)]

Já existe um campo oculto (`Html.HiddenFor` auxiliar) para o número de curso na exibição de edição. Adicionando um *Html.LabelFor* auxiliar não elimina a necessidade do campo oculto porque ele não causa o número de curso a ser incluído nos dados postados quando o usuário clica **salvar** na página de edição.

Na *Views\Course\Delete.cshtml* e *Views\Course\Details.cshtml*, altere a legenda de nome do departamento de "Name" para "Departamento" e adicione um campo de número de curso antes do **título**  campo.

[!code-cshtml[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample8.cshtml?highlight=2,9-15)]

Execute o **Create** página (exibir a página de índice do curso e clique em **criar novo**) e insira os dados para um novo curso:

| Valor | Configuração |
| ----- | ------- |
| Número | Enter *1000*. |
| Título | Insira *álgebra*. |
| Créditos | Enter *4*. |
|Departamento | Selecione **matemática**. |

Clique em **Criar**. A página de índice de cursos é exibida com o novo curso adicionado à lista. O nome do departamento na lista de páginas de Índice é obtido da propriedade de navegação, mostrando que a relação foi estabelecida corretamente.

Execute o **edite** página (exibir a página de índice do curso e clique em **editar** em um curso).

Altere dados na página e clique em **Salvar**. A página índice de cursos é exibida com os dados de curso atualizado.

## <a name="add-office-to-instructors-page"></a>Adicionar o office a página instrutores

Quando você edita um registro de instrutor, deseja poder atualizar a atribuição de escritório do instrutor. O `Instructor` entidade tem uma relação um-para-zero-ou-um com o `OfficeAssignment` entidade, o que significa que você deve lidar com as seguintes situações:

- Se o usuário limpar a atribuição de escritório e ela originalmente tinha um valor, remova e excluir o `OfficeAssignment` entidade.
- Se o usuário insere um valor de atribuição de escritório e ele originalmente estava vazio, você deve criar um novo `OfficeAssignment` entidade.
- Se o usuário altera o valor de uma atribuição de escritório, você deve alterar o valor em existente `OfficeAssignment` entidade.

Abra *InstructorController.cs* e examine o `HttpGet` `Edit` método:

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample9.cs)]

O código gerado por scaffolding aqui não é o que você deseja. Configurar dados para uma lista suspensa, mas você precisa é de uma caixa de texto. Substitua esse método com o código a seguir:

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample10.cs?highlight=7-10)]

Este código remove a `ViewBag` instrução e adiciona o carregamento adiantado para associado `OfficeAssignment` entidade. Não é possível executar o carregamento adiantado com o `Find` método, portanto, o `Where` e `Single` métodos são usados em vez disso, para selecionar o instrutor.

Substitua os `HttpPost` `Edit` método com o código a seguir. que trata de atualizações de atribuição do office:

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample11.cs)]

A referência ao `RetryLimitExceededException` requer um `using` instrução. Para adicioná-la, passe o mouse sobre `RetryLimitExceededException`. Obter uma explicação do problema é exibida. Selecione **Mostrar possíveis correções** e, em seguida, clique em **usando System.Data.Entity.Infrastructure;**.

![Resolver as exceções de repetição](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image7.png)

O código faz o seguinte:

- Altera o nome do método para `EditPost` porque a assinatura agora é igual a `HttpGet` método (o `ActionName` atributo especifica que a URL /Edit/ ainda é usada).
- Obtém a entidade `Instructor` atual do banco de dados usando o carregamento adiantado para a propriedade de navegação `OfficeAssignment`. Isso é o mesmo que você fez `HttpGet` `Edit` método.
- Atualiza a entidade `Instructor` recuperada com valores do associador de modelos. O [TryUpdateModel](https://msdn.microsoft.com/library/dd470908(v=vs.108).aspx) sobrecarga usada permite que você *lista branca* as propriedades que você deseja incluir. Isso impede o excesso de postagem, conforme explicado em [segundo tutorial](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application.md).

    [!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample12.cs)]
- Define se o local do escritório estiver em branco, o `Instructor.OfficeAssignment` propriedade como nulo, de modo que a linha relacionada no `OfficeAssignment` tabela será excluída.

    [!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample13.cs)]
- Salva as alterações no banco de dados.

Na *Views\Instructor\Edit.cshtml*após o `div` elementos para o **data de contratação** campo, adicione um novo campo para editar o local do escritório:

[!code-cshtml[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample14.cshtml)]

Execute a página (selecione o **instrutores** guia e, em seguida, clique em **editar** em um instrutor). Altere o **Local do Escritório** e clique em **Salvar**.

## <a name="add-courses-to-instructors-page"></a>Adicionar cursos à página instrutores

Os instrutores podem ministrar a quantidade de cursos que desejarem. Agora, você aprimorará a página Editar instrutor adicionando a capacidade de alterar as atribuições de curso usando um grupo de caixas de seleção.

A relação entre o `Course` e `Instructor` entidades é muitos-para-muitos, o que significa que você não tem acesso direto às propriedades de chave estrangeiras que estão na tabela de junção. Em vez disso, você pode adicionar e remover entidades de e para o `Instructor.Courses` propriedade de navegação.

A interface do usuário que permite alterar a quais cursos um instrutor é atribuído é um grupo de caixas de seleção. Uma caixa de seleção é exibida para cada curso no banco de dados, e aqueles aos quais o instrutor está atribuído no momento são marcados. O usuário pode marcar ou desmarcar as caixas de seleção para alterar as atribuições de curso. Se o número de cursos for muito maior, você provavelmente desejará usar um método diferente de apresentação dos dados no modo de exibição, mas você usaria o mesmo método de manipulação de propriedades de navegação para criar ou excluir relações.

Para fornecer dados à exibição para a lista de caixas de seleção, você usará uma classe de modelo de exibição. Crie *AssignedCourseData.cs* na *ViewModels* pasta e substitua o código existente pelo código a seguir:

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample15.cs)]

Na *InstructorController.cs*, substitua o `HttpGet` `Edit` método com o código a seguir. As alterações são realçadas.

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample16.cs?highlight=9,12,20-35)]

O código adiciona o carregamento adiantado à propriedade de navegação `Courses` e chama o novo método `PopulateAssignedCourseData` para fornecer informações para a matriz de caixa de seleção usando a classe de modelo de exibição `AssignedCourseData`.

O código a `PopulateAssignedCourseData` método lê todos `Course` entidades para carregar uma lista de cursos usando a exibição de classe de modelo. Para cada curso, o código verifica se o curso existe na propriedade de navegação `Courses` do instrutor. Para criar uma pesquisa eficiente ao verificar se um curso é atribuído ao instrutor, os cursos atribuídos ao instrutor são colocados em um [HashSet](https://msdn.microsoft.com/library/bb359438.aspx) coleção. O `Assigned` estiver definida como `true` para cursos o instrutor é atribuído. A exibição usará essa propriedade para determinar quais caixas de seleção precisam ser exibidas como selecionadas. Por fim, a lista é passada para a exibição em um `ViewBag` propriedade.

Em seguida, adicione o código que é executado quando o usuário clica em **Salvar**. Substitua os `EditPost` método com o código a seguir, que chama um novo método que atualiza o `Courses` propriedade de navegação do `Instructor` entidade. As alterações são realçadas.

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample17.cs?highlight=3,11,25,37,40-68)]

A assinatura de método agora é diferente de `HttpGet` `Edit` método, portanto, o nome do método muda de `EditPost` voltar ao `Edit`.

Como o modo de exibição não tem uma coleção de `Course` entidades, o associador de modelo não pode atualizar automaticamente o `Courses` propriedade de navegação. Em vez de usar o associador de modelo para atualizar o `Courses` propriedade de navegação, você terá de fazer isso no novo `UpdateInstructorCourses` método. Portanto, você precisa excluir a propriedade `Courses` do model binding. Isso não exige nenhuma alteração ao código que chama [TryUpdateModel](https://msdn.microsoft.com/library/dd470908(v=vs.98).aspx) porque você está usando o *lista de permissões* sobrecarga e `Courses` não está na lista de inclusão.

Se nenhuma seleção caixas tiverem sido selecionadas, o código na `UpdateInstructorCourses` inicializa o `Courses` propriedade de navegação com uma coleção vazia:

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample18.cs)]

Em seguida, o código executa um loop em todos os cursos no banco de dados e verifica cada curso em relação àqueles atribuídos no momento ao instrutor e em relação àqueles que foram selecionados na exibição. Para facilitar pesquisas eficientes, as últimas duas coleções são armazenadas em objetos `HashSet`.

Se a caixa de seleção para um curso foi marcada, mas o curso não está na propriedade de navegação `Instructor.Courses`, o curso é adicionado à coleção na propriedade de navegação.

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample19.cs)]

Se a caixa de seleção para um curso não foi marcada, mas o curso está na propriedade de navegação `Instructor.Courses`, o curso é removido da propriedade de navegação.

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample20.cs)]

No *Views\Instructor\Edit.cshtml*, adicione um **cursos** campo com uma matriz de caixas de seleção, adicionando o seguinte código imediatamente após o `div` elementos para o `OfficeAssignment` campo e antes do `div` elemento para o **salvar** botão:

[!code-cshtml[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample21.cshtml)]

Depois de colar o código, se as quebras de linha e o recuo não têm a mesma aparecem aqui, corrigir tudo manualmente para que ele se parece com o que você vê aqui. O recuo não precisa ser perfeito, mas cada uma das linhas `@</tr><tr>`, `@:<td>`, `@:</td>` e `@</tr>` precisa estar em uma única linha, conforme mostrado, ou você receberá um erro de tempo de execução.

Esse código cria uma tabela HTML que contém três colunas. Em cada coluna há uma caixa de seleção, seguida de uma legenda que consiste no número e título do curso. Todas as caixas de seleção têm o mesmo nome ("selectedCourses"), que informa o associador de modelo que eles devem ser tratados como um grupo. O `value` atributo de cada caixa de seleção é definido como o valor da `CourseID.` quando a página é postada, o associador de modelos passa uma matriz para o controlador que consiste o `CourseID` valores para apenas as caixas de seleção que estão selecionados.

Quando as caixas de seleção são inicialmente renderizadas, aquelas que se destinam aos cursos atribuídos ao instrutor têm `checked` atributos, que seleciona (exibe-os como marcados).

Depois de alterar as atribuições de curso, você desejará ser capaz de verificar as alterações quando o site é retornado para o `Index` página. Portanto, você precisará adicionar uma coluna à tabela na página. Nesse caso, você não precisa usar o `ViewBag` do objeto, porque as informações que você deseja exibir já estão no `Courses` propriedade de navegação do `Instructor` entidade que você está passando para a página como o modelo.

Na *Views\Instructor\Index.cshtml*, adicione uma **cursos** título imediatamente após o **Office** título, conforme mostrado no exemplo a seguir:

[!code-cshtml[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample22.cshtml?highlight=6)]

Em seguida, adicione uma nova célula de detalhe imediatamente após a célula de detalhes de local do office:

[!code-cshtml[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample23.cshtml?highlight=7-14)]

Execute o **índice de instrutor** página para ver os cursos atribuídos a cada instrutor.

Clique em **editar** em um instrutor para ver a página de edição.

Altere algumas atribuições de curso e clique em **salvar**. As alterações feitas são refletidas na página Índice.

 Observação: A abordagem usada aqui para editar os dados de curso do instrutor funciona bem quando há uma quantidade limitada de cursos. Para coleções muito maiores, uma interface do usuário e um método de atualização diferentes são necessários.

## <a name="update-deleteconfirmed"></a>Atualizar DeleteConfirmed

Na *InstructorController.cs*, exclua o `DeleteConfirmed` método e insira o seguinte código em seu lugar.

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample24.cs?highlight=5-8,12-18)]

Este código faz a seguinte alteração:

- Se o instrutor é atribuído como administrador de qualquer departamento, remove a atribuição de instrutor desse departamento. Sem esse código, você obterá um erro de integridade referencial, se você tentou excluir um instrutor que foi atribuído como administrador de um departamento.

Esse código não tratará o cenário de um instrutor atribuído como administrador para vários departamentos. O último tutorial, você adicionará código que impede que esse cenário está acontecendo.

## <a name="add-office-location-and-courses-to-the-create-page"></a>Adicionar local de escritório e cursos para a página Criar

Na *InstructorController.cs*, exclua o `HttpGet` e `HttpPost` `Create` métodos e, em seguida, adicione o código a seguir em seu lugar:


[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample25.cs)]

Esse código é semelhante ao que você viu para os métodos de edição, exceto que inicialmente nenhum curso está selecionado. O `HttpGet` `Create` chamadas de método de `PopulateAssignedCourseData` método não porque pode haver cursos selecionados, mas a fim de fornecer uma coleção vazia para o `foreach` loop no modo de exibição (caso contrário, o código de exibição gera uma exceção de referência nula ).

O método HttpPost Create adiciona cada curso selecionado para a propriedade de navegação de cursos antes do código de modelo que verifica se há erros de validação e adiciona o novo instrutor ao banco de dados. Cursos são adicionados, mesmo se houver erros de modelo, de modo que quando há erros de modelo (por exemplo, o usuário inserido uma data inválida), de modo que quando a página é exibida novamente com uma mensagem de erro, as seleções de cursos que foram feitas são restauradas automaticamente.

Observe que para poder adicionar cursos à propriedade de navegação `Courses`, é necessário inicializar a propriedade como uma coleção vazia:

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample26.cs)]

Como alternativa a fazer isso no código do controlador, faça isso no modelo Instrutor alterando o getter de propriedade para criar automaticamente a coleção se ela não existir, conforme mostrado no seguinte exemplo:

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample27.cs)]

Se você modificar a propriedade `Courses` dessa forma, poderá remover o código de inicialização de propriedade explícita no controlador.

Na *Views\Instructor\Create.cshtml*, adicione uma caixa de texto de local de escritório e caixas de seleção de cursos, depois que o campo de data de contratação e antes do **enviar** botão.

[!code-cshtml[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample28.cshtml)]

Depois de colar o código, corrigi quebras de linha e recuo como fez anteriormente para a página de edição.

Execute a página criar e adicionar um instrutor.

<a id="transactions"></a>

## <a name="handling-transactions"></a>Processamento de transações

Conforme explicado a [tutorial de funcionalidade básica de CRUD](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application.md), por padrão o Entity Framework implementa implicitamente transações. Para cenários em que você precisa de mais controle – por exemplo, se você quiser incluir operações feitas fora do Entity Framework em uma transação –, consulte [trabalhando com transações](https://msdn.microsoft.com/data/dn456843) no MSDN.

## <a name="get-the-code"></a>Obter o código

[Baixe o projeto concluído](https://webpifeed.blob.core.windows.net/webpifeed/Partners/ASP.NET%20MVC%20Application%20Using%20Entity%20Framework%20Code%20First.zip)

## <a name="additional-resources"></a>Recursos adicionais

Links para outros recursos do Entity Framework pode ser encontrado na [acesso a dados ASP.NET – recursos recomendados](../../../../whitepapers/aspnet-data-access-content-map.md).

## <a name="next-step"></a>Próximas etapas

Neste tutorial, você:

> [!div class="checklist"]
> * Páginas de cursos personalizados
> * Office adicionado à página instrutores
> * Cursos adicionados à página instrutores
> * DeleteConfirmed atualizado
> * Local do escritório adicionado e cursos para a página criar

Avance para o próximo artigo para saber como implementar um modelo de programação assíncrona.
> [!div class="nextstepaction"]
> [Modelo de programação assíncrona](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application.md)
