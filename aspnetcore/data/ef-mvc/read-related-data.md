---
title: 'Tutorial: Ler dados relacionados - ASP.NET MVC com EF Core'
description: Neste tutorial, você lerá e exibirá dados relacionados – ou seja, os dados que o Entity Framework carrega nas propriedades de navegação.
author: rick-anderson
ms.author: riande
ms.date: 09/28/2019
ms.topic: tutorial
uid: data/ef-mvc/read-related-data
ms.openlocfilehash: 1a16f905669d0192d713cbba9bfc6e3e75008b5d
ms.sourcegitcommit: 7d3c6565dda6241eb13f9a8e1e1fd89b1cfe4d18
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72259428"
---
# <a name="tutorial-read-related-data---aspnet-mvc-with-ef-core"></a>Tutorial: Ler dados relacionados - ASP.NET MVC com EF Core

No tutorial anterior, você concluiu o modelo de dados Escola. Neste tutorial, você lerá e exibirá dados relacionados – ou seja, os dados que o Entity Framework carrega nas propriedades de navegação.

As ilustrações a seguir mostram as páginas com as quais você trabalhará.

![Página Índice de Cursos](read-related-data/_static/courses-index.png)

![Página Índice de Instrutores](read-related-data/_static/instructors-index.png)

Neste tutorial, você:

> [!div class="checklist"]
> * Aprender a carregar entidades relacionadas
> * Criar uma página Cursos
> * Criar uma página Instrutores
> * Aprender sobre o carregamento explícito

## <a name="prerequisites"></a>Pré-requisitos

* [Criar um modelo de dados complexo](complex-data-model.md)

## <a name="learn-how-to-load-related-data"></a>Aprender a carregar entidades relacionadas

Há várias maneiras pelas quais um software ORM (Object-Relational Mapping), como o Entity Framework, pode carregar dados relacionados nas propriedades de navegação de uma entidade:

* Carregamento adiantado. Quando a entidade é lida, os dados relacionados são recuperados com ela. Normalmente, isso resulta em uma única consulta de junção que recupera todos os dados necessários. Especifique o carregamento adiantado no Entity Framework Core usando os métodos `Include` e `ThenInclude`.

  ![Exemplo de carregamento adiantado](read-related-data/_static/eager-loading.png)

  Recupere alguns dos dados em consultas separadas e o EF "corrigirá" as propriedades de navegação.  Ou seja, o EF adiciona de forma automática as entidades recuperadas separadamente no local em que pertencem nas propriedades de navegação de entidades recuperadas anteriormente. Para a consulta que recupera dados relacionados, você pode usar o método `Load` em vez de um método que retorna uma lista ou um objeto, como `ToList` ou `Single`.

  ![Exemplo de consultas separadas](read-related-data/_static/separate-queries.png)

* Carregamento explícito. Quando a entidade é lida pela primeira vez, os dados relacionados não são recuperados. Você escreve o código que recupera os dados relacionados se eles são necessários. Como no caso do carregamento adiantado com consultas separadas, o carregamento explícito resulta no envio de várias consultas ao banco de dados. A diferença é que, com o carregamento explícito, o código especifica as propriedades de navegação a serem carregadas. No Entity Framework Core 1.1, você pode usar o método `Load` para fazer o carregamento explícito. Por exemplo:

  ![Exemplo de carregamento explícito](read-related-data/_static/explicit-loading.png)

* Carregamento lento. Quando a entidade é lida pela primeira vez, os dados relacionados não são recuperados. No entanto, na primeira vez que você tenta acessar uma propriedade de navegação, os dados necessários para essa propriedade de navegação são recuperados automaticamente. Uma consulta é enviada ao banco de dados sempre que você tenta obter dados de uma propriedade de navegação pela primeira vez. O Entity Framework Core 1.0 não dá suporte ao carregamento lento.

### <a name="performance-considerations"></a>Considerações sobre o desempenho

Se você sabe que precisa de dados relacionados para cada entidade recuperada, o carregamento adiantado costuma oferecer o melhor desempenho, porque uma única consulta enviada para o banco de dados é geralmente mais eficiente do que consultas separadas para cada entidade recuperada. Por exemplo, suponha que cada departamento tenha dez cursos relacionados. O carregamento adiantado de todos os dados relacionados resultará em apenas uma única consulta (junção) e uma única viagem de ida e volta para o banco de dados. Uma consulta separada para cursos de cada departamento resultará em onze viagens de ida e volta para o banco de dados. As viagens de ida e volta extras para o banco de dados são especialmente prejudiciais ao desempenho quando a latência é alta.

Por outro lado, em alguns cenários, consultas separadas são mais eficientes. O carregamento adiantado de todos os dados relacionados em uma consulta pode fazer com que uma junção muito complexa seja gerada, que o SQL Server não consegue processar com eficiência. Ou se precisar acessar as propriedades de navegação de uma entidade somente para um subconjunto de um conjunto de entidades que está sendo processado, consultas separadas poderão ter um melhor desempenho, pois o carregamento adiantado de tudo desde o início recupera mais dados do que você precisa. Se o desempenho for crítico, será melhor testar o desempenho das duas maneiras para fazer a melhor escolha.

## <a name="create-a-courses-page"></a>Criar uma página Cursos

A entidade Course inclui uma propriedade de navegação que contém a entidade Department do departamento ao qual o curso é atribuído. Para exibir o nome do departamento atribuído em uma lista de cursos, você precisa obter a propriedade Name da entidade Department que está na propriedade de navegação `Course.Department`.

Crie um controlador chamado CoursesController para o tipo de entidade Course, usando as mesmas opções para o scaffolder **Controlador MVC com exibições, usando o Entity Framework** que você usou anteriormente para o controlador Alunos, conforme mostrado na seguinte ilustração:

![Adicionar o controlador Courses](read-related-data/_static/add-courses-controller.png)

Abra *CoursesController.cs* e examine o método `Index`. O scaffolding automático especificou o carregamento adiantado para a propriedade de navegação `Department` usando o método `Include`.

Substitua o método `Index` pelo seguinte código, que usa um nome mais apropriado para o `IQueryable` que retorna as entidades Course (`courses` em vez de `schoolContext`):

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_RevisedIndexMethod)]

Abra *Views/Courses/Index.cshtml* e substitua o código de modelo pelo código a seguir. As alterações são realçadas:

[!code-html[](intro/samples/cu/Views/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

Você fez as seguintes alterações no código gerado por scaffolding:

* Alterou o cabeçalho de Índice para Cursos.

* Adicionou uma coluna **Número** que mostra o valor da propriedade `CourseID`. Por padrão, as chaves primárias não são geradas por scaffolding porque normalmente não têm sentido para os usuários finais. No entanto, nesse caso, a chave primária é significativa e você deseja mostrá-la.

* Alterou a coluna **Departamento** para que ela exiba o nome de departamento. O código exibe a propriedade `Name` da entidade Department que é carregada na propriedade de navegação `Department`:

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

Execute o aplicativo e selecione a guia **Cursos** para ver a lista com nomes de departamentos.

![Página Índice de Cursos](read-related-data/_static/courses-index.png)

## <a name="create-an-instructors-page"></a>Criar uma página Instrutores

Nesta seção, você criará um controlador e uma exibição para a entidade Instructor para exibir a página Instrutores:

![Página Índice de Instrutores](read-related-data/_static/instructors-index.png)

Essa página lê e exibe dados relacionados das seguintes maneiras:

* A lista de instrutores exibe dados relacionados da entidade OfficeAssignment. As entidades Instructor e OfficeAssignment estão em uma relação um para zero ou um. Você usará o carregamento adiantado para as entidades OfficeAssignment. Conforme explicado anteriormente, o carregamento adiantado é geralmente mais eficiente quando você precisa dos dados relacionados para todas as linhas recuperadas da tabela primária. Nesse caso, você deseja exibir atribuições de escritório para todos os instrutores exibidos.

* Quando o usuário seleciona um instrutor, as entidades Course relacionadas são exibidas. As entidades Instructor e Course estão em uma relação muitos para muitos. Você usará o carregamento adiantado para as entidades Course e suas entidades Department relacionadas. Nesse caso, consultas separadas podem ser mais eficientes porque você precisa de cursos somente para o instrutor selecionado. No entanto, este exemplo mostra como usar o carregamento adiantado para propriedades de navegação em entidades que estão nas propriedades de navegação.

* Quando o usuário seleciona um curso, dados relacionados do conjunto de entidades Enrollments são exibidos. As entidades Course e Enrollment estão em uma relação um para muitos. Você usará consultas separadas para entidades Enrollment e suas entidades Student relacionadas.

### <a name="create-a-view-model-for-the-instructor-index-view"></a>Criar um modelo de exibição para a exibição Índice de Instrutor

A página Instrutores mostra dados de três tabelas diferentes. Portanto, você criará um modelo de exibição que inclui três propriedades, cada uma contendo os dados de uma das tabelas.

Na pasta *SchoolViewModels*, crie *InstructorIndexData.cs* e substitua o código existente pelo seguinte código:

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="create-the-instructor-controller-and-views"></a>Criar exibições e o controlador Instrutor

Crie um controlador Instrutores com ações de leitura/gravação do EF, conforme mostrado na seguinte ilustração:

![Adicionar o controlador Instrutores](read-related-data/_static/add-instructors-controller.png)

Abra *InstructorsController.cs* e adicione um usando a instrução para o namespace ViewModels:

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Using)]

Substitua o método Index pelo código a seguir para fazer o carregamento adiantado de dados relacionados e colocá-los no modelo de exibição.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EagerLoading)]

O método aceita dados de rota opcionais (`id`) e um parâmetro de cadeia de caracteres de consulta (`courseID`) que fornece os valores de ID do curso e do instrutor selecionados. Os parâmetros são fornecidos pelos hiperlinks **Selecionar** na página.

O código começa com a criação de uma instância do modelo de exibição e colocando-a na lista de instrutores. O código especifica o carregamento adiantado para as propriedades de navegação `Instructor.OfficeAssignment` e `Instructor.CourseAssignments`. Dentro da propriedade `CourseAssignments`, a propriedade `Course` é carregada e, dentro dela, as propriedades `Enrollments` e `Department` são carregadas e, dentro de cada entidade `Enrollment`, a propriedade `Student` é carregada.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude)]

Como a exibição sempre exige a entidade OfficeAssignment, é mais eficiente buscar isso na mesma consulta. As entidades Course são necessárias quando um instrutor é selecionado na página da Web; portanto, uma única consulta é melhor do que várias consultas apenas se a página é exibida com mais frequência com um curso selecionado do que sem ele.

O código repete `CourseAssignments` e `Course` porque você precisa de duas propriedades de `Course`. A primeira cadeia de caracteres de chamadas `ThenInclude` obtém `CourseAssignment.Course`, `Course.Enrollments` e `Enrollment.Student`.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=3-6)]

Nesse ponto do código, outro `ThenInclude` se refere às propriedades de navegação de `Student`, que não é necessário. Mas a chamada a `Include` é reiniciada com propriedades `Instructor` e, portanto, você precisa passar pela cadeia novamente, dessa vez, especificando `Course.Department` em vez de `Course.Enrollments`.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=7-9)]

O código a seguir é executado quando o instrutor é selecionado. O instrutor selecionado é recuperado da lista de instrutores no modelo de exibição. Em seguida, a propriedade `Courses` do modelo de exibição é carregada com as entidades Course da propriedade de navegação `CourseAssignments` desse instrutor.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=56-62)]

O método `Where` retorna uma coleção, mas nesse caso, os critérios passado para esse método resultam no retorno de apenas uma única entidade Instructor. O método `Single` converte a coleção em uma única entidade Instructor, que fornece acesso à propriedade `CourseAssignments` dessa entidade. A propriedade `CourseAssignments` contém entidades `CourseAssignment`, das quais você deseja apenas entidades `Course` relacionadas.

Use o método `Single` em uma coleção quando souber que a coleção terá apenas um item. O método Single gera uma exceção se a coleção passada para ele está vazia ou se há mais de um item. Uma alternativa é `SingleOrDefault`, que retorna um valor padrão (nulo, nesse caso) se a coleção está vazia. No entanto, nesse caso, isso ainda resultará em uma exceção (da tentativa de encontrar uma propriedade `Courses` em uma referência nula), e a mensagem de exceção menos claramente indicará a causa do problema. Quando você chama o método `Single`, também pode passar a condição Where, em vez de chamar o método `Where` separadamente:

```csharp
.Single(i => i.ID == id.Value)
```

Em vez de:

```csharp
.Where(i => i.ID == id.Value).Single()
```

Em seguida, se um curso foi selecionado, o curso selecionado é recuperado na lista de cursos no modelo de exibição. Em seguida, a propriedade `Enrollments` do modelo de exibição é carregada com as entidades Enrollment da propriedade de navegação `Enrollments` desse curso.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=64-69)]

### <a name="modify-the-instructor-index-view"></a>Modificar a exibição Índice de Instrutor

Em *Views/Instructors/Index.cshtml*, substitua o código de modelo pelo código a seguir. As alterações são realçadas.

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=1-64&highlight=1,3-7,15-19,24,26-31,41-54,56)]

Você fez as seguintes alterações no código existente:

* Alterou a classe de modelo para `InstructorIndexData`.

* Alterou o título de página de **Índice** para **Instrutores**.

* Adicionou uma coluna **Office** que exibe `item.OfficeAssignment.Location` somente se `item.OfficeAssignment` não é nulo. (Como essa é uma relação um para zero ou um, pode não haver uma entidade OfficeAssignment relacionada.)

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* Adicionou uma coluna **Courses** que exibe os cursos ministrados por cada instrutor. Para obter mais informações, consulte a seção [transição de linha explícita](xref:mvc/views/razor#explicit-line-transition) do artigo sintaxe Razor.

* Adicionou um código que adiciona `class="success"` dinamicamente ao elemento `tr` do instrutor selecionado. Isso define uma cor da tela de fundo para a linha selecionada usando uma classe Bootstrap.

  ```html
  string selectedRow = "";
  if (item.ID == (int?)ViewData["InstructorID"])
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* Adicionou um novo hiperlink rotulado **Selecionar** imediatamente antes dos outros links em cada linha, o que faz com que a ID do instrutor selecionado seja enviada para o método `Index`.

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

Execute o aplicativo e selecione a guia **Instrutores**. A página exibe a propriedade Location das entidades OfficeAssignment relacionadas e uma célula de tabela vazia quando não há nenhuma entidade OfficeAssignment relacionada.

![Página Índice de Instrutores – nenhuma opção selecionada](read-related-data/_static/instructors-index-no-selection.png)

No arquivo *Views/Instructors/Index.cshtml*, após o elemento de tabela de fechamento (ao final do arquivo), adicione o código a seguir. Esse código exibe uma lista de cursos relacionados a um instrutor quando um instrutor é selecionado.

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=66-101)]

Esse código lê a propriedade `Courses` do modelo de exibição para exibir uma lista de cursos. Também fornece um hiperlink **Selecionar** que envia a ID do curso selecionado para o método de ação `Index`.

Atualize a página e selecione um instrutor. Agora, você verá uma grade que exibe os cursos atribuídos ao instrutor selecionado, e para cada curso, verá o nome do departamento atribuído.

![Página Índice de Instrutores – instrutor selecionado](read-related-data/_static/instructors-index-instructor-selected.png)

Após o bloco de código que você acabou de adicionar, adicione o código a seguir. Isso exibe uma lista dos alunos que estão registrados em um curso quando esse curso é selecionado.

[!code-html[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=103-125)]

Esse código lê a propriedade Enrollments do modelo de exibição para exibir uma lista dos alunos registrados no curso.

Atualize a página novamente e selecione um instrutor. Em seguida, selecione um curso para ver a lista de alunos registrados e suas notas.

![Página Índice de Instrutores – instrutor e curso selecionados](read-related-data/_static/instructors-index.png)

## <a name="about-explicit-loading"></a>Sobre o carregamento explícito

Quando você recuperou a lista de instrutores em *InstructorsController.cs*, você especificou o carregamento adiantado para a propriedade de navegação `CourseAssignments`.

Suponha que os usuários esperados raramente desejem ver registros em um curso e um instrutor selecionados. Nesse caso, talvez você deseje carregar os dados de registro somente se eles forem solicitados. Para ver um exemplo de como fazer carregamento explícito, substitua o método `Index` pelo código a seguir, que remove o carregamento adiantado para Enrollments e carrega essa propriedade de forma explícita. As alterações de código são realçadas.

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ExplicitLoading&highlight=23-29)]

O novo código remove as chamadas do método *ThenInclude* para dados de registro do código que recupera as entidades do instrutor. Também solta `AsNoTracking`.  Se um curso e um instrutor são selecionados, o código realçado recupera entidades Enrollment para o curso selecionado e as entidades Student para cada Enrollment.

Execute que o aplicativo, acesse a página Índice de Instrutores agora e você não verá nenhuma diferença no que é exibido na página, embora você tenha alterado a maneira como os dados são recuperados.

## <a name="get-the-code"></a>Obter o código

[Baixe ou exiba o aplicativo concluído.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você:

> [!div class="checklist"]
> * Aprendeu a carregar dados relacionados
> * Criou uma página Cursos
> * Criou uma página Instrutores
> * Aprendeu sobre o carregamento explícito

Vá para o próximo tutorial para aprender a atualizar dados relacionados.

> [!div class="nextstepaction"]
> [Atualizar dados relacionados](update-related-data.md)
