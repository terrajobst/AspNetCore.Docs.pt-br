---
title: Páginas Razor com o EF Core no ASP.NET Core – Modelo de dados – 5 de 8
author: tdykstra
description: Neste tutorial, você adiciona mais entidades e relações e personaliza o modelo de dados especificando formatação, validação e regras de mapeamento.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
uid: data/ef-rp/complex-data-model
ms.openlocfilehash: 8a1c0759453b02f4ce1c45471a8f93da626f8261
ms.sourcegitcommit: 257cc3fe8c1d61341aa3b07e5bc0fa3d1c1c1d1c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69583292"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---data-model---5-of-8"></a><span data-ttu-id="be374-103">Páginas Razor com o EF Core no ASP.NET Core – Modelo de dados – 5 de 8</span><span class="sxs-lookup"><span data-stu-id="be374-103">Razor Pages with EF Core in ASP.NET Core - Data Model - 5 of 8</span></span>

<span data-ttu-id="be374-104">Por [Tom Dykstra](https://github.com/tdykstra) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="be374-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="be374-105">Os tutoriais anteriores trabalharam com um modelo de dados básico composto por três entidades.</span><span class="sxs-lookup"><span data-stu-id="be374-105">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="be374-106">Neste tutorial:</span><span class="sxs-lookup"><span data-stu-id="be374-106">In this tutorial:</span></span>

* <span data-ttu-id="be374-107">Mais entidades e relações são adicionadas.</span><span class="sxs-lookup"><span data-stu-id="be374-107">More entities and relationships are added.</span></span>
* <span data-ttu-id="be374-108">O modelo de dados é personalizado com a especificação das regras de formatação, validação e mapeamento de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="be374-108">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="be374-109">O modelo de dados concluído é mostrado na seguinte ilustração:</span><span class="sxs-lookup"><span data-stu-id="be374-109">The completed data model is shown in the following illustration:</span></span>

![Diagrama de entidade](complex-data-model/_static/diagram.png)

## <a name="the-student-entity"></a><span data-ttu-id="be374-111">A entidade Student</span><span class="sxs-lookup"><span data-stu-id="be374-111">The Student entity</span></span>

![Entidade Student](complex-data-model/_static/student-entity.png)

<span data-ttu-id="be374-113">Substitua o código em *Models/Student.cs* pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="be374-113">Replace the code in *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Student.cs)]

<span data-ttu-id="be374-114">O código anterior adiciona uma propriedade `FullName` e adiciona os seguintes atributos às propriedades existentes:</span><span class="sxs-lookup"><span data-stu-id="be374-114">The preceding code adds a `FullName` property and adds the following attributes to existing properties:</span></span>

* `[DataType]`
* `[DisplayFormat]`
* `[StringLength]`
* `[Column]`
* `[Required]`
* `[Display]`

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="be374-115">A propriedade calculada FullName</span><span class="sxs-lookup"><span data-stu-id="be374-115">The FullName calculated property</span></span>

<span data-ttu-id="be374-116">`FullName` é uma propriedade calculada que retorna um valor criado pela concatenação de duas outras propriedades.</span><span class="sxs-lookup"><span data-stu-id="be374-116">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="be374-117">`FullName` não pode ser definido, assim, ele apenas tem um acessador get.</span><span class="sxs-lookup"><span data-stu-id="be374-117">`FullName` can't be set, so it has only a get accessor.</span></span> <span data-ttu-id="be374-118">Nenhuma coluna `FullName` é criada no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="be374-118">No `FullName` column is created in the database.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="be374-119">O atributo DataType</span><span class="sxs-lookup"><span data-stu-id="be374-119">The DataType attribute</span></span>

```csharp
[DataType(DataType.Date)]
```

<span data-ttu-id="be374-120">Para as datas de registro do aluno, todas as páginas atualmente exibem a hora do dia junto com a data, embora apenas a data seja relevante.</span><span class="sxs-lookup"><span data-stu-id="be374-120">For student enrollment dates, all of the pages currently display the time of day along with the date, although only the date is relevant.</span></span> <span data-ttu-id="be374-121">Usando atributos de anotação de dados, você pode fazer uma alteração de código que corrigirá o formato de exibição em cada página que mostra os dados.</span><span class="sxs-lookup"><span data-stu-id="be374-121">By using data annotation attributes, you can make one code change that will fix the display format in every page that shows the data.</span></span> 

<span data-ttu-id="be374-122">O atributo [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) especifica um tipo de dados mais específico do que o tipo intrínseco de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="be374-122">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="be374-123">Neste caso, apenas a data deve ser exibida, não a data e a hora.</span><span class="sxs-lookup"><span data-stu-id="be374-123">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="be374-124">A [Enumeração DataType](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) fornece muitos tipos de dados, como Date, Time, PhoneNumber, Currency, EmailAddress, etc. O atributo `DataType` também pode permitir que o aplicativo forneça automaticamente recursos específicos a um tipo.</span><span class="sxs-lookup"><span data-stu-id="be374-124">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="be374-125">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="be374-125">For example:</span></span>

* <span data-ttu-id="be374-126">O link `mailto:` é criado automaticamente para `DataType.EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="be374-126">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="be374-127">O seletor de data é fornecido para `DataType.Date` na maioria dos navegadores.</span><span class="sxs-lookup"><span data-stu-id="be374-127">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="be374-128">O atributo `DataType` emite atributos HTML 5 `data-` (pronunciados como data dash).</span><span class="sxs-lookup"><span data-stu-id="be374-128">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes.</span></span> <span data-ttu-id="be374-129">Os atributos `DataType` não fornecem validação.</span><span class="sxs-lookup"><span data-stu-id="be374-129">The `DataType` attributes don't provide validation.</span></span>

### <a name="the-displayformat-attribute"></a><span data-ttu-id="be374-130">O atributo DisplayFormat</span><span class="sxs-lookup"><span data-stu-id="be374-130">The DisplayFormat attribute</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="be374-131">`DataType.Date` não especifica o formato da data exibida.</span><span class="sxs-lookup"><span data-stu-id="be374-131">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="be374-132">Por padrão, o campo de dados é exibido de acordo com os formatos padrão com base nas [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support) do servidor.</span><span class="sxs-lookup"><span data-stu-id="be374-132">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="be374-133">O atributo `DisplayFormat` é usado para especificar explicitamente o formato de data.</span><span class="sxs-lookup"><span data-stu-id="be374-133">The `DisplayFormat` attribute is used to explicitly specify the date format.</span></span> <span data-ttu-id="be374-134">A configuração `ApplyFormatInEditMode` especifica que a formatação também deve ser aplicada à interface do usuário de edição.</span><span class="sxs-lookup"><span data-stu-id="be374-134">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="be374-135">Alguns campos não devem usar `ApplyFormatInEditMode`.</span><span class="sxs-lookup"><span data-stu-id="be374-135">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="be374-136">Por exemplo, o símbolo de moeda geralmente não deve ser exibido em uma caixa de texto de edição.</span><span class="sxs-lookup"><span data-stu-id="be374-136">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="be374-137">O atributo `DisplayFormat` pode ser usado por si só.</span><span class="sxs-lookup"><span data-stu-id="be374-137">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="be374-138">Geralmente, é uma boa ideia usar o atributo `DataType` com o atributo `DisplayFormat`.</span><span class="sxs-lookup"><span data-stu-id="be374-138">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="be374-139">O atributo `DataType` transmite a semântica dos dados em vez de como renderizá-los em uma tela.</span><span class="sxs-lookup"><span data-stu-id="be374-139">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="be374-140">O atributo `DataType` oferece os seguintes benefícios que não estão disponíveis em `DisplayFormat`:</span><span class="sxs-lookup"><span data-stu-id="be374-140">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="be374-141">O navegador pode habilitar recursos do HTML5.</span><span class="sxs-lookup"><span data-stu-id="be374-141">The browser can enable HTML5 features.</span></span> <span data-ttu-id="be374-142">Por exemplo, mostra um controle de calendário, o símbolo de moeda apropriado à localidade, links de email e validação de entrada do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="be374-142">For example, show a calendar control, the locale-appropriate currency symbol, email links, and client-side input validation.</span></span>
* <span data-ttu-id="be374-143">Por padrão, o navegador renderiza os dados usando o formato correto de acordo com a localidade.</span><span class="sxs-lookup"><span data-stu-id="be374-143">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="be374-144">Para obter mais informações, consulte a [documentação do Auxiliar de Marcação \<input>](xref:mvc/views/working-with-forms#the-input-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="be374-144">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

### <a name="the-stringlength-attribute"></a><span data-ttu-id="be374-145">O atributo StringLength</span><span class="sxs-lookup"><span data-stu-id="be374-145">The StringLength attribute</span></span>

```csharp
[StringLength(50, ErrorMessage = "First name cannot be longer than 50 characters.")]
```

<span data-ttu-id="be374-146">Regras de validação de dados e mensagens de erro de validação podem ser especificadas com atributos.</span><span class="sxs-lookup"><span data-stu-id="be374-146">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="be374-147">O atributo [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) especifica o tamanho mínimo e máximo de caracteres permitidos em um campo de dados.</span><span class="sxs-lookup"><span data-stu-id="be374-147">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="be374-148">O código mostrado limita os nomes a, no máximo, 50 caracteres.</span><span class="sxs-lookup"><span data-stu-id="be374-148">The code shown limits names to no more than 50 characters.</span></span> <span data-ttu-id="be374-149">Um exemplo que define o comprimento mínimo da cadeia de caracteres é mostrado [posteriormente](#the-required-attribute).</span><span class="sxs-lookup"><span data-stu-id="be374-149">An example that sets the minimum string length is shown [later](#the-required-attribute).</span></span>

<span data-ttu-id="be374-150">O atributo `StringLength` também fornece a validação do lado do cliente e do servidor.</span><span class="sxs-lookup"><span data-stu-id="be374-150">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="be374-151">O valor mínimo não tem impacto sobre o esquema de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="be374-151">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="be374-152">O atributo `StringLength` não impede que um usuário insira um espaço em branco em um nome.</span><span class="sxs-lookup"><span data-stu-id="be374-152">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="be374-153">O atributo [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) pode ser usado para aplicar restrições à entrada.</span><span class="sxs-lookup"><span data-stu-id="be374-153">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) attribute can be used to apply restrictions to the input.</span></span> <span data-ttu-id="be374-154">Por exemplo, o seguinte código exige que o primeiro caractere esteja em maiúscula e os caracteres restantes estejam em ordem alfabética:</span><span class="sxs-lookup"><span data-stu-id="be374-154">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="be374-155">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="be374-155">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="be374-156">No **SSOX** (Pesquisador de Objetos do SQL Server), abra o designer de tabela Aluno clicando duas vezes na tabela **Aluno**.</span><span class="sxs-lookup"><span data-stu-id="be374-156">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![Tabela Alunos no SSOX antes das migrações](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="be374-158">A imagem anterior mostra o esquema para a tabela `Student`.</span><span class="sxs-lookup"><span data-stu-id="be374-158">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="be374-159">Os campos de nome têm o tipo `nvarchar(MAX)`.</span><span class="sxs-lookup"><span data-stu-id="be374-159">The name fields have type `nvarchar(MAX)`.</span></span> <span data-ttu-id="be374-160">Quando uma migração é criada e aplicada posteriormente neste tutorial, os campos de nome se tornam `nvarchar(50)` como resultado dos atributos de comprimento da cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="be374-160">When a migration is created and applied later in this tutorial, the name fields become `nvarchar(50)` as a result of the string length attributes.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="be374-161">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="be374-161">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="be374-162">Em sua ferramenta SQLite, examine as definições de coluna da tabela `Student`.</span><span class="sxs-lookup"><span data-stu-id="be374-162">In your SQLite tool, examine the column definitions for the `Student` table.</span></span> <span data-ttu-id="be374-163">Os campos de nome têm o tipo `Text`.</span><span class="sxs-lookup"><span data-stu-id="be374-163">The name fields have type `Text`.</span></span> <span data-ttu-id="be374-164">Observe que o campo nome é chamado `FirstMidName`.</span><span class="sxs-lookup"><span data-stu-id="be374-164">Notice that the first name field is called `FirstMidName`.</span></span> <span data-ttu-id="be374-165">Na próxima seção, altere o nome dessa coluna para `FirstName`.</span><span class="sxs-lookup"><span data-stu-id="be374-165">In the next section, you change the name of that column to `FirstName`.</span></span>

---

### <a name="the-column-attribute"></a><span data-ttu-id="be374-166">O atributo Column</span><span class="sxs-lookup"><span data-stu-id="be374-166">The Column attribute</span></span>

```csharp
[Column("FirstName")]
public string FirstMidName { get; set; }
```

<span data-ttu-id="be374-167">Os atributos podem controlar como as classes e propriedades são mapeadas para o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="be374-167">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="be374-168">No modelo `Student`, o atributo `Column` é usado para mapear o nome da propriedade `FirstMidName` para "FirstName" no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="be374-168">In the `Student` model, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the database.</span></span>

<span data-ttu-id="be374-169">Quando o banco de dados é criado, os nomes de propriedade no modelo são usados para nomes de coluna (exceto quando o atributo `Column` é usado).</span><span class="sxs-lookup"><span data-stu-id="be374-169">When the database is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span> <span data-ttu-id="be374-170">O modelo `Student` usa `FirstMidName` para o campo de nome porque o campo também pode conter um sobrenome.</span><span class="sxs-lookup"><span data-stu-id="be374-170">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="be374-171">Com o atributo `[Column]`, `Student.FirstMidName` no modelo de dados é mapeado para a coluna `FirstName` da tabela `Student`.</span><span class="sxs-lookup"><span data-stu-id="be374-171">With the `[Column]` attribute, `Student.FirstMidName` in the data model maps to the `FirstName` column of the `Student` table.</span></span> <span data-ttu-id="be374-172">A adição do atributo `Column` altera o modelo que dá suporte ao `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="be374-172">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="be374-173">O modelo que dá suporte ao `SchoolContext` não corresponde mais ao banco de dados.</span><span class="sxs-lookup"><span data-stu-id="be374-173">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="be374-174">Essa discrepância será resolvida adicionando uma migração posteriormente neste tutorial.</span><span class="sxs-lookup"><span data-stu-id="be374-174">That discrepancy will be resolved by adding a migration later in this tutorial.</span></span>

### <a name="the-required-attribute"></a><span data-ttu-id="be374-175">O atributo Required</span><span class="sxs-lookup"><span data-stu-id="be374-175">The Required attribute</span></span>

```csharp
[Required]
```

<span data-ttu-id="be374-176">O atributo `Required` torna as propriedades de nome campos obrigatórios.</span><span class="sxs-lookup"><span data-stu-id="be374-176">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="be374-177">O atributo `Required` não é necessário para tipos que não permitem valor nulo, como tipos de valor (por exemplo, `DateTime`, `int` e `double`).</span><span class="sxs-lookup"><span data-stu-id="be374-177">The `Required` attribute isn't needed for non-nullable types such as value types (for example, `DateTime`, `int`, and `double`).</span></span> <span data-ttu-id="be374-178">Tipos que não podem ser nulos são tratados automaticamente como campos obrigatórios.</span><span class="sxs-lookup"><span data-stu-id="be374-178">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="be374-179">O atributo `Required` pode ser substituído por um parâmetro de tamanho mínimo no atributo `StringLength`:</span><span class="sxs-lookup"><span data-stu-id="be374-179">The `Required` attribute could be replaced with a minimum length parameter in the `StringLength` attribute:</span></span>

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a><span data-ttu-id="be374-180">O atributo Display</span><span class="sxs-lookup"><span data-stu-id="be374-180">The Display attribute</span></span>

```csharp
[Display(Name = "Last Name")]
```

<span data-ttu-id="be374-181">O atributo `Display` especifica que a legenda para as caixas de texto deve ser "Nome", "Sobrenome", "Nome Completo" e "Data de Registro".</span><span class="sxs-lookup"><span data-stu-id="be374-181">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="be374-182">As legendas padrão não tinham nenhum espaço entre as palavras, por exemplo, "Lastname".</span><span class="sxs-lookup"><span data-stu-id="be374-182">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="create-a-migration"></a><span data-ttu-id="be374-183">Criar uma migração</span><span class="sxs-lookup"><span data-stu-id="be374-183">Create a migration</span></span>

<span data-ttu-id="be374-184">Execute o aplicativo e acesse a página Alunos.</span><span class="sxs-lookup"><span data-stu-id="be374-184">Run the app and go to the Students page.</span></span> <span data-ttu-id="be374-185">Uma exceção é gerada.</span><span class="sxs-lookup"><span data-stu-id="be374-185">An exception is thrown.</span></span> <span data-ttu-id="be374-186">O atributo `[Column]` faz com que o EF Espere encontrar uma coluna chamada `FirstName`, mas o nome da coluna no banco de dados ainda é `FirstMidName`.</span><span class="sxs-lookup"><span data-stu-id="be374-186">The `[Column]` attribute causes EF to expect to find a column named `FirstName`, but the column name in the database is still `FirstMidName`.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="be374-187">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="be374-187">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="be374-188">A mensagem de erro é semelhante ao exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="be374-188">The error message is similar to the following example:</span></span>

```
SqlException: Invalid column name 'FirstName'.
```

* <span data-ttu-id="be374-189">No PMC, insira os seguintes comandos para criar uma nova migração e atualizar o banco de dados:</span><span class="sxs-lookup"><span data-stu-id="be374-189">In the PMC, enter the following commands to create a new migration and update the database:</span></span>

  ```powershell
  Add-Migration ColumnFirstName
  Update-Database
  ```

  <span data-ttu-id="be374-190">O primeiro desses comandos gera a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="be374-190">The first of these commands generates the following warning message:</span></span>

  ```text
  An operation was scaffolded that may result in the loss of data.
  Please review the migration for accuracy.
  ```

  <span data-ttu-id="be374-191">O aviso é gerado porque os campos de nome agora estão limitados a 50 caracteres.</span><span class="sxs-lookup"><span data-stu-id="be374-191">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="be374-192">Se um nome no banco de dados tiver mais de 50 caracteres, o 51º caractere até o último caractere serão perdidos.</span><span class="sxs-lookup"><span data-stu-id="be374-192">If a name in the database had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="be374-193">Abra a tabela Alunos no SSOX:</span><span class="sxs-lookup"><span data-stu-id="be374-193">Open the Student table in SSOX:</span></span>

  ![Tabela Alunos no SSOX após as migrações](complex-data-model/_static/ssox-after-migration.png)

  <span data-ttu-id="be374-195">Antes de a migração ser aplicada, as colunas de nome eram do tipo [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span><span class="sxs-lookup"><span data-stu-id="be374-195">Before the migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="be374-196">As colunas de nome agora são `nvarchar(50)`.</span><span class="sxs-lookup"><span data-stu-id="be374-196">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="be374-197">O nome da coluna foi alterado de `FirstMidName` para `FirstName`.</span><span class="sxs-lookup"><span data-stu-id="be374-197">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="be374-198">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="be374-198">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="be374-199">A mensagem de erro é semelhante ao exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="be374-199">The error message is similar to the following example:</span></span>

```
SqliteException: SQLite Error 1: 'no such column: s.FirstName'.
```

* <span data-ttu-id="be374-200">Abra uma janela Comando na pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="be374-200">Open a command window in the project folder.</span></span> <span data-ttu-id="be374-201">Insira os seguintes comandos para criar uma nova migração e atualizar o banco de dados:</span><span class="sxs-lookup"><span data-stu-id="be374-201">Enter the following commands to create a new migration and update the database:</span></span>

  ```console
  dotnet ef migrations add ColumnFirstName
  dotnet ef database update
  ```

  <span data-ttu-id="be374-202">O comando de atualização de banco de dados exibe um erro como o exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="be374-202">The database update command displays an error like the following example:</span></span>

  ```text
  SQLite does not support this migration operation ('AlterColumnOperation'). For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
  ```

<span data-ttu-id="be374-203">Para este tutorial, a maneira de passar esse erro é excluir e recriar a migração inicial.</span><span class="sxs-lookup"><span data-stu-id="be374-203">For this tutorial, the way to get past this error is to delete and re-create the initial migration.</span></span> <span data-ttu-id="be374-204">Para obter mais informações, confira a nota de aviso do SQLite na parte superior do [tutorial de migrações](xref:data/ef-rp/migrations).</span><span class="sxs-lookup"><span data-stu-id="be374-204">For more information, see the SQLite warning note at the top of the [migrations tutorial](xref:data/ef-rp/migrations).</span></span>

* <span data-ttu-id="be374-205">Exclua a pasta *Migração*.</span><span class="sxs-lookup"><span data-stu-id="be374-205">Delete the *Migrations* folder.</span></span>
* <span data-ttu-id="be374-206">Execute os seguintes comandos para remover o banco de dados, criar uma nova migração inicial e aplicar a migração:</span><span class="sxs-lookup"><span data-stu-id="be374-206">Run the following commands to drop the database, create a new initial migration, and apply the migration:</span></span>

  ```console
  dotnet ef database drop --force
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

* <span data-ttu-id="be374-207">Examine a tabela Student em sua ferramenta do SQLite.</span><span class="sxs-lookup"><span data-stu-id="be374-207">Examine the Student table in your SQLite tool.</span></span> <span data-ttu-id="be374-208">A coluna que era FirstMidName agora é FirstName.</span><span class="sxs-lookup"><span data-stu-id="be374-208">The column that was FirstMidName is now FirstName.</span></span>

---

* <span data-ttu-id="be374-209">Execute o aplicativo e acesse a página Alunos.</span><span class="sxs-lookup"><span data-stu-id="be374-209">Run the app and go to the Students page.</span></span>
* <span data-ttu-id="be374-210">Observe que os horários não são inseridos nem exibidos juntamente com datas.</span><span class="sxs-lookup"><span data-stu-id="be374-210">Notice that times are not input or displayed along with dates.</span></span>
* <span data-ttu-id="be374-211">Selecione **Criar Novo** e tente inserir um nome com mais de 50 caracteres.</span><span class="sxs-lookup"><span data-stu-id="be374-211">Select **Create New**, and try to enter a name longer than 50 characters.</span></span>

> [!Note]
> <span data-ttu-id="be374-212">Nas seções a seguir, a criação do aplicativo em alguns estágios gera erros do compilador.</span><span class="sxs-lookup"><span data-stu-id="be374-212">In the following sections, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="be374-213">As instruções especificam quando compilar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="be374-213">The instructions specify when to build the app.</span></span>

## <a name="the-instructor-entity"></a><span data-ttu-id="be374-214">A entidade Instructor</span><span class="sxs-lookup"><span data-stu-id="be374-214">The Instructor Entity</span></span>

![Entidade Instructor](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="be374-216">Crie *Models/Instructor.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="be374-216">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Instructor.cs)]

<span data-ttu-id="be374-217">Vários atributos podem estar em uma linha.</span><span class="sxs-lookup"><span data-stu-id="be374-217">Multiple attributes can be on one line.</span></span> <span data-ttu-id="be374-218">Os atributos `HireDate` podem ser escritos da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="be374-218">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="navigation-properties"></a><span data-ttu-id="be374-219">Propriedades de navegação</span><span class="sxs-lookup"><span data-stu-id="be374-219">Navigation properties</span></span>

<span data-ttu-id="be374-220">As propriedades `CourseAssignments` e `OfficeAssignment` são propriedades de navegação.</span><span class="sxs-lookup"><span data-stu-id="be374-220">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="be374-221">Um instrutor pode ministrar qualquer quantidade de cursos e, portanto, `CourseAssignments` é definido como uma coleção.</span><span class="sxs-lookup"><span data-stu-id="be374-221">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="be374-222">Um instrutor pode ter no máximo um escritório, portanto, a propriedade `OfficeAssignment` mantém uma única entidade `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="be374-222">An instructor can have at most one office, so the `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="be374-223">`OfficeAssignment` será nulo se nenhum escritório for atribuído.</span><span class="sxs-lookup"><span data-stu-id="be374-223">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="the-officeassignment-entity"></a><span data-ttu-id="be374-224">A entidade OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="be374-224">The OfficeAssignment entity</span></span>

![Entidade OfficeAssignment](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="be374-226">Crie *Models/OfficeAssignment.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="be374-226">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="be374-227">O atributo Key</span><span class="sxs-lookup"><span data-stu-id="be374-227">The Key attribute</span></span>

<span data-ttu-id="be374-228">O atributo `[Key]` é usado para identificar uma propriedade como a PK (chave primária) quando o nome da propriedade é algo diferente de classnameID ou ID.</span><span class="sxs-lookup"><span data-stu-id="be374-228">The `[Key]` attribute is used to identify a property as the primary key (PK) when the property name is something other than classnameID or ID.</span></span>

<span data-ttu-id="be374-229">Há uma relação um para zero ou um entre as entidades `Instructor` e `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="be374-229">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="be374-230">Uma atribuição de escritório existe apenas em relação ao instrutor ao qual ela é atribuída.</span><span class="sxs-lookup"><span data-stu-id="be374-230">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="be374-231">A PK `OfficeAssignment` também é a FK (chave estrangeira) da entidade `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="be374-231">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span>

<span data-ttu-id="be374-232">O EF Core não pode reconhecer `InstructorID` automaticamente como a CP de `OfficeAssignment` porque `InstructorID` não segue a convenção de nomenclatura de ID ou classnameID.</span><span class="sxs-lookup"><span data-stu-id="be374-232">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because `InstructorID` doesn't follow the ID or classnameID naming convention.</span></span> <span data-ttu-id="be374-233">Portanto, o atributo `Key` é usado para identificar `InstructorID` como a PK:</span><span class="sxs-lookup"><span data-stu-id="be374-233">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="be374-234">Por padrão, o EF Core trata a chave como não gerada pelo banco de dados porque a coluna destina-se a uma relação de identificação.</span><span class="sxs-lookup"><span data-stu-id="be374-234">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="be374-235">A propriedade de navegação Instructor</span><span class="sxs-lookup"><span data-stu-id="be374-235">The Instructor navigation property</span></span>

<span data-ttu-id="be374-236">A propriedade de navegação `Instructor.OfficeAssignment` pode ser nula porque pode não haver uma linha `OfficeAssignment` para um determinado instrutor.</span><span class="sxs-lookup"><span data-stu-id="be374-236">The `Instructor.OfficeAssignment` navigation property can be null because there might not be an `OfficeAssignment` row for a given instructor.</span></span> <span data-ttu-id="be374-237">Um instrutor pode não ter uma atribuição de escritório.</span><span class="sxs-lookup"><span data-stu-id="be374-237">An instructor might not have an office assignment.</span></span>

<span data-ttu-id="be374-238">A propriedade de navegação `OfficeAssignment.Instructor` sempre terá uma entidade de instrutor porque o tipo `InstructorID` de chave estrangeira é `int`, um tipo de valor não anulável.</span><span class="sxs-lookup"><span data-stu-id="be374-238">The `OfficeAssignment.Instructor` navigation property will always have an instructor entity because the foreign key `InstructorID` type is `int`, a non-nullable value type.</span></span> <span data-ttu-id="be374-239">Uma atribuição de escritório não pode existir sem um instrutor.</span><span class="sxs-lookup"><span data-stu-id="be374-239">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="be374-240">Quando uma entidade `Instructor` tem uma entidade `OfficeAssignment` relacionada, cada entidade tem uma referência à outra em sua propriedade de navegação.</span><span class="sxs-lookup"><span data-stu-id="be374-240">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="be374-241">A entidade Course</span><span class="sxs-lookup"><span data-stu-id="be374-241">The Course Entity</span></span>

![Entidade Course](complex-data-model/_static/course-entity.png)

<span data-ttu-id="be374-243">Atualize *Models/Course.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="be374-243">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Course.cs?highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="be374-244">A entidade `Course` tem uma propriedade de FK (chave estrangeira) `DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="be374-244">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="be374-245">`DepartmentID` aponta para a entidade `Department` relacionada.</span><span class="sxs-lookup"><span data-stu-id="be374-245">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="be374-246">A entidade `Course` tem uma propriedade de navegação `Department`.</span><span class="sxs-lookup"><span data-stu-id="be374-246">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="be374-247">O EF Core não exige uma propriedade de chave estrangeira para um modelo de dados quando o modelo tem uma propriedade de navegação para uma entidade relacionada.</span><span class="sxs-lookup"><span data-stu-id="be374-247">EF Core doesn't require a foreign key property for a data model when the model has a navigation property for a related entity.</span></span> <span data-ttu-id="be374-248">O EF Core cria automaticamente FKs no banco de dados sempre que forem necessárias.</span><span class="sxs-lookup"><span data-stu-id="be374-248">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="be374-249">O EF Core cria [propriedades de sombra](/ef/core/modeling/shadow-properties) para FKs criadas automaticamente.</span><span class="sxs-lookup"><span data-stu-id="be374-249">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="be374-250">Porém, incluir explicitamente a FK no modelo de dados pode tornar as atualizações mais simples e mais eficientes.</span><span class="sxs-lookup"><span data-stu-id="be374-250">However, explicitly including the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="be374-251">Por exemplo, considere um modelo em que a propriedade de FK `DepartmentID` *não* é incluída.</span><span class="sxs-lookup"><span data-stu-id="be374-251">For example, consider a model where the FK property `DepartmentID` is *not* included.</span></span> <span data-ttu-id="be374-252">Quando uma entidade de curso é buscada para editar:</span><span class="sxs-lookup"><span data-stu-id="be374-252">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="be374-253">A propriedade `Department` será nula se não for carregada de forma explícita.</span><span class="sxs-lookup"><span data-stu-id="be374-253">The `Department` property is null if it's not explicitly loaded.</span></span>
* <span data-ttu-id="be374-254">Para atualizar a entidade de curso, a entidade `Department` primeiro deve ser buscada.</span><span class="sxs-lookup"><span data-stu-id="be374-254">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="be374-255">Quando a propriedade de FK `DepartmentID` está incluída no modelo de dados, não é necessário buscar a entidade `Department` antes de uma atualização.</span><span class="sxs-lookup"><span data-stu-id="be374-255">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="be374-256">O atributo DatabaseGenerated</span><span class="sxs-lookup"><span data-stu-id="be374-256">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="be374-257">O atributo `[DatabaseGenerated(DatabaseGeneratedOption.None)]` especifica que a PK é fornecida pelo aplicativo em vez de ser gerada pelo banco de dados.</span><span class="sxs-lookup"><span data-stu-id="be374-257">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="be374-258">Por padrão, o EF Core supõe que os valores de PK sejam gerados pelo banco de dados.</span><span class="sxs-lookup"><span data-stu-id="be374-258">By default, EF Core assumes that PK values are generated by the database.</span></span> <span data-ttu-id="be374-259">O banco de dados gerado costuma ser a melhor abordagem.</span><span class="sxs-lookup"><span data-stu-id="be374-259">Database-generated is generally the best approach.</span></span> <span data-ttu-id="be374-260">Para entidades `Course`, o usuário especifica o PK.</span><span class="sxs-lookup"><span data-stu-id="be374-260">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="be374-261">Por exemplo, um número de curso, como uma série 1000 para o departamento de matemática e uma série 2000 para o departamento em inglês.</span><span class="sxs-lookup"><span data-stu-id="be374-261">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="be374-262">O atributo `DatabaseGenerated` também pode ser usado para gerar valores padrão.</span><span class="sxs-lookup"><span data-stu-id="be374-262">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="be374-263">Por exemplo, o banco de dados pode gerar automaticamente um campo de data para registrar a data em que uma linha foi criada ou atualizada.</span><span class="sxs-lookup"><span data-stu-id="be374-263">For example, the database can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="be374-264">Para obter mais informações, consulte [Propriedades geradas](/ef/core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="be374-264">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="be374-265">Propriedades de navegação e de chave estrangeira</span><span class="sxs-lookup"><span data-stu-id="be374-265">Foreign key and navigation properties</span></span>

<span data-ttu-id="be374-266">As propriedades de navegação e de FK (chave estrangeira) na entidade `Course` refletem as seguintes relações:</span><span class="sxs-lookup"><span data-stu-id="be374-266">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="be374-267">Um curso é atribuído a um departamento; portanto, há uma FK `DepartmentID` e uma propriedade de navegação `Department`.</span><span class="sxs-lookup"><span data-stu-id="be374-267">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="be374-268">Um curso pode ter qualquer quantidade de estudantes inscritos; portanto, a propriedade de navegação `Enrollments` é uma coleção:</span><span class="sxs-lookup"><span data-stu-id="be374-268">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="be374-269">Um curso pode ser ministrado por vários instrutores; portanto, a propriedade de navegação `CourseAssignments` é uma coleção:</span><span class="sxs-lookup"><span data-stu-id="be374-269">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection:</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="be374-270">`CourseAssignment` é explicado [posteriormente](#many-to-many-relationships).</span><span class="sxs-lookup"><span data-stu-id="be374-270">`CourseAssignment` is explained [later](#many-to-many-relationships).</span></span>

## <a name="the-department-entity"></a><span data-ttu-id="be374-271">A entidade Department</span><span class="sxs-lookup"><span data-stu-id="be374-271">The Department entity</span></span>

![Entidade Department](complex-data-model/_static/department-entity.png)

<span data-ttu-id="be374-273">Crie *Models/Department.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="be374-273">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Models/Department1.cs)]

### <a name="the-column-attribute"></a><span data-ttu-id="be374-274">O atributo Column</span><span class="sxs-lookup"><span data-stu-id="be374-274">The Column attribute</span></span>

<span data-ttu-id="be374-275">Anteriormente, o atributo `Column` foi usado para alterar o mapeamento de nome de coluna.</span><span class="sxs-lookup"><span data-stu-id="be374-275">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="be374-276">No código da entidade `Department`, o atributo `Column` é usado para alterar o mapeamento de tipo de dados SQL.</span><span class="sxs-lookup"><span data-stu-id="be374-276">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="be374-277">A coluna `Budget` é definida usando o tipo de dinheiro do SQL Server no banco de dados:</span><span class="sxs-lookup"><span data-stu-id="be374-277">The `Budget` column is defined using the SQL Server money type in the database:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="be374-278">Em geral, o mapeamento de coluna não é necessário.</span><span class="sxs-lookup"><span data-stu-id="be374-278">Column mapping is generally not required.</span></span> <span data-ttu-id="be374-279">O EF Core escolhe o tipo de dados do SQL Server apropriado com base no tipo CLR da propriedade.</span><span class="sxs-lookup"><span data-stu-id="be374-279">EF Core chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="be374-280">O tipo `decimal` CLR é mapeado para um tipo `decimal` SQL Server.</span><span class="sxs-lookup"><span data-stu-id="be374-280">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="be374-281">`Budget` refere-se à moeda e o tipo de dados de dinheiro é mais apropriado para moeda.</span><span class="sxs-lookup"><span data-stu-id="be374-281">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="be374-282">Propriedades de navegação e de chave estrangeira</span><span class="sxs-lookup"><span data-stu-id="be374-282">Foreign key and navigation properties</span></span>

<span data-ttu-id="be374-283">As propriedades de navegação e de FK refletem as seguintes relações:</span><span class="sxs-lookup"><span data-stu-id="be374-283">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="be374-284">Um departamento pode ou não ter um administrador.</span><span class="sxs-lookup"><span data-stu-id="be374-284">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="be374-285">Um administrador é sempre um instrutor.</span><span class="sxs-lookup"><span data-stu-id="be374-285">An administrator is always an instructor.</span></span> <span data-ttu-id="be374-286">Portanto, a propriedade `InstructorID` está incluída como a FK da entidade `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="be374-286">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="be374-287">A propriedade de navegação é chamada `Administrator`, mas contém uma entidade `Instructor`:</span><span class="sxs-lookup"><span data-stu-id="be374-287">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="be374-288">O ponto de interrogação (?) no código anterior especifica que a propriedade permite valor nulo.</span><span class="sxs-lookup"><span data-stu-id="be374-288">The question mark (?) in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="be374-289">Um departamento pode ter vários cursos e, portanto, há uma propriedade de navegação Courses:</span><span class="sxs-lookup"><span data-stu-id="be374-289">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="be374-290">por convenção, o EF Core habilita a exclusão em cascata em FKs que não permitem valor nulo e em relações muitos para muitos.</span><span class="sxs-lookup"><span data-stu-id="be374-290">By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="be374-291">Esse comportamento padrão pode resultar em regras circulares de exclusão em cascata.</span><span class="sxs-lookup"><span data-stu-id="be374-291">This default behavior can result in circular cascade delete rules.</span></span> <span data-ttu-id="be374-292">As regras de exclusão em cascata circular causam uma exceção quando uma migração é adicionada.</span><span class="sxs-lookup"><span data-stu-id="be374-292">Circular cascade delete rules cause an exception when a migration is added.</span></span>

<span data-ttu-id="be374-293">Por exemplo, se a propriedade `Department.InstructorID` tiver sido definida como não anulável, o EF Core configurará uma regra de exclusão em cascata.</span><span class="sxs-lookup"><span data-stu-id="be374-293">For example, if the `Department.InstructorID` property was defined as non-nullable, EF Core would configure a cascade delete rule.</span></span> <span data-ttu-id="be374-294">Nesse caso, o departamento seria excluído quando o instrutor atribuído como seu administrador fosse excluído.</span><span class="sxs-lookup"><span data-stu-id="be374-294">In that case, the department would be deleted when the instructor assigned as its administrator is deleted.</span></span> <span data-ttu-id="be374-295">Nesse cenário, uma regra restrita fará mais sentido.</span><span class="sxs-lookup"><span data-stu-id="be374-295">In this scenario, a restrict rule would make more sense.</span></span> <span data-ttu-id="be374-296">A API fluente a seguir definiria uma regra restrita e desabilitaria a exclusão em cascata.</span><span class="sxs-lookup"><span data-stu-id="be374-296">The following fluent API would set a restrict rule and disable cascade delete.</span></span>

  ```csharp
  modelBuilder.Entity<Department>()
     .HasOne(d => d.Administrator)
     .WithMany()
     .OnDelete(DeleteBehavior.Restrict)
  ```

## <a name="the-enrollment-entity"></a><span data-ttu-id="be374-297">A entidade Enrollment</span><span class="sxs-lookup"><span data-stu-id="be374-297">The Enrollment entity</span></span>

<span data-ttu-id="be374-298">Um registro se refere a um curso feito por um aluno.</span><span class="sxs-lookup"><span data-stu-id="be374-298">An enrollment record is for one course taken by one student.</span></span>

![Entidade Enrollment](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="be374-300">Atualize *Models/Enrollment.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="be374-300">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Enrollment.cs?highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="be374-301">Propriedades de navegação e de chave estrangeira</span><span class="sxs-lookup"><span data-stu-id="be374-301">Foreign key and navigation properties</span></span>

<span data-ttu-id="be374-302">As propriedades de navegação e de FK refletem as seguintes relações:</span><span class="sxs-lookup"><span data-stu-id="be374-302">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="be374-303">Um registro destina-se a um curso e, portanto, há uma propriedade de FK `CourseID` e uma propriedade de navegação `Course`:</span><span class="sxs-lookup"><span data-stu-id="be374-303">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="be374-304">Um registro destina-se a um aluno e, portanto, há uma propriedade de FK `StudentID` e uma propriedade de navegação `Student`:</span><span class="sxs-lookup"><span data-stu-id="be374-304">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="be374-305">Relações muitos para muitos</span><span class="sxs-lookup"><span data-stu-id="be374-305">Many-to-Many Relationships</span></span>

<span data-ttu-id="be374-306">Há uma relação muitos para muitos entre as entidades `Student` e `Course`.</span><span class="sxs-lookup"><span data-stu-id="be374-306">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="be374-307">A entidade `Enrollment` funciona como uma tabela de junção muitos para muitos *com conteúdo* no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="be374-307">The `Enrollment` entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="be374-308">"Com conteúdo" significa que a tabela `Enrollment` contém dados adicionais além das FKs das tabelas unidas (nesse caso, a FK e `Grade`).</span><span class="sxs-lookup"><span data-stu-id="be374-308">"With payload" means that the `Enrollment` table contains additional data besides FKs for the joined tables (in this case, the PK and `Grade`).</span></span>

<span data-ttu-id="be374-309">A ilustração a seguir mostra a aparência dessas relações em um diagrama de entidades.</span><span class="sxs-lookup"><span data-stu-id="be374-309">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="be374-310">(Esse diagrama foi gerado com o [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) para EF 6.x.</span><span class="sxs-lookup"><span data-stu-id="be374-310">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="be374-311">A criação do diagrama não faz parte do tutorial.)</span><span class="sxs-lookup"><span data-stu-id="be374-311">Creating the diagram isn't part of the tutorial.)</span></span>

![Relação muitos para muitos de Student-Course](complex-data-model/_static/student-course.png)

<span data-ttu-id="be374-313">Cada linha de relação tem um 1 em uma extremidade e um asterisco (\*) na outra, indicando uma relação um para muitos.</span><span class="sxs-lookup"><span data-stu-id="be374-313">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="be374-314">Se a tabela `Enrollment` não incluir informações de nota, ela apenas precisará conter as duas FKs (`CourseID` e `StudentID`).</span><span class="sxs-lookup"><span data-stu-id="be374-314">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs (`CourseID` and `StudentID`).</span></span> <span data-ttu-id="be374-315">Uma tabela de junção muitos para muitos sem conteúdo é às vezes chamada de PJT (uma tabela de junção pura).</span><span class="sxs-lookup"><span data-stu-id="be374-315">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="be374-316">As entidades `Instructor` e `Course` têm uma relação muitos para muitos usando uma tabela de junção pura.</span><span class="sxs-lookup"><span data-stu-id="be374-316">The `Instructor` and `Course` entities have a many-to-many relationship using a pure join table.</span></span>

<span data-ttu-id="be374-317">Observação: O EF 6.x é compatível com tabelas de junção implícita para relações muitos para muitos, ao contrário do EF Core.</span><span class="sxs-lookup"><span data-stu-id="be374-317">Note: EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="be374-318">Para obter mais informações, consulte [Relações muitos para muitos no EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span><span class="sxs-lookup"><span data-stu-id="be374-318">For more information, see [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="be374-319">A entidade CourseAssignment</span><span class="sxs-lookup"><span data-stu-id="be374-319">The CourseAssignment entity</span></span>

![Entidade CourseAssignment](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="be374-321">Crie *Models/CourseAssignment.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="be374-321">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/CourseAssignment.cs)]

<span data-ttu-id="be374-322">O relacionamento de muitos para muitos do instrutor para cursos requer uma tabela de junção e a entidade para essa tabela de junção é CourseAssignment.</span><span class="sxs-lookup"><span data-stu-id="be374-322">The Instructor-to-Courses many-to-many relationship requires a join table, and the entity for that join table is CourseAssignment.</span></span>

![Instrutor para Cursos m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="be374-324">É comum nomear uma entidade de junção `EntityName1EntityName2`.</span><span class="sxs-lookup"><span data-stu-id="be374-324">It's common to name a join entity `EntityName1EntityName2`.</span></span> <span data-ttu-id="be374-325">Por exemplo, a tabela de junção Instrutor para Cursos com esse padrão seria `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="be374-325">For example, the Instructor-to-Courses join table using this pattern would be `CourseInstructor`.</span></span> <span data-ttu-id="be374-326">No entanto, recomendamos que você use um nome que descreve a relação.</span><span class="sxs-lookup"><span data-stu-id="be374-326">However, we recommend using a name that describes the relationship.</span></span>

<span data-ttu-id="be374-327">Modelos de dados começam simples e aumentam.</span><span class="sxs-lookup"><span data-stu-id="be374-327">Data models start out simple and grow.</span></span> <span data-ttu-id="be374-328">As PJTs (tabelas de junção sem payload) costumam evoluir para incluir a payload.</span><span class="sxs-lookup"><span data-stu-id="be374-328">Join tables without payload (PJTs) frequently evolve to include payload.</span></span> <span data-ttu-id="be374-329">Começando com um nome descritivo de entidade, o nome não precisa ser alterado quando a tabela de junção é alterada.</span><span class="sxs-lookup"><span data-stu-id="be374-329">By starting with a descriptive entity name, the name doesn't need to change when the join table changes.</span></span> <span data-ttu-id="be374-330">O ideal é que a entidade de junção tenha seu próprio nome natural (possivelmente, uma única palavra) no domínio de negócios.</span><span class="sxs-lookup"><span data-stu-id="be374-330">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="be374-331">Por exemplo, Manuais e Clientes podem ser vinculados com uma entidade de junção chamada Ratings.</span><span class="sxs-lookup"><span data-stu-id="be374-331">For example, Books and Customers could be linked with a join entity called Ratings.</span></span> <span data-ttu-id="be374-332">Para a relação muitos para muitos de Instrutor para Cursos, `CourseAssignment` é preferível a `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="be374-332">For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="be374-333">Chave composta</span><span class="sxs-lookup"><span data-stu-id="be374-333">Composite key</span></span>

<span data-ttu-id="be374-334">As duas FKs em `CourseAssignment` (`InstructorID` e `CourseID`) juntas identificam exclusivamente cada linha da tabela `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="be374-334">The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table.</span></span> <span data-ttu-id="be374-335">`CourseAssignment` não exige um PK dedicado.</span><span class="sxs-lookup"><span data-stu-id="be374-335">`CourseAssignment` doesn't require a dedicated PK.</span></span> <span data-ttu-id="be374-336">As propriedades `InstructorID` e `CourseID` funcionam como uma PK composta.</span><span class="sxs-lookup"><span data-stu-id="be374-336">The `InstructorID` and `CourseID` properties function as a composite PK.</span></span> <span data-ttu-id="be374-337">A única maneira de especificar PKs compostas no EF Core é com a *API fluente*.</span><span class="sxs-lookup"><span data-stu-id="be374-337">The only way to specify composite PKs to EF Core is with the *fluent API*.</span></span> <span data-ttu-id="be374-338">A próxima seção mostra como configurar a PK composta.</span><span class="sxs-lookup"><span data-stu-id="be374-338">The next section shows how to configure the composite PK.</span></span>

<span data-ttu-id="be374-339">A chave composta garante que:</span><span class="sxs-lookup"><span data-stu-id="be374-339">The composite key ensures that:</span></span>

* <span data-ttu-id="be374-340">Várias linhas são permitidas para um curso.</span><span class="sxs-lookup"><span data-stu-id="be374-340">Multiple rows are allowed for one course.</span></span>
* <span data-ttu-id="be374-341">Várias linhas são permitidas para um instrutor.</span><span class="sxs-lookup"><span data-stu-id="be374-341">Multiple rows are allowed for one instructor.</span></span>
* <span data-ttu-id="be374-342">Não sejam permitidas várias linhas para o mesmo instrutor e curso.</span><span class="sxs-lookup"><span data-stu-id="be374-342">Multiple rows aren't allowed for the same instructor and course.</span></span>

<span data-ttu-id="be374-343">A entidade de junção `Enrollment` define sua própria PK e, portanto, duplicatas desse tipo são possíveis.</span><span class="sxs-lookup"><span data-stu-id="be374-343">The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible.</span></span> <span data-ttu-id="be374-344">Para impedir duplicatas como essas:</span><span class="sxs-lookup"><span data-stu-id="be374-344">To prevent such duplicates:</span></span>

* <span data-ttu-id="be374-345">Adicione um índice exclusivo nos campos de FK ou</span><span class="sxs-lookup"><span data-stu-id="be374-345">Add a unique index on the FK fields, or</span></span>
* <span data-ttu-id="be374-346">Configure `Enrollment` com uma chave primária composta semelhante a `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="be374-346">Configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="be374-347">Para obter mais informações, consulte [Índices](/ef/core/modeling/indexes).</span><span class="sxs-lookup"><span data-stu-id="be374-347">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-database-context"></a><span data-ttu-id="be374-348">Atualizar o contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="be374-348">Update the database context</span></span>

<span data-ttu-id="be374-349">Atualize *Data/SchoolContext.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="be374-349">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Data/SchoolContext.cs?highlight=15-18,25-31)]

<span data-ttu-id="be374-350">O código anterior adiciona novas entidades e configura a PK composta da entidade `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="be374-350">The preceding code adds the new entities and configures the `CourseAssignment` entity's composite PK.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="be374-351">Alternativa de API fluente para atributos</span><span class="sxs-lookup"><span data-stu-id="be374-351">Fluent API alternative to attributes</span></span>

<span data-ttu-id="be374-352">O método `OnModelCreating` no código anterior usa a *API fluente* para configurar o comportamento do EF Core.</span><span class="sxs-lookup"><span data-stu-id="be374-352">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="be374-353">A API é chamada "fluente" porque geralmente é usada pelo encadeamento de uma série de chamadas de método em uma única instrução.</span><span class="sxs-lookup"><span data-stu-id="be374-353">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="be374-354">O [seguinte código](/ef/core/modeling/#use-fluent-api-to-configure-a-model) é um exemplo da API fluente:</span><span class="sxs-lookup"><span data-stu-id="be374-354">The [following code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="be374-355">Neste tutorial, a API fluente é usada apenas para o mapeamento do banco de dados que não pode ser feito com atributos.</span><span class="sxs-lookup"><span data-stu-id="be374-355">In this tutorial, the fluent API is used only for database mapping that can't be done with attributes.</span></span> <span data-ttu-id="be374-356">No entanto, a API fluente pode especificar a maioria das regras de formatação, validação e mapeamento que pode ser feita com atributos.</span><span class="sxs-lookup"><span data-stu-id="be374-356">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="be374-357">Alguns atributos como `MinimumLength` não podem ser aplicados com a API fluente.</span><span class="sxs-lookup"><span data-stu-id="be374-357">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="be374-358">`MinimumLength` não altera o esquema; apenas aplica uma regra de validação de tamanho mínimo.</span><span class="sxs-lookup"><span data-stu-id="be374-358">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="be374-359">Alguns desenvolvedores preferem usar a API fluente exclusivamente para que possam manter suas classes de entidade "limpas".</span><span class="sxs-lookup"><span data-stu-id="be374-359">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="be374-360">Atributos e a API fluente podem ser combinados.</span><span class="sxs-lookup"><span data-stu-id="be374-360">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="be374-361">Há algumas configurações que apenas podem ser feitas com a API fluente (especificando uma PK composta).</span><span class="sxs-lookup"><span data-stu-id="be374-361">There are some configurations that can only be done with the fluent API (specifying a composite PK).</span></span> <span data-ttu-id="be374-362">Há algumas configurações que apenas podem ser feitas com atributos (`MinimumLength`).</span><span class="sxs-lookup"><span data-stu-id="be374-362">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="be374-363">A prática recomendada para uso de atributos ou da API fluente:</span><span class="sxs-lookup"><span data-stu-id="be374-363">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="be374-364">Escolha uma dessas duas abordagens.</span><span class="sxs-lookup"><span data-stu-id="be374-364">Choose one of these two approaches.</span></span>
* <span data-ttu-id="be374-365">Use a abordagem escolhida da forma mais consistente possível.</span><span class="sxs-lookup"><span data-stu-id="be374-365">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="be374-366">Alguns dos atributos usados neste tutorial são usados para:</span><span class="sxs-lookup"><span data-stu-id="be374-366">Some of the attributes used in this tutorial are used for:</span></span>

* <span data-ttu-id="be374-367">Somente validação (por exemplo, `MinimumLength`).</span><span class="sxs-lookup"><span data-stu-id="be374-367">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="be374-368">Apenas configuração do EF Core (por exemplo, `HasKey`).</span><span class="sxs-lookup"><span data-stu-id="be374-368">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="be374-369">Validação e configuração do EF Core (por exemplo, `[StringLength(50)]`).</span><span class="sxs-lookup"><span data-stu-id="be374-369">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="be374-370">Para obter mais informações sobre atributos vs. API fluente, consulte [Métodos de configuração](/ef/core/modeling/).</span><span class="sxs-lookup"><span data-stu-id="be374-370">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram"></a><span data-ttu-id="be374-371">Diagrama de entidade</span><span class="sxs-lookup"><span data-stu-id="be374-371">Entity diagram</span></span>

<span data-ttu-id="be374-372">A ilustração a seguir mostra o diagrama criado pelo EF Power Tools para o modelo Escola concluído.</span><span class="sxs-lookup"><span data-stu-id="be374-372">The following illustration shows the diagram that EF Power Tools create for the completed School model.</span></span>

![Diagrama de entidade](complex-data-model/_static/diagram.png)

<span data-ttu-id="be374-374">O diagrama anterior mostra:</span><span class="sxs-lookup"><span data-stu-id="be374-374">The preceding diagram shows:</span></span>

* <span data-ttu-id="be374-375">Várias linhas de relação um-para-muitos (1 para \*).</span><span class="sxs-lookup"><span data-stu-id="be374-375">Several one-to-many relationship lines (1 to \*).</span></span>
* <span data-ttu-id="be374-376">A linha de relação um para zero ou um (1 para 0..1) entre as entidades `Instructor` e `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="be374-376">The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.</span></span>
* <span data-ttu-id="be374-377">A linha de relação zero-ou-um-para-muitos (0..1 para \*) entre as entidades `Instructor` e `Department`.</span><span class="sxs-lookup"><span data-stu-id="be374-377">The zero-or-one-to-many relationship line (0..1 to \*) between the `Instructor` and `Department` entities.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="be374-378">Propagar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="be374-378">Seed the database</span></span>

<span data-ttu-id="be374-379">Atualize o código em *Data/DbInitializer.cs*:</span><span class="sxs-lookup"><span data-stu-id="be374-379">Update the code in *Data/DbInitializer.cs*:</span></span>

[!code-csharp[](intro/samples/cu30/Data/DbInitializer.cs)]

<span data-ttu-id="be374-380">O código anterior fornece dados de semente para as novas entidades.</span><span class="sxs-lookup"><span data-stu-id="be374-380">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="be374-381">A maioria desse código cria novos objetos de entidade e carrega dados de exemplo.</span><span class="sxs-lookup"><span data-stu-id="be374-381">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="be374-382">Os dados de exemplo são usados para teste.</span><span class="sxs-lookup"><span data-stu-id="be374-382">The sample data is used for testing.</span></span> <span data-ttu-id="be374-383">Consulte `Enrollments` e `CourseAssignments` para obter exemplos de como tabelas de junção muitos para muitos podem ser propagadas.</span><span class="sxs-lookup"><span data-stu-id="be374-383">See `Enrollments` and `CourseAssignments` for examples of how many-to-many join tables can be seeded.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="be374-384">Adicionar uma migração</span><span class="sxs-lookup"><span data-stu-id="be374-384">Add a migration</span></span>

<span data-ttu-id="be374-385">Compile o projeto.</span><span class="sxs-lookup"><span data-stu-id="be374-385">Build the project.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="be374-386">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="be374-386">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="be374-387">No PMC, execute o seguinte comando.</span><span class="sxs-lookup"><span data-stu-id="be374-387">In PMC, run the following command.</span></span>

```powershell
Add-Migration ComplexDataModel
```

<span data-ttu-id="be374-388">O comando anterior exibe um aviso sobre a possível perda de dados.</span><span class="sxs-lookup"><span data-stu-id="be374-388">The preceding command displays a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="be374-389">Se o comando `database update` é executado, o seguinte erro é produzido:</span><span class="sxs-lookup"><span data-stu-id="be374-389">If the `database update` command is run, the following error is produced:</span></span>

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

<span data-ttu-id="be374-390">Na próxima seção, você verá o que fazer sobre esse erro.</span><span class="sxs-lookup"><span data-stu-id="be374-390">In the next section, you see what to do about this error.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="be374-391">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="be374-391">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="be374-392">Se você adicionar uma migração e executar o comando `database update`, o seguinte erro será produzido:</span><span class="sxs-lookup"><span data-stu-id="be374-392">If you add a migration and run the `database update` command, the following error would be produced:</span></span>

```text
SQLite does not support this migration operation ('DropForeignKeyOperation').
For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
```

<span data-ttu-id="be374-393">Na próxima seção, você verá como evitar esse erro.</span><span class="sxs-lookup"><span data-stu-id="be374-393">In the next section, you see how to avoid this error.</span></span>

---

## <a name="apply-the-migration-or-drop-and-re-create"></a><span data-ttu-id="be374-394">Aplicar a migração ou remover e recriar</span><span class="sxs-lookup"><span data-stu-id="be374-394">Apply the migration or drop and re-create</span></span>

<span data-ttu-id="be374-395">Agora que você tem um banco de dados existente, precisa pensar sobre como aplicar as alterações a ele.</span><span class="sxs-lookup"><span data-stu-id="be374-395">Now that you have an existing database, you need to think about how to apply changes to it.</span></span> <span data-ttu-id="be374-396">Este tutorial mostra duas alternativas:</span><span class="sxs-lookup"><span data-stu-id="be374-396">This tutorial shows two alternatives:</span></span>

* <span data-ttu-id="be374-397">[Remover e recriar o banco de dados](#drop).</span><span class="sxs-lookup"><span data-stu-id="be374-397">[Drop and re-create the database](#drop).</span></span> <span data-ttu-id="be374-398">Escolha esta seção se você estiver usando o SQLite.</span><span class="sxs-lookup"><span data-stu-id="be374-398">Choose this section if you're using SQLite.</span></span>
* <span data-ttu-id="be374-399">[Aplicar a migração ao banco de dados existente](#applyexisting).</span><span class="sxs-lookup"><span data-stu-id="be374-399">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="be374-400">As instruções nesta seção funcionam apenas para SQL Server, **não para o SQLite**.</span><span class="sxs-lookup"><span data-stu-id="be374-400">The instructions in this section work for SQL Server only, **not for SQLite**.</span></span> 

<span data-ttu-id="be374-401">Qualquer opção funciona para o SQL Server.</span><span class="sxs-lookup"><span data-stu-id="be374-401">Either choice works for SQL Server.</span></span> <span data-ttu-id="be374-402">Embora o método apply-migration seja mais complexo e demorado, é a abordagem preferencial para ambientes de produção do mundo real.</span><span class="sxs-lookup"><span data-stu-id="be374-402">While the apply-migration method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span> 

<a name="drop"></a>

## <a name="drop-and-re-create-the-database"></a><span data-ttu-id="be374-403">Remover e recriar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="be374-403">Drop and re-create the database</span></span>

<span data-ttu-id="be374-404">[Ignore esta seção](#apply-the-migration) se você estiver usando SQL Server e desejar fazer a abordagem de migração de aplicação na seção a seguir.</span><span class="sxs-lookup"><span data-stu-id="be374-404">[Skip this section](#apply-the-migration) if you're using SQL Server and want to do the apply-migration approach in the following section.</span></span>

<span data-ttu-id="be374-405">Para forçar o EF Core a criar um novo banco de dados, remova e atualize o banco de dados:</span><span class="sxs-lookup"><span data-stu-id="be374-405">To force EF Core to create a new database, drop and update the database:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="be374-406">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="be374-406">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="be374-407">No **PMC** (Console do Gerenciador de Pacotes), execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="be374-407">In the **Package Manager Console** (PMC), run the following command:</span></span>

  ```powershell
  Drop-Database
  ```

* <span data-ttu-id="be374-408">Exclua a pasta *Migrations* e execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="be374-408">Delete the *Migrations* folder, then run the following command:</span></span>

  ```powershell
  Add-Migration InitialCreate
  Update-Database
  ```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="be374-409">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="be374-409">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="be374-410">Abra uma janela Comando e navegue para a pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="be374-410">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="be374-411">A pasta do projeto contém o arquivo *ContosoUniversity.csproj*.</span><span class="sxs-lookup"><span data-stu-id="be374-411">The project folder contains the *ContosoUniversity.csproj* file.</span></span>

* <span data-ttu-id="be374-412">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="be374-412">Run the following command:</span></span>

  ```console
  dotnet ef database drop --force
  ```

* <span data-ttu-id="be374-413">Exclua a pasta *Migrations* e execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="be374-413">Delete the *Migrations* folder, then run the following command:</span></span>

  ```console
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

<span data-ttu-id="be374-414">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="be374-414">Run the app.</span></span> <span data-ttu-id="be374-415">A execução do aplicativo executa o método `DbInitializer.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="be374-415">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="be374-416">O `DbInitializer.Initialize` preenche o novo banco de dados.</span><span class="sxs-lookup"><span data-stu-id="be374-416">The `DbInitializer.Initialize` populates the new database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="be374-417">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="be374-417">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="be374-418">Abra o banco de dados no SSOX:</span><span class="sxs-lookup"><span data-stu-id="be374-418">Open the database in SSOX:</span></span>

* <span data-ttu-id="be374-419">Se o SSOX for aberto anteriormente, clique no botão **Atualizar**.</span><span class="sxs-lookup"><span data-stu-id="be374-419">If SSOX was opened previously, click the **Refresh** button.</span></span>
* <span data-ttu-id="be374-420">Expanda o nó **Tabelas**.</span><span class="sxs-lookup"><span data-stu-id="be374-420">Expand the **Tables** node.</span></span> <span data-ttu-id="be374-421">As tabelas criadas são exibidas.</span><span class="sxs-lookup"><span data-stu-id="be374-421">The created tables are displayed.</span></span>

  ![Tabelas no SSOX](complex-data-model/_static/ssox-tables.png)

* <span data-ttu-id="be374-423">Examine a tabela **CourseAssignment**:</span><span class="sxs-lookup"><span data-stu-id="be374-423">Examine the **CourseAssignment** table:</span></span>

  * <span data-ttu-id="be374-424">Clique com o botão direito do mouse na tabela **CourseAssignment** e selecione **Exibir Dados**.</span><span class="sxs-lookup"><span data-stu-id="be374-424">Right-click the **CourseAssignment** table and select **View Data**.</span></span>
  * <span data-ttu-id="be374-425">Verifique se a tabela **CourseAssignment** contém dados.</span><span class="sxs-lookup"><span data-stu-id="be374-425">Verify the **CourseAssignment** table contains data.</span></span>

  ![Dados de CourseAssignment no SSOX](complex-data-model/_static/ssox-ci-data.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="be374-427">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="be374-427">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="be374-428">Use sua ferramenta SQLite para examinar o banco de dados:</span><span class="sxs-lookup"><span data-stu-id="be374-428">Use your SQLite tool to examine the database:</span></span>

* <span data-ttu-id="be374-429">Novas tabelas e colunas.</span><span class="sxs-lookup"><span data-stu-id="be374-429">New tables and columns.</span></span>
* <span data-ttu-id="be374-430">Dados propagados em tabelas, por exemplo, a tabela **CourseAssignment**.</span><span class="sxs-lookup"><span data-stu-id="be374-430">Seeded data in tables, for example the **CourseAssignment** table.</span></span>

---

<a name="applyexisting"></a>

## <a name="apply-the-migration"></a><span data-ttu-id="be374-431">Aplicar a migração</span><span class="sxs-lookup"><span data-stu-id="be374-431">Apply the migration</span></span>

<span data-ttu-id="be374-432">Esta seção é opcional.</span><span class="sxs-lookup"><span data-stu-id="be374-432">This section is optional.</span></span> <span data-ttu-id="be374-433">Estas etapas só funcionarão para o LocalDB do SQL Server e apenas se você tiver ignorado a seção [Remover e recriar o banco de dados](#drop) anterior.</span><span class="sxs-lookup"><span data-stu-id="be374-433">These steps work only for SQL Server LocalDB and only if you skipped the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="be374-434">Quando as migrações são executadas com os dados existentes, pode haver restrições de FK que não são atendidas com os dados existentes.</span><span class="sxs-lookup"><span data-stu-id="be374-434">When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data.</span></span> <span data-ttu-id="be374-435">Com os dados de produção, é necessário executar etapas para migrar os dados existentes.</span><span class="sxs-lookup"><span data-stu-id="be374-435">With production data, steps must be taken to migrate the existing data.</span></span> <span data-ttu-id="be374-436">Esta seção fornece um exemplo de correção de violações de restrição de FK.</span><span class="sxs-lookup"><span data-stu-id="be374-436">This section provides an example of fixing FK constraint violations.</span></span> <span data-ttu-id="be374-437">Não faça essas alterações de código sem um backup.</span><span class="sxs-lookup"><span data-stu-id="be374-437">Don't make these code changes without a backup.</span></span> <span data-ttu-id="be374-438">Não faça essas alterações de código se você tiver concluído a seção [Remover e recriar o banco de dados](#drop) anterior.</span><span class="sxs-lookup"><span data-stu-id="be374-438">Don't make these code changes if you completed the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="be374-439">O arquivo *{timestamp}_ComplexDataModel.cs* contém o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="be374-439">The *{timestamp}_ComplexDataModel.cs* file contains the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_DepartmentID)]

<span data-ttu-id="be374-440">O código anterior adiciona uma FK `DepartmentID` que não permite valor nulo à tabela `Course`.</span><span class="sxs-lookup"><span data-stu-id="be374-440">The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table.</span></span> <span data-ttu-id="be374-441">O banco de dados do tutorial anterior contém linhas em `Course` e, portanto, essa tabela não pode ser atualizada por migrações.</span><span class="sxs-lookup"><span data-stu-id="be374-441">The database from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.</span></span>

<span data-ttu-id="be374-442">Para fazer a migração `ComplexDataModel` funcionar com os dados existentes:</span><span class="sxs-lookup"><span data-stu-id="be374-442">To make the `ComplexDataModel` migration work with existing data:</span></span>

* <span data-ttu-id="be374-443">Altere o código para dar à nova coluna (`DepartmentID`) um valor padrão.</span><span class="sxs-lookup"><span data-stu-id="be374-443">Change the code to give the new column (`DepartmentID`) a default value.</span></span>
* <span data-ttu-id="be374-444">Crie um departamento fictício chamado "Temp" para atuar como o departamento padrão.</span><span class="sxs-lookup"><span data-stu-id="be374-444">Create a fake department named "Temp" to act as the default department.</span></span>

#### <a name="fix-the-foreign-key-constraints"></a><span data-ttu-id="be374-445">Corrigir as restrições de chave estrangeira</span><span class="sxs-lookup"><span data-stu-id="be374-445">Fix the foreign key constraints</span></span>

<span data-ttu-id="be374-446">Na classe de migração `ComplexDataModel`, atualize o método `Up`:</span><span class="sxs-lookup"><span data-stu-id="be374-446">In the `ComplexDataModel` migration class, update the `Up` method:</span></span>

* <span data-ttu-id="be374-447">Abra o arquivo *{timestamp}_ComplexDataModel.cs*.</span><span class="sxs-lookup"><span data-stu-id="be374-447">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>
* <span data-ttu-id="be374-448">Comente a linha de código que adiciona a coluna `DepartmentID` à tabela `Course`.</span><span class="sxs-lookup"><span data-stu-id="be374-448">Comment out the line of code that adds the `DepartmentID` column to the `Course` table.</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

<span data-ttu-id="be374-449">Adicione o código realçado a seguir.</span><span class="sxs-lookup"><span data-stu-id="be374-449">Add the following highlighted code.</span></span> <span data-ttu-id="be374-450">O novo código é inserido após o bloco `.CreateTable( name: "Department"`:</span><span class="sxs-lookup"><span data-stu-id="be374-450">The new code goes after the `.CreateTable( name: "Department"` block:</span></span>

<span data-ttu-id="be374-451">[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=23-31)]</span><span class="sxs-lookup"><span data-stu-id="be374-451">[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=23-31)]</span></span>

<span data-ttu-id="be374-452">Com as alterações anteriores, as linhas `Course` existentes estarão relacionadas ao departamento "Temp" após a execução do método `ComplexDataModel.Up`.</span><span class="sxs-lookup"><span data-stu-id="be374-452">With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel.Up` method runs.</span></span>

<span data-ttu-id="be374-453">A maneira de lidar com a situação mostrada aqui é simplificada para este tutorial.</span><span class="sxs-lookup"><span data-stu-id="be374-453">The way of handling the situation shown here is simplified for this tutorial.</span></span> <span data-ttu-id="be374-454">Um aplicativo de produção:</span><span class="sxs-lookup"><span data-stu-id="be374-454">A production app would:</span></span>

* <span data-ttu-id="be374-455">Inclui código ou scripts para adicionar linhas `Department` e linhas `Course` relacionadas às novas linhas `Department`.</span><span class="sxs-lookup"><span data-stu-id="be374-455">Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.</span></span>
* <span data-ttu-id="be374-456">Não usa o departamento "Temp" nem o valor padrão para `Course.DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="be374-456">Not use the "Temp" department or the default value for `Course.DepartmentID`.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="be374-457">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="be374-457">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="be374-458">No **PMC** (Console do Gerenciador de Pacotes), execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="be374-458">In the **Package Manager Console** (PMC), run the following command:</span></span>

  ```powershell
  Update-Database
  ```

<span data-ttu-id="be374-459">Como o método `DbInitializer.Initialize` foi projetado para funcionar apenas com um banco de dados vazio, use SSOX para excluir todas as linhas nas tabelas Student e Course.</span><span class="sxs-lookup"><span data-stu-id="be374-459">Because the `DbInitializer.Initialize` method is designed to work only with an empty database, use SSOX to delete all the rows in the Student and Course tables.</span></span> <span data-ttu-id="be374-460">(A exclusão em cascata cuidará da tabela de Registro.)</span><span class="sxs-lookup"><span data-stu-id="be374-460">(Cascade delete will take care of the Enrollment table.)</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="be374-461">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="be374-461">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="be374-462">Se você estiver usando o SQL Server LocalDB com o Visual Studio Code, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="be374-462">If you're using SQL Server LocalDB with Visual Studio Code, run the following command:</span></span>

  ```console
  dotnet ef database update
  ```

---

<span data-ttu-id="be374-463">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="be374-463">Run the app.</span></span> <span data-ttu-id="be374-464">A execução do aplicativo executa o método `DbInitializer.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="be374-464">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="be374-465">O `DbInitializer.Initialize` preenche o novo banco de dados.</span><span class="sxs-lookup"><span data-stu-id="be374-465">The `DbInitializer.Initialize` populates the new database.</span></span>

## <a name="next-steps"></a><span data-ttu-id="be374-466">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="be374-466">Next steps</span></span>

<span data-ttu-id="be374-467">Os próximos dois tutoriais mostram como ler e atualizar dados relacionados.</span><span class="sxs-lookup"><span data-stu-id="be374-467">The next two tutorials show how to read and update related data.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="be374-468">[Tutorial anterior](xref:data/ef-rp/migrations)
> [Próximo tutorial](xref:data/ef-rp/read-related-data)</span><span class="sxs-lookup"><span data-stu-id="be374-468">[Previous tutorial](xref:data/ef-rp/migrations)
[Next tutorial](xref:data/ef-rp/read-related-data)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="be374-469">Os tutoriais anteriores trabalharam com um modelo de dados básico composto por três entidades.</span><span class="sxs-lookup"><span data-stu-id="be374-469">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="be374-470">Neste tutorial:</span><span class="sxs-lookup"><span data-stu-id="be374-470">In this tutorial:</span></span>

* <span data-ttu-id="be374-471">Mais entidades e relações são adicionadas.</span><span class="sxs-lookup"><span data-stu-id="be374-471">More entities and relationships are added.</span></span>
* <span data-ttu-id="be374-472">O modelo de dados é personalizado com a especificação das regras de formatação, validação e mapeamento de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="be374-472">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="be374-473">As classes de entidade para o modelo de dados concluído são mostradas na seguinte ilustração:</span><span class="sxs-lookup"><span data-stu-id="be374-473">The entity classes for the completed data model are shown in the following illustration:</span></span>

![Diagrama de entidade](complex-data-model/_static/diagram.png)

<span data-ttu-id="be374-475">Caso tenha problemas que não consiga resolver, baixe o [aplicativo concluído](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="be374-475">If you run into problems you can't solve, download the [completed app](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span>

## <a name="customize-the-data-model-with-attributes"></a><span data-ttu-id="be374-476">Personalizar o modelo de dados com atributos</span><span class="sxs-lookup"><span data-stu-id="be374-476">Customize the data model with attributes</span></span>

<span data-ttu-id="be374-477">Nesta seção, o modelo de dados é personalizado com atributos.</span><span class="sxs-lookup"><span data-stu-id="be374-477">In this section, the data model is customized using attributes.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="be374-478">O atributo DataType</span><span class="sxs-lookup"><span data-stu-id="be374-478">The DataType attribute</span></span>

<span data-ttu-id="be374-479">As páginas de alunos atualmente exibem a hora da data de registro.</span><span class="sxs-lookup"><span data-stu-id="be374-479">The student pages currently displays the time of the enrollment date.</span></span> <span data-ttu-id="be374-480">Normalmente, os campos de data mostram apenas a data e não a hora.</span><span class="sxs-lookup"><span data-stu-id="be374-480">Typically, date fields show only the date and not the time.</span></span>

<span data-ttu-id="be374-481">Atualize *Models/Student.cs* com o seguinte código realçado:</span><span class="sxs-lookup"><span data-stu-id="be374-481">Update *Models/Student.cs* with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

<span data-ttu-id="be374-482">O atributo [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) especifica um tipo de dados mais específico do que o tipo intrínseco de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="be374-482">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="be374-483">Neste caso, apenas a data deve ser exibida, não a data e a hora.</span><span class="sxs-lookup"><span data-stu-id="be374-483">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="be374-484">A [Enumeração DataType](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) fornece muitos tipos de dados, como Date, Time, PhoneNumber, Currency, EmailAddress, etc. O atributo `DataType` também pode permitir que o aplicativo forneça automaticamente recursos específicos a um tipo.</span><span class="sxs-lookup"><span data-stu-id="be374-484">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="be374-485">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="be374-485">For example:</span></span>

* <span data-ttu-id="be374-486">O link `mailto:` é criado automaticamente para `DataType.EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="be374-486">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="be374-487">O seletor de data é fornecido para `DataType.Date` na maioria dos navegadores.</span><span class="sxs-lookup"><span data-stu-id="be374-487">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="be374-488">O atributo `DataType` emite atributos `data-` HTML 5 (pronunciados “data dash”) que são consumidos pelos navegadores HTML 5.</span><span class="sxs-lookup"><span data-stu-id="be374-488">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers consume.</span></span> <span data-ttu-id="be374-489">Os atributos `DataType` não fornecem validação.</span><span class="sxs-lookup"><span data-stu-id="be374-489">The `DataType` attributes don't provide validation.</span></span>

<span data-ttu-id="be374-490">`DataType.Date` não especifica o formato da data exibida.</span><span class="sxs-lookup"><span data-stu-id="be374-490">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="be374-491">Por padrão, o campo de dados é exibido de acordo com os formatos padrão com base nas [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support) do servidor.</span><span class="sxs-lookup"><span data-stu-id="be374-491">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="be374-492">O atributo `DisplayFormat` é usado para especificar explicitamente o formato de data:</span><span class="sxs-lookup"><span data-stu-id="be374-492">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="be374-493">A configuração `ApplyFormatInEditMode` especifica que a formatação também deve ser aplicada à interface do usuário de edição.</span><span class="sxs-lookup"><span data-stu-id="be374-493">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="be374-494">Alguns campos não devem usar `ApplyFormatInEditMode`.</span><span class="sxs-lookup"><span data-stu-id="be374-494">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="be374-495">Por exemplo, o símbolo de moeda geralmente não deve ser exibido em uma caixa de texto de edição.</span><span class="sxs-lookup"><span data-stu-id="be374-495">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="be374-496">O atributo `DisplayFormat` pode ser usado por si só.</span><span class="sxs-lookup"><span data-stu-id="be374-496">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="be374-497">Geralmente, é uma boa ideia usar o atributo `DataType` com o atributo `DisplayFormat`.</span><span class="sxs-lookup"><span data-stu-id="be374-497">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="be374-498">O atributo `DataType` transmite a semântica dos dados em vez de como renderizá-los em uma tela.</span><span class="sxs-lookup"><span data-stu-id="be374-498">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="be374-499">O atributo `DataType` oferece os seguintes benefícios que não estão disponíveis em `DisplayFormat`:</span><span class="sxs-lookup"><span data-stu-id="be374-499">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="be374-500">O navegador pode habilitar recursos do HTML5.</span><span class="sxs-lookup"><span data-stu-id="be374-500">The browser can enable HTML5 features.</span></span> <span data-ttu-id="be374-501">Por exemplo, mostra um controle de calendário, o símbolo de moeda apropriado à localidade, links de email, validação de entrada do lado do cliente, etc.</span><span class="sxs-lookup"><span data-stu-id="be374-501">For example, show a calendar control, the locale-appropriate currency symbol, email links, client-side input validation, etc.</span></span>
* <span data-ttu-id="be374-502">Por padrão, o navegador renderiza os dados usando o formato correto de acordo com a localidade.</span><span class="sxs-lookup"><span data-stu-id="be374-502">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="be374-503">Para obter mais informações, consulte a [documentação do Auxiliar de Marcação \<input>](xref:mvc/views/working-with-forms#the-input-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="be374-503">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

<span data-ttu-id="be374-504">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="be374-504">Run the app.</span></span> <span data-ttu-id="be374-505">Navegue para a página Índice de Alunos.</span><span class="sxs-lookup"><span data-stu-id="be374-505">Navigate to the Students Index page.</span></span> <span data-ttu-id="be374-506">As horas não são mais exibidas.</span><span class="sxs-lookup"><span data-stu-id="be374-506">Times are no longer displayed.</span></span> <span data-ttu-id="be374-507">Cada exibição que usa o modelo `Student` exibe a data sem a hora.</span><span class="sxs-lookup"><span data-stu-id="be374-507">Every view that uses the `Student` model displays the date without time.</span></span>

![Página Índice de Alunos mostrando datas sem horas](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a><span data-ttu-id="be374-509">O atributo StringLength</span><span class="sxs-lookup"><span data-stu-id="be374-509">The StringLength attribute</span></span>

<span data-ttu-id="be374-510">Regras de validação de dados e mensagens de erro de validação podem ser especificadas com atributos.</span><span class="sxs-lookup"><span data-stu-id="be374-510">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="be374-511">O atributo [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) especifica o tamanho mínimo e máximo de caracteres permitidos em um campo de dados.</span><span class="sxs-lookup"><span data-stu-id="be374-511">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="be374-512">O atributo `StringLength` também fornece a validação do lado do cliente e do servidor.</span><span class="sxs-lookup"><span data-stu-id="be374-512">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="be374-513">O valor mínimo não tem impacto sobre o esquema de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="be374-513">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="be374-514">Atualize o modelo `Student` com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="be374-514">Update the `Student` model with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

<span data-ttu-id="be374-515">O código anterior limita os nomes a, no máximo, 50 caracteres.</span><span class="sxs-lookup"><span data-stu-id="be374-515">The preceding code limits names to no more than 50 characters.</span></span> <span data-ttu-id="be374-516">O atributo `StringLength` não impede que um usuário insira um espaço em branco em um nome.</span><span class="sxs-lookup"><span data-stu-id="be374-516">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="be374-517">O atributo [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) é usado para aplicar restrições à entrada.</span><span class="sxs-lookup"><span data-stu-id="be374-517">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) attribute is used to apply restrictions to the input.</span></span> <span data-ttu-id="be374-518">Por exemplo, o seguinte código exige que o primeiro caractere esteja em maiúscula e os caracteres restantes estejam em ordem alfabética:</span><span class="sxs-lookup"><span data-stu-id="be374-518">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

<span data-ttu-id="be374-519">Execute o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="be374-519">Run the app:</span></span>

* <span data-ttu-id="be374-520">Navegue para a página Alunos.</span><span class="sxs-lookup"><span data-stu-id="be374-520">Navigate to the Students page.</span></span>
* <span data-ttu-id="be374-521">Selecione **Criar Novo** e insira um nome com mais de 50 caracteres.</span><span class="sxs-lookup"><span data-stu-id="be374-521">Select **Create New**, and enter a name longer than 50 characters.</span></span>
* <span data-ttu-id="be374-522">Selecione **Criar** e a validação do lado do cliente mostrará uma mensagem de erro.</span><span class="sxs-lookup"><span data-stu-id="be374-522">Select **Create**, client-side validation shows an error message.</span></span>

![Página Índice de Alunos mostrando erros de tamanho de cadeia de caracteres](complex-data-model/_static/string-length-errors.png)

<span data-ttu-id="be374-524">No **SSOX** (Pesquisador de Objetos do SQL Server), abra o designer de tabela Aluno clicando duas vezes na tabela **Aluno**.</span><span class="sxs-lookup"><span data-stu-id="be374-524">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![Tabela Alunos no SSOX antes das migrações](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="be374-526">A imagem anterior mostra o esquema para a tabela `Student`.</span><span class="sxs-lookup"><span data-stu-id="be374-526">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="be374-527">Os campos de nome têm o tipo `nvarchar(MAX)` porque as migrações não foram executadas no BD.</span><span class="sxs-lookup"><span data-stu-id="be374-527">The name fields have type `nvarchar(MAX)` because migrations has not been run on the DB.</span></span> <span data-ttu-id="be374-528">Quando as migrações forem executadas mais adiante neste tutorial, os campos de nome se tornarão `nvarchar(50)`.</span><span class="sxs-lookup"><span data-stu-id="be374-528">When migrations are run later in this tutorial, the name fields become `nvarchar(50)`.</span></span>

### <a name="the-column-attribute"></a><span data-ttu-id="be374-529">O atributo Column</span><span class="sxs-lookup"><span data-stu-id="be374-529">The Column attribute</span></span>

<span data-ttu-id="be374-530">Os atributos podem controlar como as classes e propriedades são mapeadas para o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="be374-530">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="be374-531">Nesta seção, o atributo `Column` é usado para mapear o nome da propriedade `FirstMidName` como "FirstName" no BD.</span><span class="sxs-lookup"><span data-stu-id="be374-531">In this section, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the DB.</span></span>

<span data-ttu-id="be374-532">Quando o BD é criado, os nomes de propriedade no modelo são usados para nomes de coluna (exceto quando o atributo `Column` é usado).</span><span class="sxs-lookup"><span data-stu-id="be374-532">When the DB is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span>

<span data-ttu-id="be374-533">O modelo `Student` usa `FirstMidName` para o campo de nome porque o campo também pode conter um sobrenome.</span><span class="sxs-lookup"><span data-stu-id="be374-533">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="be374-534">Atualize o arquivo *Student.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="be374-534">Update the *Student.cs* file with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Column&highlight=4,14)]

<span data-ttu-id="be374-535">Com a alteração anterior, `Student.FirstMidName` no aplicativo é mapeado para a coluna `FirstName` da tabela `Student`.</span><span class="sxs-lookup"><span data-stu-id="be374-535">With the preceding change, `Student.FirstMidName` in the app maps to the `FirstName` column of the `Student` table.</span></span>

<span data-ttu-id="be374-536">A adição do atributo `Column` altera o modelo que dá suporte ao `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="be374-536">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="be374-537">O modelo que dá suporte ao `SchoolContext` não corresponde mais ao banco de dados.</span><span class="sxs-lookup"><span data-stu-id="be374-537">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="be374-538">Se o aplicativo for executado antes da aplicação das migrações, a seguinte exceção será gerada:</span><span class="sxs-lookup"><span data-stu-id="be374-538">If the app is run before applying migrations, the following exception is generated:</span></span>

```SQL
SqlException: Invalid column name 'FirstName'.
```

<span data-ttu-id="be374-539">Para atualizar o BD:</span><span class="sxs-lookup"><span data-stu-id="be374-539">To update the DB:</span></span>

* <span data-ttu-id="be374-540">Compile o projeto.</span><span class="sxs-lookup"><span data-stu-id="be374-540">Build the project.</span></span>
* <span data-ttu-id="be374-541">Abra uma janela Comando na pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="be374-541">Open a command window in the project folder.</span></span> <span data-ttu-id="be374-542">Insira os seguintes comandos para criar uma nova migração e atualizar o BD:</span><span class="sxs-lookup"><span data-stu-id="be374-542">Enter the following commands to create a new migration and update the DB:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="be374-543">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="be374-543">Visual Studio</span></span>](#tab/visual-studio)

```PMC
Add-Migration ColumnFirstName
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="be374-544">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="be374-544">Visual Studio Code</span></span>](#tab/visual-studio-code)

```console
dotnet ef migrations add ColumnFirstName
dotnet ef database update
```

---

<span data-ttu-id="be374-545">O comando `migrations add ColumnFirstName` gera a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="be374-545">The `migrations add ColumnFirstName` command generates the following warning message:</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
```

<span data-ttu-id="be374-546">O aviso é gerado porque os campos de nome agora estão limitados a 50 caracteres.</span><span class="sxs-lookup"><span data-stu-id="be374-546">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="be374-547">Se um nome no BD tiver mais de 50 caracteres, o 51º caractere até o último caractere serão perdidos.</span><span class="sxs-lookup"><span data-stu-id="be374-547">If a name in the DB had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="be374-548">Teste o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="be374-548">Test the app.</span></span>

<span data-ttu-id="be374-549">Abra a tabela Alunos no SSOX:</span><span class="sxs-lookup"><span data-stu-id="be374-549">Open the Student table in SSOX:</span></span>

![Tabela Alunos no SSOX após as migrações](complex-data-model/_static/ssox-after-migration.png)

<span data-ttu-id="be374-551">Antes de a migração ser aplicada, as colunas de nome eram do tipo [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span><span class="sxs-lookup"><span data-stu-id="be374-551">Before migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="be374-552">As colunas de nome agora são `nvarchar(50)`.</span><span class="sxs-lookup"><span data-stu-id="be374-552">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="be374-553">O nome da coluna foi alterado de `FirstMidName` para `FirstName`.</span><span class="sxs-lookup"><span data-stu-id="be374-553">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

> [!Note]
> <span data-ttu-id="be374-554">Na seção a seguir, a criação do aplicativo em alguns estágios gera erros do compilador.</span><span class="sxs-lookup"><span data-stu-id="be374-554">In the following section, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="be374-555">As instruções especificam quando compilar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="be374-555">The instructions specify when to build the app.</span></span>

## <a name="student-entity-update"></a><span data-ttu-id="be374-556">Atualização da entidade Student</span><span class="sxs-lookup"><span data-stu-id="be374-556">Student entity update</span></span>

![Entidade Student](complex-data-model/_static/student-entity.png)

<span data-ttu-id="be374-558">Atualize *Models/Student.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="be374-558">Update *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a><span data-ttu-id="be374-559">O atributo Required</span><span class="sxs-lookup"><span data-stu-id="be374-559">The Required attribute</span></span>

<span data-ttu-id="be374-560">O atributo `Required` torna as propriedades de nome campos obrigatórios.</span><span class="sxs-lookup"><span data-stu-id="be374-560">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="be374-561">O atributo `Required` não é necessário para tipos que não permitem valor nulo, como tipos de valor (`DateTime`, `int`, `double`, etc.).</span><span class="sxs-lookup"><span data-stu-id="be374-561">The `Required` attribute isn't needed for non-nullable types such as value types (`DateTime`, `int`, `double`, etc.).</span></span> <span data-ttu-id="be374-562">Tipos que não podem ser nulos são tratados automaticamente como campos obrigatórios.</span><span class="sxs-lookup"><span data-stu-id="be374-562">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="be374-563">O atributo `Required` pode ser substituído por um parâmetro de tamanho mínimo no atributo `StringLength`:</span><span class="sxs-lookup"><span data-stu-id="be374-563">The `Required` attribute could be replaced with a minimum length parameter in the `StringLength` attribute:</span></span>

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a><span data-ttu-id="be374-564">O atributo Display</span><span class="sxs-lookup"><span data-stu-id="be374-564">The Display attribute</span></span>

<span data-ttu-id="be374-565">O atributo `Display` especifica que a legenda para as caixas de texto deve ser "Nome", "Sobrenome", "Nome Completo" e "Data de Registro".</span><span class="sxs-lookup"><span data-stu-id="be374-565">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="be374-566">As legendas padrão não tinham nenhum espaço entre as palavras, por exemplo, "Lastname".</span><span class="sxs-lookup"><span data-stu-id="be374-566">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="be374-567">A propriedade calculada FullName</span><span class="sxs-lookup"><span data-stu-id="be374-567">The FullName calculated property</span></span>

<span data-ttu-id="be374-568">`FullName` é uma propriedade calculada que retorna um valor criado pela concatenação de duas outras propriedades.</span><span class="sxs-lookup"><span data-stu-id="be374-568">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="be374-569">`FullName` não pode ser definido; ele apenas tem um acessador get.</span><span class="sxs-lookup"><span data-stu-id="be374-569">`FullName` cannot be set, it has only a get accessor.</span></span> <span data-ttu-id="be374-570">Nenhuma coluna `FullName` é criada no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="be374-570">No `FullName` column is created in the database.</span></span>

## <a name="create-the-instructor-entity"></a><span data-ttu-id="be374-571">Criar a entidade Instructor</span><span class="sxs-lookup"><span data-stu-id="be374-571">Create the Instructor Entity</span></span>

![Entidade Instructor](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="be374-573">Crie *Models/Instructor.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="be374-573">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Instructor.cs)]

<span data-ttu-id="be374-574">Vários atributos podem estar em uma linha.</span><span class="sxs-lookup"><span data-stu-id="be374-574">Multiple attributes can be on one line.</span></span> <span data-ttu-id="be374-575">Os atributos `HireDate` podem ser escritos da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="be374-575">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a><span data-ttu-id="be374-576">As propriedades de navegação CourseAssignments e OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="be374-576">The CourseAssignments and OfficeAssignment navigation properties</span></span>

<span data-ttu-id="be374-577">As propriedades `CourseAssignments` e `OfficeAssignment` são propriedades de navegação.</span><span class="sxs-lookup"><span data-stu-id="be374-577">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="be374-578">Um instrutor pode ministrar qualquer quantidade de cursos e, portanto, `CourseAssignments` é definido como uma coleção.</span><span class="sxs-lookup"><span data-stu-id="be374-578">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="be374-579">Se uma propriedade de navegação armazenar várias entidades:</span><span class="sxs-lookup"><span data-stu-id="be374-579">If a navigation property holds multiple entities:</span></span>

* <span data-ttu-id="be374-580">Ele deve ser um tipo de lista no qual as entradas possam ser adicionadas, excluídas e atualizadas.</span><span class="sxs-lookup"><span data-stu-id="be374-580">It must be a list type where the entries can be added, deleted, and updated.</span></span>

<span data-ttu-id="be374-581">Os tipos de propriedade de navegação incluem:</span><span class="sxs-lookup"><span data-stu-id="be374-581">Navigation property types include:</span></span>

* `ICollection<T>`
* `List<T>`
* `HashSet<T>`

<span data-ttu-id="be374-582">Se `ICollection<T>` for especificado, o EF Core criará uma coleção `HashSet<T>` por padrão.</span><span class="sxs-lookup"><span data-stu-id="be374-582">If `ICollection<T>` is specified, EF Core creates a `HashSet<T>` collection by default.</span></span>

<span data-ttu-id="be374-583">A entidade `CourseAssignment` é explicada na seção sobre relações muitos para muitos.</span><span class="sxs-lookup"><span data-stu-id="be374-583">The `CourseAssignment` entity is explained in the section on many-to-many relationships.</span></span>

<span data-ttu-id="be374-584">Regras de negócio do Contoso University indicam que um instrutor pode ter, no máximo, um escritório.</span><span class="sxs-lookup"><span data-stu-id="be374-584">Contoso University business rules state that an instructor can have at most one office.</span></span> <span data-ttu-id="be374-585">A propriedade `OfficeAssignment` contém uma única entidade `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="be374-585">The `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="be374-586">`OfficeAssignment` será nulo se nenhum escritório for atribuído.</span><span class="sxs-lookup"><span data-stu-id="be374-586">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-the-officeassignment-entity"></a><span data-ttu-id="be374-587">Criar a entidade OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="be374-587">Create the OfficeAssignment entity</span></span>

![Entidade OfficeAssignment](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="be374-589">Crie *Models/OfficeAssignment.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="be374-589">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="be374-590">O atributo Key</span><span class="sxs-lookup"><span data-stu-id="be374-590">The Key attribute</span></span>

<span data-ttu-id="be374-591">O atributo `[Key]` é usado para identificar uma propriedade como a PK (chave primária) quando o nome da propriedade é algo diferente de classnameID ou ID.</span><span class="sxs-lookup"><span data-stu-id="be374-591">The `[Key]` attribute is used to identify a property as the primary key (PK) when the property name is something other than classnameID or ID.</span></span>

<span data-ttu-id="be374-592">Há uma relação um para zero ou um entre as entidades `Instructor` e `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="be374-592">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="be374-593">Uma atribuição de escritório existe apenas em relação ao instrutor ao qual ela é atribuída.</span><span class="sxs-lookup"><span data-stu-id="be374-593">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="be374-594">A PK `OfficeAssignment` também é a FK (chave estrangeira) da entidade `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="be374-594">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span> <span data-ttu-id="be374-595">O EF Core não pode reconhecer `InstructorID` automaticamente como o PK de `OfficeAssignment` porque:</span><span class="sxs-lookup"><span data-stu-id="be374-595">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because:</span></span>

* <span data-ttu-id="be374-596">`InstructorID` não segue a convenção de nomenclatura de ID nem de classnameID.</span><span class="sxs-lookup"><span data-stu-id="be374-596">`InstructorID` doesn't follow the ID or classnameID naming convention.</span></span>

<span data-ttu-id="be374-597">Portanto, o atributo `Key` é usado para identificar `InstructorID` como a PK:</span><span class="sxs-lookup"><span data-stu-id="be374-597">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="be374-598">Por padrão, o EF Core trata a chave como não gerada pelo banco de dados porque a coluna destina-se a uma relação de identificação.</span><span class="sxs-lookup"><span data-stu-id="be374-598">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="be374-599">A propriedade de navegação Instructor</span><span class="sxs-lookup"><span data-stu-id="be374-599">The Instructor navigation property</span></span>

<span data-ttu-id="be374-600">A propriedade de navegação `OfficeAssignment` da entidade `Instructor` permite valor nulo porque:</span><span class="sxs-lookup"><span data-stu-id="be374-600">The `OfficeAssignment` navigation property for the `Instructor` entity is nullable because:</span></span>

* <span data-ttu-id="be374-601">Tipos de referência (como classes que permitem valor nulo).</span><span class="sxs-lookup"><span data-stu-id="be374-601">Reference types (such as classes are nullable).</span></span>
* <span data-ttu-id="be374-602">Um instrutor pode não ter uma atribuição de escritório.</span><span class="sxs-lookup"><span data-stu-id="be374-602">An instructor might not have an office assignment.</span></span>

<span data-ttu-id="be374-603">A entidade `OfficeAssignment` tem uma propriedade de navegação `Instructor` que não permite valor nulo porque:</span><span class="sxs-lookup"><span data-stu-id="be374-603">The `OfficeAssignment` entity has a non-nullable `Instructor` navigation property because:</span></span>

* <span data-ttu-id="be374-604">`InstructorID` não permite valor nulo.</span><span class="sxs-lookup"><span data-stu-id="be374-604">`InstructorID` is non-nullable.</span></span>
* <span data-ttu-id="be374-605">Uma atribuição de escritório não pode existir sem um instrutor.</span><span class="sxs-lookup"><span data-stu-id="be374-605">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="be374-606">Quando uma entidade `Instructor` tem uma entidade `OfficeAssignment` relacionada, cada entidade tem uma referência à outra em sua propriedade de navegação.</span><span class="sxs-lookup"><span data-stu-id="be374-606">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

<span data-ttu-id="be374-607">O atributo `[Required]` pode ser aplicado à propriedade de navegação `Instructor`:</span><span class="sxs-lookup"><span data-stu-id="be374-607">The `[Required]` attribute could be applied to the `Instructor` navigation property:</span></span>

```csharp
[Required]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="be374-608">O código anterior especifica que deve haver um instrutor relacionado.</span><span class="sxs-lookup"><span data-stu-id="be374-608">The preceding code specifies that there must be a related instructor.</span></span> <span data-ttu-id="be374-609">O código anterior é desnecessário porque a chave estrangeira `InstructorID` (que também é a PK) não permite valor nulo.</span><span class="sxs-lookup"><span data-stu-id="be374-609">The preceding code is unnecessary because the `InstructorID` foreign key (which is also the PK) is non-nullable.</span></span>

## <a name="modify-the-course-entity"></a><span data-ttu-id="be374-610">Modificar a entidade Course</span><span class="sxs-lookup"><span data-stu-id="be374-610">Modify the Course Entity</span></span>

![Entidade Course](complex-data-model/_static/course-entity.png)

<span data-ttu-id="be374-612">Atualize *Models/Course.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="be374-612">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="be374-613">A entidade `Course` tem uma propriedade de FK (chave estrangeira) `DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="be374-613">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="be374-614">`DepartmentID` aponta para a entidade `Department` relacionada.</span><span class="sxs-lookup"><span data-stu-id="be374-614">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="be374-615">A entidade `Course` tem uma propriedade de navegação `Department`.</span><span class="sxs-lookup"><span data-stu-id="be374-615">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="be374-616">O EF Core não exige uma propriedade de FK para um modelo de dados quando o modelo tem uma propriedade de navegação para uma entidade relacionada.</span><span class="sxs-lookup"><span data-stu-id="be374-616">EF Core doesn't require a FK property for a data model when the model has a navigation property for a related entity.</span></span>

<span data-ttu-id="be374-617">O EF Core cria automaticamente FKs no banco de dados sempre que forem necessárias.</span><span class="sxs-lookup"><span data-stu-id="be374-617">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="be374-618">O EF Core cria [propriedades de sombra](/ef/core/modeling/shadow-properties) para FKs criadas automaticamente.</span><span class="sxs-lookup"><span data-stu-id="be374-618">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="be374-619">Ter a FK no modelo de dados pode tornar as atualizações mais simples e mais eficientes.</span><span class="sxs-lookup"><span data-stu-id="be374-619">Having the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="be374-620">Por exemplo, considere um modelo em que a propriedade de FK `DepartmentID` *não* é incluída.</span><span class="sxs-lookup"><span data-stu-id="be374-620">For example, consider a model where the FK property `DepartmentID` is *not* included.</span></span> <span data-ttu-id="be374-621">Quando uma entidade de curso é buscada para editar:</span><span class="sxs-lookup"><span data-stu-id="be374-621">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="be374-622">A entidade `Department` será nula se não for carregada de forma explícita.</span><span class="sxs-lookup"><span data-stu-id="be374-622">The `Department` entity is null if it's not explicitly loaded.</span></span>
* <span data-ttu-id="be374-623">Para atualizar a entidade de curso, a entidade `Department` primeiro deve ser buscada.</span><span class="sxs-lookup"><span data-stu-id="be374-623">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="be374-624">Quando a propriedade de FK `DepartmentID` está incluída no modelo de dados, não é necessário buscar a entidade `Department` antes de uma atualização.</span><span class="sxs-lookup"><span data-stu-id="be374-624">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="be374-625">O atributo DatabaseGenerated</span><span class="sxs-lookup"><span data-stu-id="be374-625">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="be374-626">O atributo `[DatabaseGenerated(DatabaseGeneratedOption.None)]` especifica que a PK é fornecida pelo aplicativo em vez de ser gerada pelo banco de dados.</span><span class="sxs-lookup"><span data-stu-id="be374-626">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="be374-627">Por padrão, o EF Core supõe que os valores de PK sejam gerados pelo BD.</span><span class="sxs-lookup"><span data-stu-id="be374-627">By default, EF Core assumes that PK values are generated by the DB.</span></span> <span data-ttu-id="be374-628">Os valores de PK gerados pelo BD geralmente são a melhor abordagem.</span><span class="sxs-lookup"><span data-stu-id="be374-628">DB generated PK values is generally the best approach.</span></span> <span data-ttu-id="be374-629">Para entidades `Course`, o usuário especifica o PK.</span><span class="sxs-lookup"><span data-stu-id="be374-629">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="be374-630">Por exemplo, um número de curso, como uma série 1000 para o departamento de matemática e uma série 2000 para o departamento em inglês.</span><span class="sxs-lookup"><span data-stu-id="be374-630">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="be374-631">O atributo `DatabaseGenerated` também pode ser usado para gerar valores padrão.</span><span class="sxs-lookup"><span data-stu-id="be374-631">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="be374-632">Por exemplo, o BD pode gerar automaticamente um campo de data para registrar a data em que uma linha foi criada ou atualizada.</span><span class="sxs-lookup"><span data-stu-id="be374-632">For example, the DB can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="be374-633">Para obter mais informações, consulte [Propriedades geradas](/ef/core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="be374-633">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="be374-634">Propriedades de navegação e de chave estrangeira</span><span class="sxs-lookup"><span data-stu-id="be374-634">Foreign key and navigation properties</span></span>

<span data-ttu-id="be374-635">As propriedades de navegação e de FK (chave estrangeira) na entidade `Course` refletem as seguintes relações:</span><span class="sxs-lookup"><span data-stu-id="be374-635">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="be374-636">Um curso é atribuído a um departamento; portanto, há uma FK `DepartmentID` e uma propriedade de navegação `Department`.</span><span class="sxs-lookup"><span data-stu-id="be374-636">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="be374-637">Um curso pode ter qualquer quantidade de estudantes inscritos; portanto, a propriedade de navegação `Enrollments` é uma coleção:</span><span class="sxs-lookup"><span data-stu-id="be374-637">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="be374-638">Um curso pode ser ministrado por vários instrutores; portanto, a propriedade de navegação `CourseAssignments` é uma coleção:</span><span class="sxs-lookup"><span data-stu-id="be374-638">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection:</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="be374-639">`CourseAssignment` é explicado [posteriormente](#many-to-many-relationships).</span><span class="sxs-lookup"><span data-stu-id="be374-639">`CourseAssignment` is explained [later](#many-to-many-relationships).</span></span>

## <a name="create-the-department-entity"></a><span data-ttu-id="be374-640">Criar a entidade Department</span><span class="sxs-lookup"><span data-stu-id="be374-640">Create the Department entity</span></span>

![Entidade Department](complex-data-model/_static/department-entity.png)

<span data-ttu-id="be374-642">Crie *Models/Department.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="be374-642">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a><span data-ttu-id="be374-643">O atributo Column</span><span class="sxs-lookup"><span data-stu-id="be374-643">The Column attribute</span></span>

<span data-ttu-id="be374-644">Anteriormente, o atributo `Column` foi usado para alterar o mapeamento de nome de coluna.</span><span class="sxs-lookup"><span data-stu-id="be374-644">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="be374-645">No código da entidade `Department`, o atributo `Column` é usado para alterar o mapeamento de tipo de dados SQL.</span><span class="sxs-lookup"><span data-stu-id="be374-645">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="be374-646">A coluna `Budget` é definida usando o tipo de dinheiro do SQL Server no BD:</span><span class="sxs-lookup"><span data-stu-id="be374-646">The `Budget` column is defined using the SQL Server money type in the DB:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="be374-647">Em geral, o mapeamento de coluna não é necessário.</span><span class="sxs-lookup"><span data-stu-id="be374-647">Column mapping is generally not required.</span></span> <span data-ttu-id="be374-648">Em geral, o EF Core escolhe o tipo de dados do SQL Server apropriado com base no tipo CLR da propriedade.</span><span class="sxs-lookup"><span data-stu-id="be374-648">EF Core generally chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="be374-649">O tipo `decimal` CLR é mapeado para um tipo `decimal` SQL Server.</span><span class="sxs-lookup"><span data-stu-id="be374-649">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="be374-650">`Budget` refere-se à moeda e o tipo de dados de dinheiro é mais apropriado para moeda.</span><span class="sxs-lookup"><span data-stu-id="be374-650">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="be374-651">Propriedades de navegação e de chave estrangeira</span><span class="sxs-lookup"><span data-stu-id="be374-651">Foreign key and navigation properties</span></span>

<span data-ttu-id="be374-652">As propriedades de navegação e de FK refletem as seguintes relações:</span><span class="sxs-lookup"><span data-stu-id="be374-652">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="be374-653">Um departamento pode ou não ter um administrador.</span><span class="sxs-lookup"><span data-stu-id="be374-653">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="be374-654">Um administrador é sempre um instrutor.</span><span class="sxs-lookup"><span data-stu-id="be374-654">An administrator is always an instructor.</span></span> <span data-ttu-id="be374-655">Portanto, a propriedade `InstructorID` está incluída como a FK da entidade `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="be374-655">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="be374-656">A propriedade de navegação é chamada `Administrator`, mas contém uma entidade `Instructor`:</span><span class="sxs-lookup"><span data-stu-id="be374-656">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="be374-657">O ponto de interrogação (?) no código anterior especifica que a propriedade permite valor nulo.</span><span class="sxs-lookup"><span data-stu-id="be374-657">The question mark (?) in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="be374-658">Um departamento pode ter vários cursos e, portanto, há uma propriedade de navegação Courses:</span><span class="sxs-lookup"><span data-stu-id="be374-658">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="be374-659">Observação: por convenção, o EF Core habilita a exclusão em cascata em FKs que não permitem valor nulo e em relações muitos para muitos.</span><span class="sxs-lookup"><span data-stu-id="be374-659">Note: By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="be374-660">A exclusão em cascata pode resultar em regras de exclusão em cascata circular.</span><span class="sxs-lookup"><span data-stu-id="be374-660">Cascading delete can result in circular cascade delete rules.</span></span> <span data-ttu-id="be374-661">As regras de exclusão em cascata circular causam uma exceção quando uma migração é adicionada.</span><span class="sxs-lookup"><span data-stu-id="be374-661">Circular cascade delete rules causes an exception when a migration is added.</span></span>

<span data-ttu-id="be374-662">Por exemplo, se a propriedade `Department.InstructorID` tiver sido definida como não anulável:</span><span class="sxs-lookup"><span data-stu-id="be374-662">For example, if the `Department.InstructorID` property was defined as non-nullable:</span></span>

* <span data-ttu-id="be374-663">O EF Core configura uma regra de exclusão em cascata para excluir o departamento quando o instrutor é excluído.</span><span class="sxs-lookup"><span data-stu-id="be374-663">EF Core configures a cascade delete rule to delete the department when the instructor is deleted.</span></span>
* <span data-ttu-id="be374-664">Excluir o departamento quando o instrutor é excluído não é o comportamento desejado.</span><span class="sxs-lookup"><span data-stu-id="be374-664">Deleting the department when the instructor is deleted isn't the intended behavior.</span></span>
* <span data-ttu-id="be374-665">A seguinte API fluente definiria uma regra de restrição em vez de em cascata.</span><span class="sxs-lookup"><span data-stu-id="be374-665">The following fluent API would set a restrict rule instead of cascade.</span></span>

   ```csharp
   modelBuilder.Entity<Department>()
      .HasOne(d => d.Administrator)
      .WithMany()
      .OnDelete(DeleteBehavior.Restrict)
  ```

<span data-ttu-id="be374-666">O código anterior desabilita a exclusão em cascata na relação departamento-instrutor.</span><span class="sxs-lookup"><span data-stu-id="be374-666">The preceding code disables cascade delete on the department-instructor relationship.</span></span>

## <a name="update-the-enrollment-entity"></a><span data-ttu-id="be374-667">Atualizar a entidade Enrollment</span><span class="sxs-lookup"><span data-stu-id="be374-667">Update the Enrollment entity</span></span>

<span data-ttu-id="be374-668">Um registro se refere a um curso feito por um aluno.</span><span class="sxs-lookup"><span data-stu-id="be374-668">An enrollment record is for one course taken by one student.</span></span>

![Entidade Enrollment](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="be374-670">Atualize *Models/Enrollment.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="be374-670">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="be374-671">Propriedades de navegação e de chave estrangeira</span><span class="sxs-lookup"><span data-stu-id="be374-671">Foreign key and navigation properties</span></span>

<span data-ttu-id="be374-672">As propriedades de navegação e de FK refletem as seguintes relações:</span><span class="sxs-lookup"><span data-stu-id="be374-672">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="be374-673">Um registro destina-se a um curso e, portanto, há uma propriedade de FK `CourseID` e uma propriedade de navegação `Course`:</span><span class="sxs-lookup"><span data-stu-id="be374-673">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="be374-674">Um registro destina-se a um aluno e, portanto, há uma propriedade de FK `StudentID` e uma propriedade de navegação `Student`:</span><span class="sxs-lookup"><span data-stu-id="be374-674">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="be374-675">Relações muitos para muitos</span><span class="sxs-lookup"><span data-stu-id="be374-675">Many-to-Many Relationships</span></span>

<span data-ttu-id="be374-676">Há uma relação muitos para muitos entre as entidades `Student` e `Course`.</span><span class="sxs-lookup"><span data-stu-id="be374-676">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="be374-677">A entidade `Enrollment` funciona como uma tabela de junção muitos para muitos *com conteúdo* no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="be374-677">The `Enrollment` entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="be374-678">"Com conteúdo" significa que a tabela `Enrollment` contém dados adicionais além das FKs das tabelas unidas (nesse caso, a FK e `Grade`).</span><span class="sxs-lookup"><span data-stu-id="be374-678">"With payload" means that the `Enrollment` table contains additional data besides FKs for the joined tables (in this case, the PK and `Grade`).</span></span>

<span data-ttu-id="be374-679">A ilustração a seguir mostra a aparência dessas relações em um diagrama de entidades.</span><span class="sxs-lookup"><span data-stu-id="be374-679">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="be374-680">(Esse diagrama foi gerado com o [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) para EF 6.x.</span><span class="sxs-lookup"><span data-stu-id="be374-680">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="be374-681">A criação do diagrama não faz parte do tutorial.)</span><span class="sxs-lookup"><span data-stu-id="be374-681">Creating the diagram isn't part of the tutorial.)</span></span>

![Relação muitos para muitos de Student-Course](complex-data-model/_static/student-course.png)

<span data-ttu-id="be374-683">Cada linha de relação tem um 1 em uma extremidade e um asterisco (\*) na outra, indicando uma relação um para muitos.</span><span class="sxs-lookup"><span data-stu-id="be374-683">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="be374-684">Se a tabela `Enrollment` não incluir informações de nota, ela apenas precisará conter as duas FKs (`CourseID` e `StudentID`).</span><span class="sxs-lookup"><span data-stu-id="be374-684">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs (`CourseID` and `StudentID`).</span></span> <span data-ttu-id="be374-685">Uma tabela de junção muitos para muitos sem conteúdo é às vezes chamada de PJT (uma tabela de junção pura).</span><span class="sxs-lookup"><span data-stu-id="be374-685">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="be374-686">As entidades `Instructor` e `Course` têm uma relação muitos para muitos usando uma tabela de junção pura.</span><span class="sxs-lookup"><span data-stu-id="be374-686">The `Instructor` and `Course` entities have a many-to-many relationship using a pure join table.</span></span>

<span data-ttu-id="be374-687">Observação: O EF 6.x é compatível com tabelas de junção implícita para relações muitos para muitos, ao contrário do EF Core.</span><span class="sxs-lookup"><span data-stu-id="be374-687">Note: EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="be374-688">Para obter mais informações, consulte [Relações muitos para muitos no EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span><span class="sxs-lookup"><span data-stu-id="be374-688">For more information, see [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="be374-689">A entidade CourseAssignment</span><span class="sxs-lookup"><span data-stu-id="be374-689">The CourseAssignment entity</span></span>

![Entidade CourseAssignment](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="be374-691">Crie *Models/CourseAssignment.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="be374-691">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/CourseAssignment.cs)]

### <a name="instructor-to-courses"></a><span data-ttu-id="be374-692">Instrutor para Cursos</span><span class="sxs-lookup"><span data-stu-id="be374-692">Instructor-to-Courses</span></span>

![Instrutor para Cursos m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="be374-694">A relação muitos para muitos de Instrutor para Cursos:</span><span class="sxs-lookup"><span data-stu-id="be374-694">The Instructor-to-Courses many-to-many relationship:</span></span>

* <span data-ttu-id="be374-695">Exige que uma tabela de junção seja representada por um conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="be374-695">Requires a join table that must be represented by an entity set.</span></span>
* <span data-ttu-id="be374-696">É uma tabela de junção pura (tabela sem conteúdo).</span><span class="sxs-lookup"><span data-stu-id="be374-696">Is a pure join table (table without payload).</span></span>

<span data-ttu-id="be374-697">É comum nomear uma entidade de junção `EntityName1EntityName2`.</span><span class="sxs-lookup"><span data-stu-id="be374-697">It's common to name a join entity `EntityName1EntityName2`.</span></span> <span data-ttu-id="be374-698">Por exemplo, a tabela de junção Instrutor para Cursos com esse padrão é `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="be374-698">For example, the Instructor-to-Courses join table using this pattern is `CourseInstructor`.</span></span> <span data-ttu-id="be374-699">No entanto, recomendamos que você use um nome que descreve a relação.</span><span class="sxs-lookup"><span data-stu-id="be374-699">However, we recommend using a name that describes the relationship.</span></span>

<span data-ttu-id="be374-700">Modelos de dados começam simples e aumentam.</span><span class="sxs-lookup"><span data-stu-id="be374-700">Data models start out simple and grow.</span></span> <span data-ttu-id="be374-701">PJTs (junções sem conteúdo) evoluem com frequência para incluir o conteúdo.</span><span class="sxs-lookup"><span data-stu-id="be374-701">No-payload joins (PJTs) frequently evolve to include payload.</span></span> <span data-ttu-id="be374-702">Começando com um nome descritivo de entidade, o nome não precisa ser alterado quando a tabela de junção é alterada.</span><span class="sxs-lookup"><span data-stu-id="be374-702">By starting with a descriptive entity name, the name doesn't need to change when the join table changes.</span></span> <span data-ttu-id="be374-703">O ideal é que a entidade de junção tenha seu próprio nome natural (possivelmente, uma única palavra) no domínio de negócios.</span><span class="sxs-lookup"><span data-stu-id="be374-703">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="be374-704">Por exemplo, Manuais e Clientes podem ser vinculados com uma entidade de junção chamada Ratings.</span><span class="sxs-lookup"><span data-stu-id="be374-704">For example, Books and Customers could be linked with a join entity called Ratings.</span></span> <span data-ttu-id="be374-705">Para a relação muitos para muitos de Instrutor para Cursos, `CourseAssignment` é preferível a `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="be374-705">For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="be374-706">Chave composta</span><span class="sxs-lookup"><span data-stu-id="be374-706">Composite key</span></span>

<span data-ttu-id="be374-707">As FKs não permitem valor nulo.</span><span class="sxs-lookup"><span data-stu-id="be374-707">FKs are not nullable.</span></span> <span data-ttu-id="be374-708">As duas FKs em `CourseAssignment` (`InstructorID` e `CourseID`) juntas identificam exclusivamente cada linha da tabela `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="be374-708">The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table.</span></span> <span data-ttu-id="be374-709">`CourseAssignment` não exige um PK dedicado.</span><span class="sxs-lookup"><span data-stu-id="be374-709">`CourseAssignment` doesn't require a dedicated PK.</span></span> <span data-ttu-id="be374-710">As propriedades `InstructorID` e `CourseID` funcionam como uma PK composta.</span><span class="sxs-lookup"><span data-stu-id="be374-710">The `InstructorID` and `CourseID` properties function as a composite PK.</span></span> <span data-ttu-id="be374-711">A única maneira de especificar PKs compostas no EF Core é com a *API fluente*.</span><span class="sxs-lookup"><span data-stu-id="be374-711">The only way to specify composite PKs to EF Core is with the *fluent API*.</span></span> <span data-ttu-id="be374-712">A próxima seção mostra como configurar a PK composta.</span><span class="sxs-lookup"><span data-stu-id="be374-712">The next section shows how to configure the composite PK.</span></span>

<span data-ttu-id="be374-713">A chave composta garante:</span><span class="sxs-lookup"><span data-stu-id="be374-713">The composite key ensures:</span></span>

* <span data-ttu-id="be374-714">Várias linhas são permitidas para um curso.</span><span class="sxs-lookup"><span data-stu-id="be374-714">Multiple rows are allowed for one course.</span></span>
* <span data-ttu-id="be374-715">Várias linhas são permitidas para um instrutor.</span><span class="sxs-lookup"><span data-stu-id="be374-715">Multiple rows are allowed for one instructor.</span></span>
* <span data-ttu-id="be374-716">Não é permitido ter várias linhas para o mesmo instrutor e curso.</span><span class="sxs-lookup"><span data-stu-id="be374-716">Multiple rows for the same instructor and course isn't allowed.</span></span>

<span data-ttu-id="be374-717">A entidade de junção `Enrollment` define sua própria PK e, portanto, duplicatas desse tipo são possíveis.</span><span class="sxs-lookup"><span data-stu-id="be374-717">The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible.</span></span> <span data-ttu-id="be374-718">Para impedir duplicatas como essas:</span><span class="sxs-lookup"><span data-stu-id="be374-718">To prevent such duplicates:</span></span>

* <span data-ttu-id="be374-719">Adicione um índice exclusivo nos campos de FK ou</span><span class="sxs-lookup"><span data-stu-id="be374-719">Add a unique index on the FK fields, or</span></span>
* <span data-ttu-id="be374-720">Configure `Enrollment` com uma chave primária composta semelhante a `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="be374-720">Configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="be374-721">Para obter mais informações, consulte [Índices](/ef/core/modeling/indexes).</span><span class="sxs-lookup"><span data-stu-id="be374-721">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-db-context"></a><span data-ttu-id="be374-722">Atualizar o contexto de BD</span><span class="sxs-lookup"><span data-stu-id="be374-722">Update the DB context</span></span>

<span data-ttu-id="be374-723">Adicione o seguinte código realçado a *Data/SchoolContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="be374-723">Add the following highlighted code to *Data/SchoolContext.cs*:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

<span data-ttu-id="be374-724">O código anterior adiciona novas entidades e configura a PK composta da entidade `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="be374-724">The preceding code adds the new entities and configures the `CourseAssignment` entity's composite PK.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="be374-725">Alternativa de API fluente para atributos</span><span class="sxs-lookup"><span data-stu-id="be374-725">Fluent API alternative to attributes</span></span>

<span data-ttu-id="be374-726">O método `OnModelCreating` no código anterior usa a *API fluente* para configurar o comportamento do EF Core.</span><span class="sxs-lookup"><span data-stu-id="be374-726">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="be374-727">A API é chamada "fluente" porque geralmente é usada pelo encadeamento de uma série de chamadas de método em uma única instrução.</span><span class="sxs-lookup"><span data-stu-id="be374-727">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="be374-728">O [seguinte código](/ef/core/modeling/#use-fluent-api-to-configure-a-model) é um exemplo da API fluente:</span><span class="sxs-lookup"><span data-stu-id="be374-728">The [following code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="be374-729">Neste tutorial, a API fluente é usada apenas para o mapeamento do BD que não pode ser feito com atributos.</span><span class="sxs-lookup"><span data-stu-id="be374-729">In this tutorial, the fluent API is used only for DB mapping that can't be done with attributes.</span></span> <span data-ttu-id="be374-730">No entanto, a API fluente pode especificar a maioria das regras de formatação, validação e mapeamento que pode ser feita com atributos.</span><span class="sxs-lookup"><span data-stu-id="be374-730">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="be374-731">Alguns atributos como `MinimumLength` não podem ser aplicados com a API fluente.</span><span class="sxs-lookup"><span data-stu-id="be374-731">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="be374-732">`MinimumLength` não altera o esquema; apenas aplica uma regra de validação de tamanho mínimo.</span><span class="sxs-lookup"><span data-stu-id="be374-732">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="be374-733">Alguns desenvolvedores preferem usar a API fluente exclusivamente para que possam manter suas classes de entidade "limpas".</span><span class="sxs-lookup"><span data-stu-id="be374-733">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="be374-734">Atributos e a API fluente podem ser combinados.</span><span class="sxs-lookup"><span data-stu-id="be374-734">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="be374-735">Há algumas configurações que apenas podem ser feitas com a API fluente (especificando uma PK composta).</span><span class="sxs-lookup"><span data-stu-id="be374-735">There are some configurations that can only be done with the fluent API (specifying a composite PK).</span></span> <span data-ttu-id="be374-736">Há algumas configurações que apenas podem ser feitas com atributos (`MinimumLength`).</span><span class="sxs-lookup"><span data-stu-id="be374-736">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="be374-737">A prática recomendada para uso de atributos ou da API fluente:</span><span class="sxs-lookup"><span data-stu-id="be374-737">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="be374-738">Escolha uma dessas duas abordagens.</span><span class="sxs-lookup"><span data-stu-id="be374-738">Choose one of these two approaches.</span></span>
* <span data-ttu-id="be374-739">Use a abordagem escolhida da forma mais consistente possível.</span><span class="sxs-lookup"><span data-stu-id="be374-739">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="be374-740">Alguns dos atributos usados neste tutorial são usados para:</span><span class="sxs-lookup"><span data-stu-id="be374-740">Some of the attributes used in the this tutorial are used for:</span></span>

* <span data-ttu-id="be374-741">Somente validação (por exemplo, `MinimumLength`).</span><span class="sxs-lookup"><span data-stu-id="be374-741">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="be374-742">Apenas configuração do EF Core (por exemplo, `HasKey`).</span><span class="sxs-lookup"><span data-stu-id="be374-742">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="be374-743">Validação e configuração do EF Core (por exemplo, `[StringLength(50)]`).</span><span class="sxs-lookup"><span data-stu-id="be374-743">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="be374-744">Para obter mais informações sobre atributos vs. API fluente, consulte [Métodos de configuração](/ef/core/modeling/).</span><span class="sxs-lookup"><span data-stu-id="be374-744">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram-showing-relationships"></a><span data-ttu-id="be374-745">Diagrama de entidade mostrando relações</span><span class="sxs-lookup"><span data-stu-id="be374-745">Entity Diagram Showing Relationships</span></span>

<span data-ttu-id="be374-746">A ilustração a seguir mostra o diagrama criado pelo EF Power Tools para o modelo Escola concluído.</span><span class="sxs-lookup"><span data-stu-id="be374-746">The following illustration shows the diagram that EF Power Tools create for the completed School model.</span></span>

![Diagrama de entidade](complex-data-model/_static/diagram.png)

<span data-ttu-id="be374-748">O diagrama anterior mostra:</span><span class="sxs-lookup"><span data-stu-id="be374-748">The preceding diagram shows:</span></span>

* <span data-ttu-id="be374-749">Várias linhas de relação um-para-muitos (1 para \*).</span><span class="sxs-lookup"><span data-stu-id="be374-749">Several one-to-many relationship lines (1 to \*).</span></span>
* <span data-ttu-id="be374-750">A linha de relação um para zero ou um (1 para 0..1) entre as entidades `Instructor` e `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="be374-750">The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.</span></span>
* <span data-ttu-id="be374-751">A linha de relação zero-ou-um-para-muitos (0..1 para \*) entre as entidades `Instructor` e `Department`.</span><span class="sxs-lookup"><span data-stu-id="be374-751">The zero-or-one-to-many relationship line (0..1 to \*) between the `Instructor` and `Department` entities.</span></span>

## <a name="seed-the-db-with-test-data"></a><span data-ttu-id="be374-752">Propagar o BD com os dados de teste</span><span class="sxs-lookup"><span data-stu-id="be374-752">Seed the DB with Test Data</span></span>

<span data-ttu-id="be374-753">Atualize o código em *Data/DbInitializer.cs*:</span><span class="sxs-lookup"><span data-stu-id="be374-753">Update the code in *Data/DbInitializer.cs*:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Final)]

<span data-ttu-id="be374-754">O código anterior fornece dados de semente para as novas entidades.</span><span class="sxs-lookup"><span data-stu-id="be374-754">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="be374-755">A maioria desse código cria novos objetos de entidade e carrega dados de exemplo.</span><span class="sxs-lookup"><span data-stu-id="be374-755">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="be374-756">Os dados de exemplo são usados para teste.</span><span class="sxs-lookup"><span data-stu-id="be374-756">The sample data is used for testing.</span></span> <span data-ttu-id="be374-757">Consulte `Enrollments` e `CourseAssignments` para obter exemplos de como tabelas de junção muitos para muitos podem ser propagadas.</span><span class="sxs-lookup"><span data-stu-id="be374-757">See `Enrollments` and `CourseAssignments` for examples of how many-to-many join tables can be seeded.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="be374-758">Adicionar uma migração</span><span class="sxs-lookup"><span data-stu-id="be374-758">Add a migration</span></span>

<span data-ttu-id="be374-759">Compile o projeto.</span><span class="sxs-lookup"><span data-stu-id="be374-759">Build the project.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="be374-760">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="be374-760">Visual Studio</span></span>](#tab/visual-studio)

```PMC
Add-Migration ComplexDataModel
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="be374-761">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="be374-761">Visual Studio Code</span></span>](#tab/visual-studio-code)

```console
dotnet ef migrations add ComplexDataModel
```

---

<span data-ttu-id="be374-762">O comando anterior exibe um aviso sobre a possível perda de dados.</span><span class="sxs-lookup"><span data-stu-id="be374-762">The preceding command displays a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="be374-763">Se o comando `database update` é executado, o seguinte erro é produzido:</span><span class="sxs-lookup"><span data-stu-id="be374-763">If the `database update` command is run, the following error is produced:</span></span>

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

## <a name="apply-the-migration"></a><span data-ttu-id="be374-764">Aplicar a migração</span><span class="sxs-lookup"><span data-stu-id="be374-764">Apply the migration</span></span>

<span data-ttu-id="be374-765">Agora que você tem um banco de dados existente, precisa pensar sobre como aplicar as alterações futuras a ele.</span><span class="sxs-lookup"><span data-stu-id="be374-765">Now that you have an existing database, you need to think about how to apply future changes to it.</span></span> <span data-ttu-id="be374-766">Este tutorial mostra duas abordagens:</span><span class="sxs-lookup"><span data-stu-id="be374-766">This tutorial shows two approaches:</span></span>

* [<span data-ttu-id="be374-767">Remover e recriar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="be374-767">Drop and re-create the database</span></span>](#drop)
* <span data-ttu-id="be374-768">[Aplicar a migração ao banco de dados existente](#applyexisting).</span><span class="sxs-lookup"><span data-stu-id="be374-768">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="be374-769">Embora esse método seja mais complexo e demorado, é a abordagem preferencial para ambientes de produção do mundo real.</span><span class="sxs-lookup"><span data-stu-id="be374-769">While this method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span> <span data-ttu-id="be374-770">**Observação**: essa é uma seção opcional do tutorial.</span><span class="sxs-lookup"><span data-stu-id="be374-770">**Note**: This is an optional section of the tutorial.</span></span> <span data-ttu-id="be374-771">Você pode remover e recriar etapas e ignorar esta seção.</span><span class="sxs-lookup"><span data-stu-id="be374-771">You can do the drop and re-create steps and skip this section.</span></span> <span data-ttu-id="be374-772">Se você quiser seguir as etapas nesta seção, não realize as etapas de remover e recriar.</span><span class="sxs-lookup"><span data-stu-id="be374-772">If you do want to follow the steps in this section, don't do the drop and re-create steps.</span></span> 

<a name="drop"></a>

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="be374-773">Remover e recriar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="be374-773">Drop and re-create the database</span></span>

<span data-ttu-id="be374-774">O código no `DbInitializer` atualizado adiciona dados de semente às novas entidades.</span><span class="sxs-lookup"><span data-stu-id="be374-774">The code in the updated `DbInitializer` adds seed data for the new entities.</span></span> <span data-ttu-id="be374-775">Para forçar o EF Core a criar um novo BD, remova e atualize o BD:</span><span class="sxs-lookup"><span data-stu-id="be374-775">To force EF Core to create a new  DB, drop and update the DB:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="be374-776">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="be374-776">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="be374-777">No **PMC** (Console do Gerenciador de Pacotes), execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="be374-777">In the **Package Manager Console** (PMC), run the following command:</span></span>

```PMC
Drop-Database
Update-Database
```

<span data-ttu-id="be374-778">Execute `Get-Help about_EntityFrameworkCore` no PMC para obter informações de ajuda.</span><span class="sxs-lookup"><span data-stu-id="be374-778">Run `Get-Help about_EntityFrameworkCore` from the PMC to get help information.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="be374-779">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="be374-779">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="be374-780">Abra uma janela Comando e navegue para a pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="be374-780">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="be374-781">A pasta do projeto contém o arquivo *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="be374-781">The project folder contains the *Startup.cs* file.</span></span>

<span data-ttu-id="be374-782">Insira o seguinte na janela Comando:</span><span class="sxs-lookup"><span data-stu-id="be374-782">Enter the following in the command window:</span></span>

 ```console
 dotnet ef database drop
dotnet ef database update
 ```

---

<span data-ttu-id="be374-783">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="be374-783">Run the app.</span></span> <span data-ttu-id="be374-784">A execução do aplicativo executa o método `DbInitializer.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="be374-784">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="be374-785">O `DbInitializer.Initialize` popula o novo BD.</span><span class="sxs-lookup"><span data-stu-id="be374-785">The `DbInitializer.Initialize` populates the new DB.</span></span>

<span data-ttu-id="be374-786">Abra o BD no SSOX:</span><span class="sxs-lookup"><span data-stu-id="be374-786">Open the DB in SSOX:</span></span>

* <span data-ttu-id="be374-787">Se o SSOX for aberto anteriormente, clique no botão **Atualizar**.</span><span class="sxs-lookup"><span data-stu-id="be374-787">If SSOX was opened previously, click the **Refresh** button.</span></span>
* <span data-ttu-id="be374-788">Expanda o nó **Tabelas**.</span><span class="sxs-lookup"><span data-stu-id="be374-788">Expand the **Tables** node.</span></span> <span data-ttu-id="be374-789">As tabelas criadas são exibidas.</span><span class="sxs-lookup"><span data-stu-id="be374-789">The created tables are displayed.</span></span>

![Tabelas no SSOX](complex-data-model/_static/ssox-tables.png)

<span data-ttu-id="be374-791">Examine a tabela **CourseAssignment**:</span><span class="sxs-lookup"><span data-stu-id="be374-791">Examine the **CourseAssignment** table:</span></span>

* <span data-ttu-id="be374-792">Clique com o botão direito do mouse na tabela **CourseAssignment** e selecione **Exibir Dados**.</span><span class="sxs-lookup"><span data-stu-id="be374-792">Right-click the **CourseAssignment** table and select **View Data**.</span></span>
* <span data-ttu-id="be374-793">Verifique se a tabela **CourseAssignment** contém dados.</span><span class="sxs-lookup"><span data-stu-id="be374-793">Verify the **CourseAssignment** table contains data.</span></span>

![Dados de CourseAssignment no SSOX](complex-data-model/_static/ssox-ci-data.png)

<a name="applyexisting"></a>

### <a name="apply-the-migration-to-the-existing-database"></a><span data-ttu-id="be374-795">Aplicar a migração ao banco de dados existente</span><span class="sxs-lookup"><span data-stu-id="be374-795">Apply the migration to the existing database</span></span>

<span data-ttu-id="be374-796">Esta seção é opcional.</span><span class="sxs-lookup"><span data-stu-id="be374-796">This section is optional.</span></span> <span data-ttu-id="be374-797">Estas etapas só funcionarão se você tiver ignorado a seção [Remover e recriar o banco de dados](#drop) anterior.</span><span class="sxs-lookup"><span data-stu-id="be374-797">These steps work only if you skipped the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="be374-798">Quando as migrações são executadas com os dados existentes, pode haver restrições de FK que não são atendidas com os dados existentes.</span><span class="sxs-lookup"><span data-stu-id="be374-798">When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data.</span></span> <span data-ttu-id="be374-799">Com os dados de produção, é necessário executar etapas para migrar os dados existentes.</span><span class="sxs-lookup"><span data-stu-id="be374-799">With production data, steps must be taken to migrate the existing data.</span></span> <span data-ttu-id="be374-800">Esta seção fornece um exemplo de correção de violações de restrição de FK.</span><span class="sxs-lookup"><span data-stu-id="be374-800">This section provides an example of fixing FK constraint violations.</span></span> <span data-ttu-id="be374-801">Não faça essas alterações de código sem um backup.</span><span class="sxs-lookup"><span data-stu-id="be374-801">Don't make these code changes without a backup.</span></span> <span data-ttu-id="be374-802">Não faça essas alterações de código se você concluir a seção anterior e atualizou o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="be374-802">Don't make these code changes if you completed the previous section and updated the database.</span></span>

<span data-ttu-id="be374-803">O arquivo *{timestamp}_ComplexDataModel.cs* contém o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="be374-803">The *{timestamp}_ComplexDataModel.cs* file contains the following code:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_DepartmentID)]

<span data-ttu-id="be374-804">O código anterior adiciona uma FK `DepartmentID` que não permite valor nulo à tabela `Course`.</span><span class="sxs-lookup"><span data-stu-id="be374-804">The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table.</span></span> <span data-ttu-id="be374-805">O BD do tutorial anterior contém linhas em `Course` e, portanto, essa tabela não pode ser atualizada por migrações.</span><span class="sxs-lookup"><span data-stu-id="be374-805">The DB from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.</span></span>

<span data-ttu-id="be374-806">Para fazer a migração `ComplexDataModel` funcionar com os dados existentes:</span><span class="sxs-lookup"><span data-stu-id="be374-806">To make the `ComplexDataModel` migration work with existing data:</span></span>

* <span data-ttu-id="be374-807">Altere o código para dar à nova coluna (`DepartmentID`) um valor padrão.</span><span class="sxs-lookup"><span data-stu-id="be374-807">Change the code to give the new column (`DepartmentID`) a default value.</span></span>
* <span data-ttu-id="be374-808">Crie um departamento fictício chamado "Temp" para atuar como o departamento padrão.</span><span class="sxs-lookup"><span data-stu-id="be374-808">Create a fake department named "Temp" to act as the default department.</span></span>

#### <a name="fix-the-foreign-key-constraints"></a><span data-ttu-id="be374-809">Corrigir as restrições de chave estrangeira</span><span class="sxs-lookup"><span data-stu-id="be374-809">Fix the foreign key constraints</span></span>

<span data-ttu-id="be374-810">Atualize o método `Up` das classes `ComplexDataModel`:</span><span class="sxs-lookup"><span data-stu-id="be374-810">Update the `ComplexDataModel` classes `Up` method:</span></span>

* <span data-ttu-id="be374-811">Abra o arquivo *{timestamp}_ComplexDataModel.cs*.</span><span class="sxs-lookup"><span data-stu-id="be374-811">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>
* <span data-ttu-id="be374-812">Comente a linha de código que adiciona a coluna `DepartmentID` à tabela `Course`.</span><span class="sxs-lookup"><span data-stu-id="be374-812">Comment out the line of code that adds the `DepartmentID` column to the `Course` table.</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

<span data-ttu-id="be374-813">Adicione o código realçado a seguir.</span><span class="sxs-lookup"><span data-stu-id="be374-813">Add the following highlighted code.</span></span> <span data-ttu-id="be374-814">O novo código é inserido após o bloco `.CreateTable( name: "Department"`:</span><span class="sxs-lookup"><span data-stu-id="be374-814">The new code goes after the `.CreateTable( name: "Department"` block:</span></span>

 [!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

<span data-ttu-id="be374-815">Com as alterações anteriores, as linhas `Course` existentes estarão relacionadas ao departamento "Temp" após a execução do método `ComplexDataModel` `Up`.</span><span class="sxs-lookup"><span data-stu-id="be374-815">With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel` `Up` method runs.</span></span>

<span data-ttu-id="be374-816">Um aplicativo de produção:</span><span class="sxs-lookup"><span data-stu-id="be374-816">A production app would:</span></span>

* <span data-ttu-id="be374-817">Inclui código ou scripts para adicionar linhas `Department` e linhas `Course` relacionadas às novas linhas `Department`.</span><span class="sxs-lookup"><span data-stu-id="be374-817">Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.</span></span>
* <span data-ttu-id="be374-818">Não usa o departamento "Temp" nem o valor padrão para `Course.DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="be374-818">Not use the "Temp" department or the default value for `Course.DepartmentID`.</span></span>

<span data-ttu-id="be374-819">O próximo tutorial abrange os dados relacionados.</span><span class="sxs-lookup"><span data-stu-id="be374-819">The next tutorial covers related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="be374-820">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="be374-820">Additional resources</span></span>

* [<span data-ttu-id="be374-821">Versão do YouTube deste tutorial (Parte 1)</span><span class="sxs-lookup"><span data-stu-id="be374-821">YouTube version of this tutorial(Part 1)</span></span>](https://www.youtube.com/watch?v=0n2f0ObgCoA)
* [<span data-ttu-id="be374-822">Versão do YouTube deste tutorial (Parte 2)</span><span class="sxs-lookup"><span data-stu-id="be374-822">YouTube version of this tutorial(Part 2)</span></span>](https://www.youtube.com/watch?v=Je0Z5K1TNmY)



> [!div class="step-by-step"]
> <span data-ttu-id="be374-823">[Anterior](xref:data/ef-rp/migrations)
> [Próximo](xref:data/ef-rp/read-related-data)</span><span class="sxs-lookup"><span data-stu-id="be374-823">[Previous](xref:data/ef-rp/migrations)
[Next](xref:data/ef-rp/read-related-data)</span></span>

::: moniker-end