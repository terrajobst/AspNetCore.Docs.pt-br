---
title: Páginas Razor com o EF Core no ASP.NET Core – Modelo de dados – 5 de 8
author: rick-anderson
description: Neste tutorial, você adiciona mais entidades e relações e personaliza o modelo de dados especificando formatação, validação e regras de mapeamento.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: data/ef-rp/complex-data-model
ms.openlocfilehash: f995f3f74da4910de518af875eb89349a8457573
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67813708"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---data-model---5-of-8"></a>Páginas Razor com o EF Core no ASP.NET Core – Modelo de dados – 5 de 8

Por [Tom Dykstra](https://github.com/tdykstra) e [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

Os tutoriais anteriores trabalharam com um modelo de dados básico composto por três entidades. Neste tutorial:

* Mais entidades e relações são adicionadas.
* O modelo de dados é personalizado com a especificação das regras de formatação, validação e mapeamento de banco de dados.

As classes de entidade para o modelo de dados concluído são mostradas na seguinte ilustração:

![Diagrama de entidade](complex-data-model/_static/diagram.png)

Caso tenha problemas que não consiga resolver, baixe o [aplicativo concluído](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).

## <a name="customize-the-data-model-with-attributes"></a>Personalizar o modelo de dados com atributos

Nesta seção, o modelo de dados é personalizado com atributos.

### <a name="the-datatype-attribute"></a>O atributo DataType

As páginas de alunos atualmente exibem a hora da data de registro. Normalmente, os campos de data mostram apenas a data e não a hora.

Atualize *Models/Student.cs* com o seguinte código realçado:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

O atributo [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) especifica um tipo de dados mais específico do que o tipo intrínseco de banco de dados. Neste caso, apenas a data deve ser exibida, não a data e a hora. A [Enumeração DataType](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) fornece muitos tipos de dados, como Date, Time, PhoneNumber, Currency, EmailAddress, etc. O atributo `DataType` também pode permitir que o aplicativo forneça automaticamente recursos específicos a um tipo. Por exemplo:

* O link `mailto:` é criado automaticamente para `DataType.EmailAddress`.
* O seletor de data é fornecido para `DataType.Date` na maioria dos navegadores.

O atributo `DataType` emite atributos `data-` HTML 5 (pronunciados “data dash”) que são consumidos pelos navegadores HTML 5. Os atributos `DataType` não fornecem validação.

`DataType.Date` não especifica o formato da data exibida. Por padrão, o campo de dados é exibido de acordo com os formatos padrão com base nas [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support) do servidor.

O atributo `DisplayFormat` é usado para especificar explicitamente o formato de data:

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

A configuração `ApplyFormatInEditMode` especifica que a formatação também deve ser aplicada à interface do usuário de edição. Alguns campos não devem usar `ApplyFormatInEditMode`. Por exemplo, o símbolo de moeda geralmente não deve ser exibido em uma caixa de texto de edição.

O atributo `DisplayFormat` pode ser usado por si só. Geralmente, é uma boa ideia usar o atributo `DataType` com o atributo `DisplayFormat`. O atributo `DataType` transmite a semântica dos dados em vez de como renderizá-los em uma tela. O atributo `DataType` oferece os seguintes benefícios que não estão disponíveis em `DisplayFormat`:

* O navegador pode habilitar recursos do HTML5. Por exemplo, mostra um controle de calendário, o símbolo de moeda apropriado à localidade, links de email, validação de entrada do lado do cliente, etc.
* Por padrão, o navegador renderiza os dados usando o formato correto de acordo com a localidade.

Para obter mais informações, consulte a [documentação do Auxiliar de Marcação \<input>](xref:mvc/views/working-with-forms#the-input-tag-helper).

Execute o aplicativo. Navegue para a página Índice de Alunos. As horas não são mais exibidas. Cada exibição que usa o modelo `Student` exibe a data sem a hora.

![Página Índice de Alunos mostrando datas sem horas](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a>O atributo StringLength

Regras de validação de dados e mensagens de erro de validação podem ser especificadas com atributos. O atributo [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) especifica o tamanho mínimo e máximo de caracteres permitidos em um campo de dados. O atributo `StringLength` também fornece a validação do lado do cliente e do servidor. O valor mínimo não tem impacto sobre o esquema de banco de dados.

Atualize o modelo `Student` com o seguinte código:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

O código anterior limita os nomes a, no máximo, 50 caracteres. O atributo `StringLength` não impede que um usuário insira um espaço em branco em um nome. O atributo [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) é usado para aplicar restrições à entrada. Por exemplo, o seguinte código exige que o primeiro caractere esteja em maiúscula e os caracteres restantes estejam em ordem alfabética:

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

Execute o aplicativo:

* Navegue para a página Alunos.
* Selecione **Criar Novo** e insira um nome com mais de 50 caracteres.
* Selecione **Criar** e a validação do lado do cliente mostrará uma mensagem de erro.

![Página Índice de Alunos mostrando erros de tamanho de cadeia de caracteres](complex-data-model/_static/string-length-errors.png)

No **SSOX** (Pesquisador de Objetos do SQL Server), abra o designer de tabela Aluno clicando duas vezes na tabela **Aluno**.

![Tabela Alunos no SSOX antes das migrações](complex-data-model/_static/ssox-before-migration.png)

A imagem anterior mostra o esquema para a tabela `Student`. Os campos de nome têm o tipo `nvarchar(MAX)` porque as migrações não foram executadas no BD. Quando as migrações forem executadas mais adiante neste tutorial, os campos de nome se tornarão `nvarchar(50)`.

### <a name="the-column-attribute"></a>O atributo Column

Os atributos podem controlar como as classes e propriedades são mapeadas para o banco de dados. Nesta seção, o atributo `Column` é usado para mapear o nome da propriedade `FirstMidName` como "FirstName" no BD.

Quando o BD é criado, os nomes de propriedade no modelo são usados para nomes de coluna (exceto quando o atributo `Column` é usado).

O modelo `Student` usa `FirstMidName` para o campo de nome porque o campo também pode conter um sobrenome.

Atualize o arquivo *Student.cs* com o seguinte código:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Column&highlight=4,14)]

Com a alteração anterior, `Student.FirstMidName` no aplicativo é mapeado para a coluna `FirstName` da tabela `Student`.

A adição do atributo `Column` altera o modelo que dá suporte ao `SchoolContext`. O modelo que dá suporte ao `SchoolContext` não corresponde mais ao banco de dados. Se o aplicativo for executado antes da aplicação das migrações, a seguinte exceção será gerada:

```SQL
SqlException: Invalid column name 'FirstName'.
```

Para atualizar o BD:

* Compile o projeto.
* Abra uma janela Comando na pasta do projeto. Insira os seguintes comandos para criar uma nova migração e atualizar o BD:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

```PMC
Add-Migration ColumnFirstName
Update-Database
```

# <a name="net-core-clitabnetcore-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

```console
dotnet ef migrations add ColumnFirstName
dotnet ef database update
```

---

O comando `migrations add ColumnFirstName` gera a seguinte mensagem de aviso:

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
```

O aviso é gerado porque os campos de nome agora estão limitados a 50 caracteres. Se um nome no BD tiver mais de 50 caracteres, o 51º caractere até o último caractere serão perdidos.

* Teste o aplicativo.

Abra a tabela Alunos no SSOX:

![Tabela Alunos no SSOX após as migrações](complex-data-model/_static/ssox-after-migration.png)

Antes de a migração ser aplicada, as colunas de nome eram do tipo [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql). As colunas de nome agora são `nvarchar(50)`. O nome da coluna foi alterado de `FirstMidName` para `FirstName`.

> [!Note]
> Na seção a seguir, a criação do aplicativo em alguns estágios gera erros do compilador. As instruções especificam quando compilar o aplicativo.

## <a name="student-entity-update"></a>Atualização da entidade Student

![Entidade Student](complex-data-model/_static/student-entity.png)

Atualize *Models/Student.cs* com o seguinte código:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a>O atributo Required

O atributo `Required` torna as propriedades de nome campos obrigatórios. O atributo `Required` não é necessário para tipos que não permitem valor nulo, como tipos de valor (`DateTime`, `int`, `double`, etc.). Tipos que não podem ser nulos são tratados automaticamente como campos obrigatórios.

O atributo `Required` pode ser substituído por um parâmetro de tamanho mínimo no atributo `StringLength`:

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a>O atributo Display

O atributo `Display` especifica que a legenda para as caixas de texto deve ser "Nome", "Sobrenome", "Nome Completo" e "Data de Registro". As legendas padrão não tinham nenhum espaço entre as palavras, por exemplo, "Lastname".

### <a name="the-fullname-calculated-property"></a>A propriedade calculada FullName

`FullName` é uma propriedade calculada que retorna um valor criado pela concatenação de duas outras propriedades. `FullName` não pode ser definido; ele apenas tem um acessador get. Nenhuma coluna `FullName` é criada no banco de dados.

## <a name="create-the-instructor-entity"></a>Criar a entidade Instructor

![Entidade Instructor](complex-data-model/_static/instructor-entity.png)

Crie *Models/Instructor.cs* com o seguinte código:

[!code-csharp[](intro/samples/cu21/Models/Instructor.cs)]

Vários atributos podem estar em uma linha. Os atributos `HireDate` podem ser escritos da seguinte maneira:

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a>As propriedades de navegação CourseAssignments e OfficeAssignment

As propriedades `CourseAssignments` e `OfficeAssignment` são propriedades de navegação.

Um instrutor pode ministrar qualquer quantidade de cursos e, portanto, `CourseAssignments` é definido como uma coleção.

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

Se uma propriedade de navegação armazenar várias entidades:

* Ele deve ser um tipo de lista no qual as entradas possam ser adicionadas, excluídas e atualizadas.

Os tipos de propriedade de navegação incluem:

* `ICollection<T>`
* `List<T>`
* `HashSet<T>`

Se `ICollection<T>` for especificado, o EF Core criará uma coleção `HashSet<T>` por padrão.

A entidade `CourseAssignment` é explicada na seção sobre relações muitos para muitos.

Regras de negócio do Contoso University indicam que um instrutor pode ter, no máximo, um escritório. A propriedade `OfficeAssignment` contém uma única entidade `OfficeAssignment`. `OfficeAssignment` será nulo se nenhum escritório for atribuído.

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-the-officeassignment-entity"></a>Criar a entidade OfficeAssignment

![Entidade OfficeAssignment](complex-data-model/_static/officeassignment-entity.png)

Crie *Models/OfficeAssignment.cs* com o seguinte código:

[!code-csharp[](intro/samples/cu21/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a>O atributo Key

O atributo `[Key]` é usado para identificar uma propriedade como a PK (chave primária) quando o nome da propriedade é algo diferente de classnameID ou ID.

Há uma relação um para zero ou um entre as entidades `Instructor` e `OfficeAssignment`. Uma atribuição de escritório existe apenas em relação ao instrutor ao qual ela é atribuída. A PK `OfficeAssignment` também é a FK (chave estrangeira) da entidade `Instructor`. O EF Core não pode reconhecer `InstructorID` automaticamente como o PK de `OfficeAssignment` porque:

* `InstructorID` não segue a convenção de nomenclatura de ID nem de classnameID.

Portanto, o atributo `Key` é usado para identificar `InstructorID` como a PK:

```csharp
[Key]
public int InstructorID { get; set; }
```

Por padrão, o EF Core trata a chave como não gerada pelo banco de dados porque a coluna destina-se a uma relação de identificação.

### <a name="the-instructor-navigation-property"></a>A propriedade de navegação Instructor

A propriedade de navegação `OfficeAssignment` da entidade `Instructor` permite valor nulo porque:

* Tipos de referência (como classes que permitem valor nulo).
* Um instrutor pode não ter uma atribuição de escritório.

A entidade `OfficeAssignment` tem uma propriedade de navegação `Instructor` que não permite valor nulo porque:

* `InstructorID` não permite valor nulo.
* Uma atribuição de escritório não pode existir sem um instrutor.

Quando uma entidade `Instructor` tem uma entidade `OfficeAssignment` relacionada, cada entidade tem uma referência à outra em sua propriedade de navegação.

O atributo `[Required]` pode ser aplicado à propriedade de navegação `Instructor`:

```csharp
[Required]
public Instructor Instructor { get; set; }
```

O código anterior especifica que deve haver um instrutor relacionado. O código anterior é desnecessário porque a chave estrangeira `InstructorID` (que também é a PK) não permite valor nulo.

## <a name="modify-the-course-entity"></a>Modificar a entidade Course

![Entidade Course](complex-data-model/_static/course-entity.png)

Atualize *Models/Course.cs* com o seguinte código:

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

A entidade `Course` tem uma propriedade de FK (chave estrangeira) `DepartmentID`. `DepartmentID` aponta para a entidade `Department` relacionada. A entidade `Course` tem uma propriedade de navegação `Department`.

O EF Core não exige uma propriedade de FK para um modelo de dados quando o modelo tem uma propriedade de navegação para uma entidade relacionada.

O EF Core cria automaticamente FKs no banco de dados sempre que forem necessárias. O EF Core cria [propriedades de sombra](/ef/core/modeling/shadow-properties) para FKs criadas automaticamente. Ter a FK no modelo de dados pode tornar as atualizações mais simples e mais eficientes. Por exemplo, considere um modelo em que a propriedade de FK `DepartmentID` *não* é incluída. Quando uma entidade de curso é buscada para editar:

* A entidade `Department` será nula se não for carregada de forma explícita.
* Para atualizar a entidade de curso, a entidade `Department` primeiro deve ser buscada.

Quando a propriedade de FK `DepartmentID` está incluída no modelo de dados, não é necessário buscar a entidade `Department` antes de uma atualização.

### <a name="the-databasegenerated-attribute"></a>O atributo DatabaseGenerated

O atributo `[DatabaseGenerated(DatabaseGeneratedOption.None)]` especifica que a PK é fornecida pelo aplicativo em vez de ser gerada pelo banco de dados.

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

Por padrão, o EF Core supõe que os valores de PK sejam gerados pelo BD. Os valores de PK gerados pelo BD geralmente são a melhor abordagem. Para entidades `Course`, o usuário especifica o PK. Por exemplo, um número de curso, como uma série 1000 para o departamento de matemática e uma série 2000 para o departamento em inglês.

O atributo `DatabaseGenerated` também pode ser usado para gerar valores padrão. Por exemplo, o BD pode gerar automaticamente um campo de data para registrar a data em que uma linha foi criada ou atualizada. Para obter mais informações, consulte [Propriedades geradas](/ef/core/modeling/generated-properties).

### <a name="foreign-key-and-navigation-properties"></a>Propriedades de navegação e de chave estrangeira

As propriedades de navegação e de FK (chave estrangeira) na entidade `Course` refletem as seguintes relações:

Um curso é atribuído a um departamento; portanto, há uma FK `DepartmentID` e uma propriedade de navegação `Department`.

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

Um curso pode ter qualquer quantidade de estudantes inscritos; portanto, a propriedade de navegação `Enrollments` é uma coleção:

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

Um curso pode ser ministrado por vários instrutores; portanto, a propriedade de navegação `CourseAssignments` é uma coleção:

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

`CourseAssignment` é explicado [posteriormente](#many-to-many-relationships).

## <a name="create-the-department-entity"></a>Criar a entidade Department

![Entidade Department](complex-data-model/_static/department-entity.png)

Crie *Models/Department.cs* com o seguinte código:

[!code-csharp[](intro/samples/cu21/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a>O atributo Column

Anteriormente, o atributo `Column` foi usado para alterar o mapeamento de nome de coluna. No código da entidade `Department`, o atributo `Column` é usado para alterar o mapeamento de tipo de dados SQL. A coluna `Budget` é definida usando o tipo de dinheiro do SQL Server no BD:

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

Em geral, o mapeamento de coluna não é necessário. Em geral, o EF Core escolhe o tipo de dados do SQL Server apropriado com base no tipo CLR da propriedade. O tipo `decimal` CLR é mapeado para um tipo `decimal` SQL Server. `Budget` refere-se à moeda e o tipo de dados de dinheiro é mais apropriado para moeda.

### <a name="foreign-key-and-navigation-properties"></a>Propriedades de navegação e de chave estrangeira

As propriedades de navegação e de FK refletem as seguintes relações:

* Um departamento pode ou não ter um administrador.
* Um administrador é sempre um instrutor. Portanto, a propriedade `InstructorID` está incluída como a FK da entidade `Instructor`.

A propriedade de navegação é chamada `Administrator`, mas contém uma entidade `Instructor`:

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

O ponto de interrogação (?) no código anterior especifica que a propriedade permite valor nulo.

Um departamento pode ter vários cursos e, portanto, há uma propriedade de navegação Courses:

```csharp
public ICollection<Course> Courses { get; set; }
```

Observação: por convenção, o EF Core habilita a exclusão em cascata em FKs que não permitem valor nulo e em relações muitos para muitos. A exclusão em cascata pode resultar em regras de exclusão em cascata circular. As regras de exclusão em cascata circular causam uma exceção quando uma migração é adicionada.

Por exemplo, se a propriedade `Department.InstructorID` tiver sido definida como não anulável:

* O EF Core configura uma regra de exclusão em cascata para excluir o departamento quando o instrutor é excluído.
* Excluir o departamento quando o instrutor é excluído não é o comportamento desejado.
* A seguinte API fluente definiria uma regra de restrição em vez de em cascata.

   ```csharp
   modelBuilder.Entity<Department>()
      .HasOne(d => d.Administrator)
      .WithMany()
      .OnDelete(DeleteBehavior.Restrict)
  ```

O código anterior desabilita a exclusão em cascata na relação departamento-instrutor.

## <a name="update-the-enrollment-entity"></a>Atualizar a entidade Enrollment

Um registro se refere a um curso feito por um aluno.

![Entidade Enrollment](complex-data-model/_static/enrollment-entity.png)

Atualize *Models/Enrollment.cs* com o seguinte código:

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a>Propriedades de navegação e de chave estrangeira

As propriedades de navegação e de FK refletem as seguintes relações:

Um registro destina-se a um curso e, portanto, há uma propriedade de FK `CourseID` e uma propriedade de navegação `Course`:

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

Um registro destina-se a um aluno e, portanto, há uma propriedade de FK `StudentID` e uma propriedade de navegação `Student`:

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a>Relações muitos para muitos

Há uma relação muitos para muitos entre as entidades `Student` e `Course`. A entidade `Enrollment` funciona como uma tabela de junção muitos para muitos *com conteúdo* no banco de dados. "Com conteúdo" significa que a tabela `Enrollment` contém dados adicionais além das FKs das tabelas unidas (nesse caso, a FK e `Grade`).

A ilustração a seguir mostra a aparência dessas relações em um diagrama de entidades. (Esse diagrama foi gerado com o [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) para EF 6.x. A criação do diagrama não faz parte do tutorial.)

![Relação muitos para muitos de Student-Course](complex-data-model/_static/student-course.png)

Cada linha de relação tem um 1 em uma extremidade e um asterisco (*) na outra, indicando uma relação um para muitos.

Se a tabela `Enrollment` não incluir informações de nota, ela apenas precisará conter as duas FKs (`CourseID` e `StudentID`). Uma tabela de junção muitos para muitos sem conteúdo é às vezes chamada de PJT (uma tabela de junção pura).

As entidades `Instructor` e `Course` têm uma relação muitos para muitos usando uma tabela de junção pura.

Observação: O EF 6.x é compatível com tabelas de junção implícita para relações muitos para muitos, ao contrário do EF Core. Para obter mais informações, consulte [Relações muitos para muitos no EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).

## <a name="the-courseassignment-entity"></a>A entidade CourseAssignment

![Entidade CourseAssignment](complex-data-model/_static/courseassignment-entity.png)

Crie *Models/CourseAssignment.cs* com o seguinte código:

[!code-csharp[](intro/samples/cu21/Models/CourseAssignment.cs)]

### <a name="instructor-to-courses"></a>Instrutor para Cursos

![Instrutor para Cursos m:M](complex-data-model/_static/courseassignment.png)

A relação muitos para muitos de Instrutor para Cursos:

* Exige que uma tabela de junção seja representada por um conjunto de entidades.
* É uma tabela de junção pura (tabela sem conteúdo).

É comum nomear uma entidade de junção `EntityName1EntityName2`. Por exemplo, a tabela de junção Instrutor para Cursos com esse padrão é `CourseInstructor`. No entanto, recomendamos que você use um nome que descreve a relação.

Modelos de dados começam simples e aumentam. PJTs (junções sem conteúdo) evoluem com frequência para incluir o conteúdo. Começando com um nome descritivo de entidade, o nome não precisa ser alterado quando a tabela de junção é alterada. O ideal é que a entidade de junção tenha seu próprio nome natural (possivelmente, uma única palavra) no domínio de negócios. Por exemplo, Manuais e Clientes podem ser vinculados com uma entidade de junção chamada Ratings. Para a relação muitos para muitos de Instrutor para Cursos, `CourseAssignment` é preferível a `CourseInstructor`.

### <a name="composite-key"></a>Chave composta

As FKs não permitem valor nulo. As duas FKs em `CourseAssignment` (`InstructorID` e `CourseID`) juntas identificam exclusivamente cada linha da tabela `CourseAssignment`. `CourseAssignment` não exige um PK dedicado. As propriedades `InstructorID` e `CourseID` funcionam como uma PK composta. A única maneira de especificar PKs compostas no EF Core é com a *API fluente*. A próxima seção mostra como configurar a PK composta.

A chave composta garante:

* Várias linhas são permitidas para um curso.
* Várias linhas são permitidas para um instrutor.
* Não é permitido ter várias linhas para o mesmo instrutor e curso.

A entidade de junção `Enrollment` define sua própria PK e, portanto, duplicatas desse tipo são possíveis. Para impedir duplicatas como essas:

* Adicione um índice exclusivo nos campos de FK ou
* Configure `Enrollment` com uma chave primária composta semelhante a `CourseAssignment`. Para obter mais informações, consulte [Índices](/ef/core/modeling/indexes).

## <a name="update-the-db-context"></a>Atualizar o contexto de BD

Adicione o seguinte código realçado a *Data/SchoolContext.cs*:

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

O código anterior adiciona novas entidades e configura a PK composta da entidade `CourseAssignment`.

## <a name="fluent-api-alternative-to-attributes"></a>Alternativa de API fluente para atributos

O método `OnModelCreating` no código anterior usa a *API fluente* para configurar o comportamento do EF Core. A API é chamada "fluente" porque geralmente é usada pelo encadeamento de uma série de chamadas de método em uma única instrução. O [seguinte código](/ef/core/modeling/#use-fluent-api-to-configure-a-model) é um exemplo da API fluente:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

Neste tutorial, a API fluente é usada apenas para o mapeamento do BD que não pode ser feito com atributos. No entanto, a API fluente pode especificar a maioria das regras de formatação, validação e mapeamento que pode ser feita com atributos.

Alguns atributos como `MinimumLength` não podem ser aplicados com a API fluente. `MinimumLength` não altera o esquema; apenas aplica uma regra de validação de tamanho mínimo.

Alguns desenvolvedores preferem usar a API fluente exclusivamente para que possam manter suas classes de entidade "limpas". Atributos e a API fluente podem ser combinados. Há algumas configurações que apenas podem ser feitas com a API fluente (especificando uma PK composta). Há algumas configurações que apenas podem ser feitas com atributos (`MinimumLength`). A prática recomendada para uso de atributos ou da API fluente:

* Escolha uma dessas duas abordagens.
* Use a abordagem escolhida da forma mais consistente possível.

Alguns dos atributos usados neste tutorial são usados para:

* Somente validação (por exemplo, `MinimumLength`).
* Apenas configuração do EF Core (por exemplo, `HasKey`).
* Validação e configuração do EF Core (por exemplo, `[StringLength(50)]`).

Para obter mais informações sobre atributos vs. API fluente, consulte [Métodos de configuração](/ef/core/modeling/).

## <a name="entity-diagram-showing-relationships"></a>Diagrama de entidade mostrando relações

A ilustração a seguir mostra o diagrama criado pelo EF Power Tools para o modelo Escola concluído.

![Diagrama de entidade](complex-data-model/_static/diagram.png)

O diagrama anterior mostra:

* Várias linhas de relação um-para-muitos (1 para \*).
* A linha de relação um para zero ou um (1 para 0..1) entre as entidades `Instructor` e `OfficeAssignment`.
* A linha de relação zero-ou-um-para-muitos (0..1 para *) entre as entidades `Instructor` e `Department`.

## <a name="seed-the-db-with-test-data"></a>Propagar o BD com os dados de teste

Atualize o código em *Data/DbInitializer.cs*:

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Final)]

O código anterior fornece dados de semente para as novas entidades. A maioria desse código cria novos objetos de entidade e carrega dados de exemplo. Os dados de exemplo são usados para teste. Consulte `Enrollments` e `CourseAssignments` para obter exemplos de como tabelas de junção muitos para muitos podem ser propagadas.

## <a name="add-a-migration"></a>Adicionar uma migração

Compile o projeto.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

```PMC
Add-Migration ComplexDataModel
```

# <a name="net-core-clitabnetcore-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

```console
dotnet ef migrations add ComplexDataModel
```

---

O comando anterior exibe um aviso sobre a possível perda de dados.

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

Se o comando `database update` é executado, o seguinte erro é produzido:

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

## <a name="apply-the-migration"></a>Aplicar a migração

Agora que você tem um banco de dados existente, precisa pensar sobre como aplicar as alterações futuras a ele. Este tutorial mostra duas abordagens:

* [Remover e recriar o banco de dados](#drop)
* [Aplicar a migração ao banco de dados existente](#applyexisting). Embora esse método seja mais complexo e demorado, é a abordagem preferencial para ambientes de produção do mundo real. **Observação**: essa é uma seção opcional do tutorial. Você pode remover e recriar etapas e ignorar esta seção. Se você quiser seguir as etapas nesta seção, não realize as etapas de remover e recriar. 

<a name="drop"></a>

### <a name="drop-and-re-create-the-database"></a>Remover e recriar o banco de dados

O código no `DbInitializer` atualizado adiciona dados de semente às novas entidades. Para forçar o EF Core a criar um novo BD, remova e atualize o BD:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

No **PMC** (Console do Gerenciador de Pacotes), execute o seguinte comando:

```PMC
Drop-Database
Update-Database
```

Execute `Get-Help about_EntityFrameworkCore` no PMC para obter informações de ajuda.

# <a name="net-core-clitabnetcore-cli"></a>[CLI do .NET Core](#tab/netcore-cli)

Abra uma janela Comando e navegue para a pasta do projeto. A pasta do projeto contém o arquivo *Startup.cs*.

Insira o seguinte na janela Comando:

 ```console
 dotnet ef database drop
dotnet ef database update
 ```

---

Execute o aplicativo. A execução do aplicativo executa o método `DbInitializer.Initialize`. O `DbInitializer.Initialize` popula o novo BD.

Abra o BD no SSOX:

* Se o SSOX for aberto anteriormente, clique no botão **Atualizar**.
* Expanda o nó **Tabelas**. As tabelas criadas são exibidas.

![Tabelas no SSOX](complex-data-model/_static/ssox-tables.png)

Examine a tabela **CourseAssignment**:

* Clique com o botão direito do mouse na tabela **CourseAssignment** e selecione **Exibir Dados**.
* Verifique se a tabela **CourseAssignment** contém dados.

![Dados de CourseAssignment no SSOX](complex-data-model/_static/ssox-ci-data.png)

<a name="applyexisting"></a>

### <a name="apply-the-migration-to-the-existing-database"></a>Aplicar a migração ao banco de dados existente

Esta seção é opcional. Estas etapas só funcionarão se você tiver ignorado a seção [Remover e recriar o banco de dados](#drop) anterior.

Quando as migrações são executadas com os dados existentes, pode haver restrições de FK que não são atendidas com os dados existentes. Com os dados de produção, é necessário executar etapas para migrar os dados existentes. Esta seção fornece um exemplo de correção de violações de restrição de FK. Não faça essas alterações de código sem um backup. Não faça essas alterações de código se você concluir a seção anterior e atualizou o banco de dados.

O arquivo *{timestamp}_ComplexDataModel.cs* contém o seguinte código:

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_DepartmentID)]

O código anterior adiciona uma FK `DepartmentID` que não permite valor nulo à tabela `Course`. O BD do tutorial anterior contém linhas em `Course` e, portanto, essa tabela não pode ser atualizada por migrações.

Para fazer a migração `ComplexDataModel` funcionar com os dados existentes:

* Altere o código para dar à nova coluna (`DepartmentID`) um valor padrão.
* Crie um departamento fictício chamado "Temp" para atuar como o departamento padrão.

#### <a name="fix-the-foreign-key-constraints"></a>Corrigir as restrições de chave estrangeira

Atualize o método `Up` das classes `ComplexDataModel`:

* Abra o arquivo *{timestamp}_ComplexDataModel.cs*.
* Comente a linha de código que adiciona a coluna `DepartmentID` à tabela `Course`.

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

Adicione o código realçado a seguir. O novo código é inserido após o bloco `.CreateTable( name: "Department"`:

 [!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

Com as alterações anteriores, as linhas `Course` existentes estarão relacionadas ao departamento "Temp" após a execução do método `ComplexDataModel` `Up`.

Um aplicativo de produção:

* Inclui código ou scripts para adicionar linhas `Department` e linhas `Course` relacionadas às novas linhas `Department`.
* Não usa o departamento "Temp" nem o valor padrão para `Course.DepartmentID`.

O próximo tutorial abrange os dados relacionados.

## <a name="additional-resources"></a>Recursos adicionais

* [Versão do YouTube deste tutorial (Parte 1)](https://www.youtube.com/watch?v=0n2f0ObgCoA)
* [Versão do YouTube deste tutorial (Parte 2)](https://www.youtube.com/watch?v=Je0Z5K1TNmY)



> [!div class="step-by-step"]
> [Anterior](xref:data/ef-rp/migrations)
> [Próximo](xref:data/ef-rp/read-related-data)
