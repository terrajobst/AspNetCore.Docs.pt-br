---
title: 'Tutorial: Criar um modelo de dados complexo - ASP.NET MVC com EF Core'
description: Neste tutorial, você adiciona mais entidades e relações e personaliza o modelo de dados especificando formatação, validação e regras de mapeamento.
author: rick-anderson
ms.author: tdykstra
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/complex-data-model
ms.openlocfilehash: 2776e3357941d0e7932882c39af121f85d037d62
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64887241"
---
# <a name="tutorial-create-a-complex-data-model---aspnet-mvc-with-ef-core"></a>Tutorial: Criar um modelo de dados complexo - ASP.NET MVC com EF Core

Nos tutoriais anteriores, você trabalhou com um modelo de dados simples composto por três entidades. Neste tutorial, você adicionará mais entidades e relações e personalizará o modelo de dados especificando formatação, validação e regras de mapeamento de banco de dados.

Quando terminar, as classes de entidade formarão o modelo de dados concluído mostrado na seguinte ilustração:

![Diagrama de entidade](complex-data-model/_static/diagram.png)

Neste tutorial, você:

> [!div class="checklist"]
> * Personalizar o Modelo de dados
> * Fazer alterações na entidade Student
> * Criar a entidade Instructor
> * Criar a entidade OfficeAssignment
> * Modificar a entidade Course
> * Criar a entidade Department
> * Modificar a entidade Enrollment
> * Atualizar o contexto de banco de dados
> * Propagar o banco de dados com dados de teste
> * Adicionar uma migração
> * Alterar a cadeia de conexão
> * Atualizar o banco de dados

## <a name="prerequisites"></a>Pré-requisitos

* [Usar as migrações do EF Core](migrations.md)

## <a name="customize-the-data-model"></a>Personalizar o Modelo de dados

Nesta seção, você verá como personalizar o modelo de dados usando atributos que especificam formatação, validação e regras de mapeamento de banco de dados. Em seguida, em várias seções a seguir, você criará o modelo de dados Escola completo com a adição de atributos às classes já criadas e criação de novas classes para os demais tipos de entidade no modelo.

### <a name="the-datatype-attribute"></a>O atributo DataType

Para datas de registro de alunos, todas as páginas da Web atualmente exibem a hora junto com a data, embora tudo o que você deseje exibir nesse campo seja a data. Usando atributos de anotação de dados, você pode fazer uma alteração de código que corrigirá o formato de exibição em cada exibição que mostra os dados. Para ver um exemplo de como fazer isso, você adicionará um atributo à propriedade `EnrollmentDate` na classe `Student`.

Em *Models/Student.cs*, adicione uma instrução `using` ao namespace `System.ComponentModel.DataAnnotations` e adicione os atributos `DataType` e `DisplayFormat` à `EnrollmentDate` propriedade, conforme mostrado no seguinte exemplo:

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

O atributo `DataType` é usado para especificar um tipo de dados mais específico do que o tipo intrínseco de banco de dados. Nesse caso, apenas desejamos acompanhar a data, não a data e a hora. A Enumeração `DataType` fornece muitos tipos de dados, como Date, Time, PhoneNumber, Currency, EmailAddress e muito mais. O atributo `DataType` também pode permitir que o aplicativo forneça automaticamente recursos específicos a um tipo. Por exemplo, um link `mailto:` pode ser criado para `DataType.EmailAddress` e um seletor de data pode ser fornecido para `DataType.Date` em navegadores que dão suporte a HTML5. O atributo `DataType` emite atributos `data-` HTML 5 (pronunciados “data dash”) que são reconhecidos pelos navegadores HTML 5. Os atributos `DataType` não fornecem nenhuma validação.

`DataType.Date` não especifica o formato da data exibida. Por padrão, o campo de dados é exibido de acordo com os formatos padrão com base nas CultureInfo do servidor.

O atributo `DisplayFormat` é usado para especificar explicitamente o formato de data:

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

A configuração `ApplyFormatInEditMode` especifica que a formatação também deve ser aplicada quando o valor é exibido em uma caixa de texto para edição. (Talvez você não deseje ter isso em alguns campos – por exemplo, para valores de moeda, provavelmente, você não deseja exibir o símbolo de moeda na caixa de texto para edição.)

Use Você pode usar o atributo `DisplayFormat` por si só, mas geralmente é uma boa ideia usar o atributo `DataType` também. O atributo `DataType` transmite a semântica dos dados, ao invés de apresentar como renderizá-lo em uma tela, e oferece os seguintes benefícios que você não obtém com `DisplayFormat`:

* O navegador pode habilitar os recursos do HTML5 (por exemplo, mostrar um controle de calendário, o símbolo de moeda apropriado à localidade, links de email, uma validação de entrada do lado do cliente, etc.).

* Por padrão, o navegador renderizará os dados usando o formato correto de acordo com a localidade.

Para obter mais informações, consulte a [documentação do auxiliar de marcação \<input>](../../mvc/views/working-with-forms.md#the-input-tag-helper).

Execute o aplicativo, acesse a página Índice de Alunos e observe que as horas não são mais exibidas nas datas de registro. O mesmo será verdadeiro para qualquer exibição que usa o modelo Aluno.

![Página Índice de Alunos mostrando datas sem horas](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a>O atributo StringLength

Você também pode especificar regras de validação de dados e mensagens de erro de validação usando atributos. O atributo `StringLength` define o tamanho máximo do banco de dados e fornece validação do lado do cliente e do lado do servidor para o ASP.NET Core MVC. Você também pode especificar o tamanho mínimo da cadeia de caracteres nesse atributo, mas o valor mínimo não tem nenhum impacto sobre o esquema de banco de dados.

Suponha que você deseje garantir que os usuários não insiram mais de 50 caracteres em um nome. Para adicionar essa limitação, adicione atributos `StringLength` às propriedades `LastName` e `FirstMidName`, conforme mostrado no seguinte exemplo:

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

O atributo `StringLength` não impedirá que um usuário insira um espaço em branco em um nome. Use o atributo `RegularExpression` para aplicar restrições à entrada. Por exemplo, o seguinte código exige que o primeiro caractere esteja em maiúscula e os caracteres restantes estejam em ordem alfabética:

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

O atributo `MaxLength` fornece uma funcionalidade semelhante ao atributo `StringLength`, mas não fornece a validação do lado do cliente.

Agora, o modelo de banco de dados foi alterado de uma forma que exige uma alteração no esquema de banco de dados. Você usará migrações para atualizar o esquema sem perda dos dados que podem ter sido adicionados ao banco de dados usando a interface do usuário do aplicativo.

Salve as alterações e compile o projeto. Em seguida, abra a janela Comando na pasta do projeto e insira os seguintes comandos:

```console
dotnet ef migrations add MaxLengthOnNames
```

```console
dotnet ef database update
```

O comando `migrations add` alerta que pode ocorrer perda de dados, pois a alteração torna o tamanho máximo mais curto para duas colunas.  As migrações criam um arquivo chamado *\<timeStamp>_MaxLengthOnNames.cs*. Esse arquivo contém o código no método `Up` que atualizará o banco de dados para que ele corresponda ao modelo de dados atual. O comando `database update` executou esse código.

O carimbo de data/hora prefixado ao nome do arquivo de migrações é usado pelo Entity Framework para ordenar as migrações. Crie várias migrações antes de executar o comando de atualização de banco de dados e, em seguida, todas as migrações são aplicadas na ordem em que foram criadas.

Execute o aplicativo, selecione a guia **Alunos**, clique em **Criar Novo** e tente inserir um nome com mais de 50 caracteres. O aplicativo deve impedir isso. 

### <a name="the-column-attribute"></a>O atributo Column

Você também pode usar atributos para controlar como as classes e propriedades são mapeadas para o banco de dados. Suponha que você tenha usado o nome `FirstMidName` para o campo de nome porque o campo também pode conter um sobrenome. Mas você deseja que a coluna do banco de dados seja nomeada `FirstName`, pois os usuários que escreverão consultas ad hoc no banco de dados estão acostumados com esse nome. Para fazer esse mapeamento, use o atributo `Column`.

O atributo `Column` especifica que quando o banco de dados for criado, a coluna da tabela `Student` que é mapeada para a propriedade `FirstMidName` será nomeada `FirstName`. Em outras palavras, quando o código se referir a `Student.FirstMidName`, os dados serão obtidos ou atualizados na coluna `FirstName` da tabela `Student`. Se você não especificar nomes de coluna, elas receberão o mesmo nome da propriedade.

No arquivo *Student.cs*, adicione uma instrução `using` a `System.ComponentModel.DataAnnotations.Schema` e adicione o atributo de nome de coluna à propriedade `FirstMidName`, conforme mostrado no seguinte código realçado:

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Column&highlight=4,14)]

A adição do atributo `Column` altera o modelo que dá suporte ao `SchoolContext` e, portanto, ele não corresponde ao banco de dados.

Salve as alterações e compile o projeto. Em seguida, abra a janela Comando na pasta do projeto e insira os seguintes comandos para criar outra migração:

```console
dotnet ef migrations add ColumnFirstName
```

```console
dotnet ef database update
```

No **Pesquisador de Objetos do SQL Server**, abra o designer de tabela Aluno clicando duas vezes na tabela **Aluno**.

![Tabela Alunos no SSOX após as migrações](complex-data-model/_static/ssox-after-migration.png)

Antes de você aplicar as duas primeiras migrações, as colunas de nome eram do tipo nvarchar(MAX). Agora elas são nvarchar(50) e o nome da coluna foi alterado de FirstMidName para FirstName.

> [!Note]
> Se você tentar compilar antes de concluir a criação de todas as classes de entidade nas seções a seguir, poderá receber erros do compilador.

## <a name="changes-to-student-entity"></a>Alterações na entidade Student

![Entidade Student](complex-data-model/_static/student-entity.png)

Em *Models/Student.cs*, substitua o código que você adicionou anteriormente pelo código a seguir. As alterações são realçadas.

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a>O atributo Required

O atributo `Required` torna as propriedades de nome campos obrigatórios. O atributo `Required` não é necessário para tipos que permitem valor nulo como tipos de valor (DateTime, int, double, float, etc.). Tipos que não podem ser nulos são tratados automaticamente como campos obrigatórios.

Remova o atributo `Required` e substitua-o por um parâmetro de tamanho mínimo para o atributo `StringLength`:

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a>O atributo Display

O atributo `Display` especifica que a legenda para as caixas de texto deve ser "Nome", "Sobrenome", "Nome Completo" e "Data de Registro", em vez do nome de a propriedade em cada instância (que não tem nenhum espaço entre as palavras).

### <a name="the-fullname-calculated-property"></a>A propriedade calculada FullName

`FullName` é uma propriedade calculada que retorna um valor criado pela concatenação de duas outras propriedades. Portanto, ela tem apenas um acessador get e nenhuma coluna `FullName` será gerada no banco de dados.

## <a name="create-instructor-entity"></a>Criar a entidade Instructor

![Entidade Instructor](complex-data-model/_static/instructor-entity.png)

Crie *Models/Instructor.cs*, substituindo o código de modelo com o seguinte código:

[!code-csharp[](intro/samples/cu/Models/Instructor.cs?name=snippet_BeforeInheritance)]

Observe que várias propriedades são as mesmas nas entidades Student e Instructor. No tutorial [Implementando a herança](inheritance.md) mais adiante nesta série, você refatorará esse código para eliminar a redundância.

Coloque vários atributos em uma linha, de modo que você também possa escrever os atributos `HireDate` da seguinte maneira:

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a>As propriedades de navegação CourseAssignments e OfficeAssignment

As propriedades `CourseAssignments` e `OfficeAssignment` são propriedades de navegação.

Um instrutor pode ministrar qualquer quantidade de cursos e, portanto, `CourseAssignments` é definido como uma coleção.

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

Se uma propriedade de navegação pode conter várias entidades, o tipo precisa ser uma lista na qual as entradas podem ser adicionadas, excluídas e atualizadas.  Especifique `ICollection<T>` ou um tipo, como `List<T>` ou `HashSet<T>`. Se você especificar `ICollection<T>`, o EF criará uma coleção `HashSet<T>` por padrão.

O motivo pelo qual elas são entidades `CourseAssignment` é explicado abaixo na seção sobre relações muitos para muitos.

As regras de negócio do Contoso Universidade indicam que um instrutor pode ter apenas, no máximo, um escritório; portanto, a propriedade `OfficeAssignment` contém uma única entidade OfficeAssignment (que pode ser nulo se o escritório não está atribuído).

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-officeassignment-entity"></a>Criar a entidade OfficeAssignment

![Entidade OfficeAssignment](complex-data-model/_static/officeassignment-entity.png)

Crie *Models/OfficeAssignment.cs* com o seguinte código:

[!code-csharp[](intro/samples/cu/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a>O atributo Key

Há uma relação um para zero ou um entre as entidades Instructor e OfficeAssignment. Uma atribuição de escritório existe apenas em relação ao instrutor ao qual ela é atribuída e, portanto, sua chave primária também é a chave estrangeira da entidade Instructor. Mas o Entity Framework não pode reconhecer InstructorID automaticamente como a chave primária dessa entidade porque o nome não segue a convenção de nomenclatura ID ou classnameID. Portanto, o atributo `Key` é usado para identificá-la como a chave:

```csharp
[Key]
public int InstructorID { get; set; }
```

Você também pode usar o atributo `Key` se a entidade tem sua própria chave primária, mas você deseja atribuir um nome de propriedade que não seja classnameID ou ID.

Por padrão, o EF trata a chave como não gerada pelo banco de dados porque a coluna destina-se a uma relação de identificação.

### <a name="the-instructor-navigation-property"></a>A propriedade de navegação Instructor

A entidade Instructor tem uma propriedade de navegação `OfficeAssignment` que permite valor nulo (porque um instrutor pode não ter uma atribuição de escritório), e a entidade OfficeAssignment tem uma propriedade de navegação `Instructor` que não permite valor nulo (porque uma atribuição de escritório não pode existir sem um instrutor – `InstructorID` não permite valor nulo). Quando uma entidade Instructor tiver uma entidade OfficeAssignment relacionada, cada entidade terá uma referência à outra em sua propriedade de navegação.

Você pode colocar um atributo `[Required]` na propriedade de navegação Instructor para especificar que deve haver um instrutor relacionado, mas não precisa fazer isso porque a chave estrangeira `InstructorID` (que também é a chave para esta tabela) não permite valor nulo.

## <a name="modify-course-entity"></a>Modificar a entidade Course

![Entidade Course](complex-data-model/_static/course-entity.png)

Em *Models/Course.cs*, substitua o código que você adicionou anteriormente pelo código a seguir. As alterações são realçadas.

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

A entidade de curso tem uma propriedade de chave estrangeira `DepartmentID` que aponta para a entidade Department relacionada e ela tem uma propriedade de navegação `Department`.

O Entity Framework não exige que você adicione uma propriedade de chave estrangeira ao modelo de dados quando você tem uma propriedade de navegação para uma entidade relacionada.  O EF cria chaves estrangeiras no banco de dados sempre que elas são necessárias e cria automaticamente [propriedades de sombra](/ef/core/modeling/shadow-properties) para elas. No entanto, ter a chave estrangeira no modelo de dados pode tornar as atualizações mais simples e mais eficientes. Por exemplo, quando você busca uma entidade de curso a ser editada, a entidade Department é nula se você não carregá-la; portanto, quando você atualiza a entidade de curso, você precisa primeiro buscar a entidade Department. Quando a propriedade de chave estrangeira `DepartmentID` está incluída no modelo de dados, você não precisa buscar a entidade Department antes da atualização.

### <a name="the-databasegenerated-attribute"></a>O atributo DatabaseGenerated

O atributo `DatabaseGenerated` com o parâmetro `None` na propriedade `CourseID` especifica que os valores de chave primária são fornecidos pelo usuário, em vez de serem gerados pelo banco de dados.

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

Por padrão, o Entity Framework pressupõe que os valores de chave primária sejam gerados pelo banco de dados. É isso que você quer na maioria dos cenários. No entanto, para entidades Course, você usará um número de curso especificado pelo usuário como uma série 1000 de um departamento, uma série 2000 para outro departamento e assim por diante.

O atributo `DatabaseGenerated` também pode ser usado para gerar valores padrão, como no caso de colunas de banco de dados usadas para registrar a data em que uma linha foi criada ou atualizada.  Para obter mais informações, consulte [Propriedades geradas](/ef/core/modeling/generated-properties).

### <a name="foreign-key-and-navigation-properties"></a>Propriedades de navegação e de chave estrangeira

As propriedades de navegação e de chave estrangeira na entidade Course refletem as seguintes relações:

Um curso é atribuído a um departamento e, portanto, há uma propriedade de chave estrangeira `DepartmentID` e de navegação `Department` pelas razões mencionadas acima.

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

Um curso pode ter qualquer quantidade de estudantes inscritos; portanto, a propriedade de navegação `Enrollments` é uma coleção:

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

Um curso pode ser ministrado por vários instrutores; portanto, a propriedade de navegação `CourseAssignments` é uma coleção (o tipo `CourseAssignment` é explicado [posteriormente](#many-to-many-relationships)):

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

## <a name="create-department-entity"></a>Criar a entidade Department

![Entidade Department](complex-data-model/_static/department-entity.png)

Crie *Models/Department.cs* com o seguinte código:

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a>O atributo Column

Anteriormente, você usou o atributo `Column` para alterar o mapeamento de nome de coluna. No código da entidade Department, o atributo `Column` está sendo usado para alterar o mapeamento de tipo de dados SQL, do modo que a coluna seja definida usando o tipo de dinheiro do SQL Server no banco de dados:

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

O mapeamento de coluna geralmente não é necessário, pois o Entity Framework escolhe o tipo de dados do SQL Server apropriado com base no tipo CLR definido para a propriedade. O tipo `decimal` CLR é mapeado para um tipo `decimal` SQL Server. Mas, nesse caso, você sabe que a coluna armazenará os valores de moeda e o tipo de dados dinheiro é mais apropriado para isso.

### <a name="foreign-key-and-navigation-properties"></a>Propriedades de navegação e de chave estrangeira

As propriedades de navegação e de chave estrangeira refletem as seguintes relações:

Um departamento pode ou não ter um administrador, e um administrador é sempre um instrutor. Portanto, a propriedade `InstructorID` é incluída como a chave estrangeira na entidade Instructor e um ponto de interrogação é adicionado após a designação de tipo `int` para marcar a propriedade como uma propriedade que permite valor nulo. A propriedade de navegação é chamada `Administrator`, mas contém uma entidade Instructor:

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

Um departamento pode ter vários cursos e, portanto, há uma propriedade de navegação Courses:

```csharp
public ICollection<Course> Courses { get; set; }
```

> [!NOTE]
> Por convenção, o Entity Framework habilita a exclusão em cascata para chaves estrangeiras que não permitem valor nulo e em relações muitos para muitos. Isso pode resultar em regras de exclusão em cascata circular, que causará uma exceção quando você tentar adicionar uma migração. Por exemplo, se você não definiu a propriedade Department.InstructorID como uma propriedade que permite valor nulo, o EF configura uma regra de exclusão em cascata para excluir o departamento quando você exclui o instrutor, que não é o que você deseja que aconteça. Se as regras de negócio exigissem que a propriedade `InstructorID` não permitisse valor nulo, você precisaria usar a seguinte instrução de API fluente para desabilitar a exclusão em cascata na relação:
>
> ```csharp
> modelBuilder.Entity<Department>()
>    .HasOne(d => d.Administrator)
>    .WithMany()
>    .OnDelete(DeleteBehavior.Restrict)
> ```

## <a name="modify-enrollment-entity"></a>Modificar a entidade Enrollment

![Entidade Enrollment](complex-data-model/_static/enrollment-entity.png)

Em *Models/Enrollment.cs*, substitua o código que você adicionou anteriormente pelo seguinte código:

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a>Propriedades de navegação e de chave estrangeira

As propriedades de navegação e de chave estrangeira refletem as seguintes relações:

Um registro destina-se a um único curso e, portanto, há uma propriedade de chave estrangeira `CourseID` e uma propriedade de navegação `Course`:

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

Um registro destina-se a um único aluno e, portanto, há uma propriedade de chave estrangeira `StudentID` e uma propriedade de navegação `Student`:

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a>Relações muitos para muitos

Há uma relação muitos para muitos entre as entidades Student e Course e a entidade Enrollment funciona como uma tabela de junção muitos para muitos *com conteúdo* no banco de dados. "Com conteúdo" significa que a tabela Registro contém dados adicionais além das chaves estrangeiras para as tabelas unidas (nesse caso, uma chave primária e uma propriedade Grade).

A ilustração a seguir mostra a aparência dessas relações em um diagrama de entidades. (Esse diagrama foi gerado usando o Entity Framework Power Tools para o EF 6.x; a criação do diagrama não faz parte do tutorial, mas está apenas sendo usada aqui como uma ilustração.)

![Relação muitos para muitos de Student-Course](complex-data-model/_static/student-course.png)

Cada linha de relação tem um 1 em uma extremidade e um asterisco (*) na outra, indicando uma relação um para muitos.

Se a tabela Registro não incluir informações de nota, ela apenas precisará conter as duas chaves estrangeiras CourseID e StudentID. Nesse caso, ela será uma tabela de junção de muitos para muitos sem conteúdo (ou uma tabela de junção pura) no banco de dados. As entidades Instructor e Course têm esse tipo de relação muitos para muitos e a próxima etapa é criar uma classe de entidade para funcionar como uma tabela de junção sem conteúdo.

(O EF 6.x é compatível com tabelas de junção implícita para relações muitos para muitos, ao contrário do EF Core. Para obter mais informações, confira a [discussão no repositório GitHub do EF Core](https://github.com/aspnet/EntityFramework/issues/1368).)

## <a name="the-courseassignment-entity"></a>A entidade CourseAssignment

![Entidade CourseAssignment](complex-data-model/_static/courseassignment-entity.png)

Crie *Models/CourseAssignment.cs* com o seguinte código:

[!code-csharp[](intro/samples/cu/Models/CourseAssignment.cs)]

### <a name="join-entity-names"></a>Unir nomes de entidade

Uma tabela de junção é necessária no banco de dados para a relação muitos para muitos de Instrutor para Cursos, e ela precisa ser representada por um conjunto de entidades. É comum nomear uma entidade de junção `EntityName1EntityName2`, que, nesse caso, será `CourseInstructor`. No entanto, recomendamos que você escolha um nome que descreve a relação. Modelos de dados começam simples e aumentam, com junções não referentes a conteúdo obtendo com frequência o conteúdo mais tarde. Se você começar com um nome descritivo de entidade, não precisará alterar o nome posteriormente. O ideal é que a entidade de junção tenha seu próprio nome natural (possivelmente, uma única palavra) no domínio de negócios. Por exemplo, Manuais e Clientes podem ser vinculados por meio de Classificações. Para essa relação, `CourseAssignment` é uma escolha melhor que `CourseInstructor`.

### <a name="composite-key"></a>Chave composta

Como as chaves estrangeiras não permitem valor nulo e, juntas, identificam exclusivamente cada linha da tabela, não é necessário ter uma chave primária. As propriedades *InstructorID* e *CourseID* devem funcionar como uma chave primária composta. A única maneira de identificar chaves primárias compostas no EF é usando a *API fluente* (isso não pode ser feito por meio de atributos). Você verá como configurar a chave primária composta na próxima seção.

A chave composta garante que, embora você possa ter várias linhas para um curso e várias linhas para um instrutor, não poderá ter várias linhas para o mesmo instrutor e curso. A entidade de junção `Enrollment` define sua própria chave primária e, portanto, duplicatas desse tipo são possíveis. Para evitar essas duplicatas, você pode adicionar um índice exclusivo nos campos de chave estrangeira ou configurar `Enrollment` com uma chave primária composta semelhante a `CourseAssignment`. Para obter mais informações, consulte [Índices](/ef/core/modeling/indexes).

## <a name="update-the-database-context"></a>Atualizar o contexto de banco de dados

Adicione o seguinte código realçado ao arquivo *Data/SchoolContext.cs*:

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

Esse código adiciona novas entidades e configura a chave primária composta da entidade CourseAssignment.

## <a name="about-a-fluent-api-alternative"></a>Sobre a alternativa de API fluente

O código no método `OnModelCreating` da classe `DbContext` usa a *API fluente* para configurar o comportamento do EF. A API é chamada "fluente" porque costuma ser usada pelo encadeamento de uma série de chamadas de método em uma única instrução, como neste exemplo da [documentação do EF Core](/ef/core/modeling/#methods-of-configuration):

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

Neste tutorial, você usa a API fluente somente para o mapeamento de banco de dados que não é possível fazer com atributos. No entanto, você também pode usar a API fluente para especificar a maioria das regras de formatação, validação e mapeamento que pode ser feita por meio de atributos. Alguns atributos como `MinimumLength` não podem ser aplicados com a API fluente. Conforme mencionado anteriormente, `MinimumLength` não altera o esquema; apenas aplica uma regra de validação do lado do cliente e do servidor.

Alguns desenvolvedores preferem usar a API fluente exclusivamente para que possam manter suas classes de entidade "limpas". Combine atributos e a API fluente se desejar. Além disso, há algumas personalizações que podem ser feitas apenas com a API fluente, mas em geral, a prática recomendada é escolher uma dessas duas abordagens e usar isso com o máximo de consistência possível. Se usar as duas, observe que sempre que houver um conflito, a API fluente substituirá atributos.

Para obter mais informações sobre atributos vs. API fluente, consulte [Métodos de configuração](/ef/core/modeling/#methods-of-configuration).

## <a name="entity-diagram-showing-relationships"></a>Diagrama de entidade mostrando relações

A ilustração a seguir mostra o diagrama criado pelo Entity Framework Power Tools para o modelo Escola concluído.

![Diagrama de entidade](complex-data-model/_static/diagram.png)

Além das linhas de relação um-para-muitos (1 para \*), você pode ver a linha de relação um para zero ou um (1 para 0..1) entre as entidades Instructor e OfficeAssignment e a linha de relação zero-ou-um-para-muitos (0..1 para *) entre as entidades Instructor e Department.

## <a name="seed-database-with-test-data"></a>Propagar o banco de dados com dados de teste

Substitua o código no arquivo *Data/DbInitializer.cs* pelo código a seguir para fornecer dados de semente para as novas entidades criadas.

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Final)]

Como você viu no primeiro tutorial, a maioria do código apenas cria novos objetos de entidade e carrega dados de exemplo em propriedades, conforme necessário, para teste. Observe como as relações muitos para muitos são tratadas: o código cria relações com a criação de entidades nos conjuntos de entidades de junção `Enrollments` e `CourseAssignment`.

## <a name="add-a-migration"></a>Adicionar uma migração

Salve as alterações e compile o projeto. Em seguida, abra a janela Comando na pasta do projeto e insira o comando `migrations add` (não execute ainda o comando de atualização de banco de dados):

```console
dotnet ef migrations add ComplexDataModel
```

Você receberá um aviso sobre a possível perda de dados.

```text
An operation was scaffolded that may result in the loss of data. Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

Se você tiver tentado executar o comando `database update` neste ponto (não faça isso ainda), receberá o seguinte erro:

> A instrução ALTER TABLE entrou em conflito com a restrição FOREIGN KEY "FK_dbo.Course_dbo.Department_DepartmentID". O conflito ocorreu no banco de dados "ContosoUniversity", tabela "dbo.Departamento", coluna 'DepartmentID'.

Às vezes, quando você executa migrações com os dados existentes, precisa inserir dados de stub no banco de dados para atender às restrições de chave estrangeira. O código gerado no método `Up` adiciona uma chave estrangeira DepartmentID que não permite valor nulo para a tabela Curso. Se já houver linhas na tabela Curso quando o código for executado, a operação `AddColumn` falhará, porque o SQL Server não saberá qual valor deve ser colocado na coluna que não pode ser nulo. Para este tutorial, você executará a migração em um novo banco de dados, mas em um aplicativo de produção, você precisará fazer com que a migração manipule os dados existentes. Portanto, as instruções a seguir mostram um exemplo de como fazer isso.

Para fazer a migração funcionar com os dados existentes, você precisa alterar o código para fornecer à nova coluna um valor padrão e criar um departamento de stub chamado "Temp" para atuar como o departamento padrão. Como resultado, as linhas Curso existentes serão todas relacionadas ao departamento "Temp" após a execução do método `Up`.

* Abra o arquivo *{timestamp}_ComplexDataModel.cs*.

* Comente a linha de código que adiciona a coluna DepartmentID à tabela Curso.

  [!code-csharp[](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

* Adicione o seguinte código realçado após o código que cria a tabela Departamento:

  [!code-csharp[](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

Em um aplicativo de produção, você escreverá código ou scripts para adicionar linhas Departamento e relacionar linhas Curso às novas linhas Departamento. Em seguida, você não precisará mais do departamento "Temp" ou do valor padrão na coluna Course.DepartmentID.

Salve as alterações e compile o projeto.

## <a name="change-the-connection-string"></a>Alterar a cadeia de conexão

Agora, você tem novo código na classe `DbInitializer` que adiciona dados de semente para as novas entidades a um banco de dados vazio. Para fazer com que o EF crie um novo banco de dados vazio, altere o nome do banco de dados na cadeia de conexão em *appsettings.json* para ContosoUniversity3 ou para outro nome que você ainda não usou no computador que está sendo usado.

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=ContosoUniversity3;Trusted_Connection=True;MultipleActiveResultSets=true"
  },
```

Salve as alterações em *appsettings.json*.

> [!NOTE]
> Como alternativa à alteração do nome do banco de dados, você pode excluir o banco de dados. Use o **SSOX** (Pesquisador de Objetos do SQL Server) ou o comando `database drop` da CLI:
>
> ```console
> dotnet ef database drop
> ```

## <a name="update-the-database"></a>Atualizar o banco de dados

Depois que você tiver alterado o nome do banco de dados ou excluído o banco de dados, execute o comando `database update` na janela Comando para executar as migrações.

```console
dotnet ef database update
```

Execute o aplicativo para fazer com que o método `DbInitializer.Initialize` execute e popule o novo banco de dados.

Abra o banco de dados no SSOX, como você fez anteriormente, e expanda o nó **Tabelas** para ver se todas as tabelas foram criadas. (Se você ainda tem o SSOX aberto do momento anterior, clique no botão **Atualizar**.)

![Tabelas no SSOX](complex-data-model/_static/ssox-tables.png)

Execute o aplicativo para disparar o código inicializador que propaga o banco de dados.

Clique com o botão direito do mouse na tabela **CourseAssignment** e selecione **Exibir Dados** para verificar se existem dados nela.

![Dados de CourseAssignment no SSOX](complex-data-model/_static/ssox-ci-data.png)

## <a name="get-the-code"></a>Obter o código

[Baixe ou exiba o aplicativo concluído.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você:

> [!div class="checklist"]
> * Personalizou o Modelo de dados
> * Fez alterações na entidade Student
> * Criou a entidade Instructor
> * Criou a entidade OfficeAssignment
> * Modificou a entidade Course
> * Criou a entidade Department
> * Modificou a entidade Enrollment
> * Atualizou o contexto de banco de dados
> * Propagou o banco de dados com os dados de teste
> * Adicionou uma migração
> * Alterou a cadeia de conexão
> * Atualizou o banco de dados

Vá para o próximo tutorial para aprender a acessar dados relacionados.

> [!div class="nextstepaction"]
> [Avançar: Acessar dados relacionados](read-related-data.md)
