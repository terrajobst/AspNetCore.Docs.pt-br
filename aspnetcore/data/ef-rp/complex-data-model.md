---
title: Páginas Razor com o EF Core no ASP.NET Core – Modelo de dados – 5 de 8
author: rick-anderson
description: Neste tutorial, você adiciona mais entidades e relações e personaliza o modelo de dados especificando formatação, validação e regras de mapeamento.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
uid: data/ef-rp/complex-data-model
ms.openlocfilehash: 411c0874d2b2c6ecadd1da9aff7a093f1e8e525a
ms.sourcegitcommit: d2ba66023884f0dca115ff010bd98d5ed6459283
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77213422"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---data-model---5-of-8"></a><span data-ttu-id="cdd0e-103">Páginas Razor com o EF Core no ASP.NET Core – Modelo de dados – 5 de 8</span><span class="sxs-lookup"><span data-stu-id="cdd0e-103">Razor Pages with EF Core in ASP.NET Core - Data Model - 5 of 8</span></span>

<span data-ttu-id="cdd0e-104">Por [Tom Dykstra](https://github.com/tdykstra) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="cdd0e-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cdd0e-105">Os tutoriais anteriores trabalharam com um modelo de dados básico composto por três entidades.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-105">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="cdd0e-106">Neste tutorial:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-106">In this tutorial:</span></span>

* <span data-ttu-id="cdd0e-107">Mais entidades e relações são adicionadas.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-107">More entities and relationships are added.</span></span>
* <span data-ttu-id="cdd0e-108">O modelo de dados é personalizado com a especificação das regras de formatação, validação e mapeamento de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-108">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="cdd0e-109">O modelo de dados concluído é mostrado na seguinte ilustração:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-109">The completed data model is shown in the following illustration:</span></span>

![Diagrama de entidade](complex-data-model/_static/diagram.png)

## <a name="the-student-entity"></a><span data-ttu-id="cdd0e-111">A entidade Student</span><span class="sxs-lookup"><span data-stu-id="cdd0e-111">The Student entity</span></span>

![Entidade Student](complex-data-model/_static/student-entity.png)

<span data-ttu-id="cdd0e-113">Substitua o código em *Models/Student.cs* pelo código a seguir:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-113">Replace the code in *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Student.cs)]

<span data-ttu-id="cdd0e-114">O código anterior adiciona uma propriedade `FullName` e adiciona os seguintes atributos às propriedades existentes:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-114">The preceding code adds a `FullName` property and adds the following attributes to existing properties:</span></span>

* `[DataType]`
* `[DisplayFormat]`
* `[StringLength]`
* `[Column]`
* `[Required]`
* `[Display]`

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="cdd0e-115">A propriedade calculada FullName</span><span class="sxs-lookup"><span data-stu-id="cdd0e-115">The FullName calculated property</span></span>

<span data-ttu-id="cdd0e-116">`FullName` é uma propriedade calculada que retorna um valor criado pela concatenação de duas outras propriedades.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-116">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="cdd0e-117">`FullName` não pode ser definido, assim, ele apenas tem um acessador get.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-117">`FullName` can't be set, so it has only a get accessor.</span></span> <span data-ttu-id="cdd0e-118">Nenhuma coluna `FullName` é criada no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-118">No `FullName` column is created in the database.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="cdd0e-119">O atributo DataType</span><span class="sxs-lookup"><span data-stu-id="cdd0e-119">The DataType attribute</span></span>

```csharp
[DataType(DataType.Date)]
```

<span data-ttu-id="cdd0e-120">Para as datas de registro do aluno, todas as páginas atualmente exibem a hora do dia junto com a data, embora apenas a data seja relevante.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-120">For student enrollment dates, all of the pages currently display the time of day along with the date, although only the date is relevant.</span></span> <span data-ttu-id="cdd0e-121">Usando atributos de anotação de dados, você pode fazer uma alteração de código que corrigirá o formato de exibição em cada página que mostra os dados.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-121">By using data annotation attributes, you can make one code change that will fix the display format in every page that shows the data.</span></span> 

<span data-ttu-id="cdd0e-122">O atributo [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) especifica um tipo de dados mais específico do que o tipo intrínseco de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-122">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="cdd0e-123">Neste caso, apenas a data deve ser exibida, não a data e a hora.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-123">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="cdd0e-124">A [Enumeração DataType](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) fornece vários tipos de dados, como data, hora, PhoneNumber, moeda, EmailAddress, etc. O atributo `DataType` também pode habilitar o aplicativo para fornecer automaticamente recursos específicos do tipo.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-124">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="cdd0e-125">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-125">For example:</span></span>

* <span data-ttu-id="cdd0e-126">O link `mailto:` é criado automaticamente para `DataType.EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-126">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="cdd0e-127">O seletor de data é fornecido para `DataType.Date` na maioria dos navegadores.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-127">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="cdd0e-128">O atributo `DataType` emite atributos HTML 5 `data-` (pronunciados como data dash).</span><span class="sxs-lookup"><span data-stu-id="cdd0e-128">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes.</span></span> <span data-ttu-id="cdd0e-129">Os atributos `DataType` não fornecem validação.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-129">The `DataType` attributes don't provide validation.</span></span>

### <a name="the-displayformat-attribute"></a><span data-ttu-id="cdd0e-130">O atributo DisplayFormat</span><span class="sxs-lookup"><span data-stu-id="cdd0e-130">The DisplayFormat attribute</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="cdd0e-131">`DataType.Date` não especifica o formato da data exibida.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-131">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="cdd0e-132">Por padrão, o campo de dados é exibido de acordo com os formatos padrão com base nas [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support) do servidor.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-132">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="cdd0e-133">O atributo `DisplayFormat` é usado para especificar explicitamente o formato de data.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-133">The `DisplayFormat` attribute is used to explicitly specify the date format.</span></span> <span data-ttu-id="cdd0e-134">A configuração `ApplyFormatInEditMode` especifica que a formatação também deve ser aplicada à interface do usuário de edição.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-134">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="cdd0e-135">Alguns campos não devem usar `ApplyFormatInEditMode`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-135">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="cdd0e-136">Por exemplo, o símbolo de moeda geralmente não deve ser exibido em uma caixa de texto de edição.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-136">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="cdd0e-137">O atributo `DisplayFormat` pode ser usado por si só.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-137">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="cdd0e-138">Geralmente, é uma boa ideia usar o atributo `DataType` com o atributo `DisplayFormat`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-138">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="cdd0e-139">O atributo `DataType` transmite a semântica dos dados em vez de como renderizá-los em uma tela.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-139">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="cdd0e-140">O atributo `DataType` oferece os seguintes benefícios que não estão disponíveis em `DisplayFormat`:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-140">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="cdd0e-141">O navegador pode habilitar recursos do HTML5.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-141">The browser can enable HTML5 features.</span></span> <span data-ttu-id="cdd0e-142">Por exemplo, mostra um controle de calendário, o símbolo de moeda apropriado à localidade, links de email e validação de entrada do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-142">For example, show a calendar control, the locale-appropriate currency symbol, email links, and client-side input validation.</span></span>
* <span data-ttu-id="cdd0e-143">Por padrão, o navegador renderiza os dados usando o formato correto de acordo com a localidade.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-143">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="cdd0e-144">Para obter mais informações, consulte a [documentação do Auxiliar de Marcação \<input>](xref:mvc/views/working-with-forms#the-input-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="cdd0e-144">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

### <a name="the-stringlength-attribute"></a><span data-ttu-id="cdd0e-145">O atributo StringLength</span><span class="sxs-lookup"><span data-stu-id="cdd0e-145">The StringLength attribute</span></span>

```csharp
[StringLength(50, ErrorMessage = "First name cannot be longer than 50 characters.")]
```

<span data-ttu-id="cdd0e-146">Regras de validação de dados e mensagens de erro de validação podem ser especificadas com atributos.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-146">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="cdd0e-147">O atributo [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) especifica o tamanho mínimo e máximo de caracteres permitidos em um campo de dados.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-147">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="cdd0e-148">O código mostrado limita os nomes a, no máximo, 50 caracteres.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-148">The code shown limits names to no more than 50 characters.</span></span> <span data-ttu-id="cdd0e-149">Um exemplo que define o comprimento mínimo da cadeia de caracteres é mostrado [posteriormente](#the-required-attribute).</span><span class="sxs-lookup"><span data-stu-id="cdd0e-149">An example that sets the minimum string length is shown [later](#the-required-attribute).</span></span>

<span data-ttu-id="cdd0e-150">O atributo `StringLength` também fornece a validação do lado do cliente e do servidor.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-150">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="cdd0e-151">O valor mínimo não tem impacto sobre o esquema de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-151">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="cdd0e-152">O atributo `StringLength` não impede que um usuário insira um espaço em branco em um nome.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-152">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="cdd0e-153">O atributo [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) pode ser usado para aplicar restrições à entrada.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-153">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) attribute can be used to apply restrictions to the input.</span></span> <span data-ttu-id="cdd0e-154">Por exemplo, o seguinte código exige que o primeiro caractere esteja em maiúscula e os caracteres restantes estejam em ordem alfabética:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-154">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="cdd0e-155">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cdd0e-155">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cdd0e-156">No **SSOX** (Pesquisador de Objetos do SQL Server), abra o designer de tabela Aluno clicando duas vezes na tabela **Aluno**.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-156">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![Tabela Alunos no SSOX antes das migrações](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="cdd0e-158">A imagem anterior mostra o esquema para a tabela `Student`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-158">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="cdd0e-159">Os campos de nome têm o tipo `nvarchar(MAX)`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-159">The name fields have type `nvarchar(MAX)`.</span></span> <span data-ttu-id="cdd0e-160">Quando uma migração é criada e aplicada posteriormente neste tutorial, os campos de nome se tornam `nvarchar(50)` como resultado dos atributos de comprimento da cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-160">When a migration is created and applied later in this tutorial, the name fields become `nvarchar(50)` as a result of the string length attributes.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="cdd0e-161">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cdd0e-161">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="cdd0e-162">Em sua ferramenta SQLite, examine as definições de coluna da tabela `Student`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-162">In your SQLite tool, examine the column definitions for the `Student` table.</span></span> <span data-ttu-id="cdd0e-163">Os campos de nome têm o tipo `Text`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-163">The name fields have type `Text`.</span></span> <span data-ttu-id="cdd0e-164">Observe que o campo nome é chamado `FirstMidName`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-164">Notice that the first name field is called `FirstMidName`.</span></span> <span data-ttu-id="cdd0e-165">Na próxima seção, altere o nome dessa coluna para `FirstName`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-165">In the next section, you change the name of that column to `FirstName`.</span></span>

---

### <a name="the-column-attribute"></a><span data-ttu-id="cdd0e-166">O atributo Column</span><span class="sxs-lookup"><span data-stu-id="cdd0e-166">The Column attribute</span></span>

```csharp
[Column("FirstName")]
public string FirstMidName { get; set; }
```

<span data-ttu-id="cdd0e-167">Os atributos podem controlar como as classes e propriedades são mapeadas para o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-167">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="cdd0e-168">No modelo `Student`, o atributo `Column` é usado para mapear o nome da propriedade `FirstMidName` para "FirstName" no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-168">In the `Student` model, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the database.</span></span>

<span data-ttu-id="cdd0e-169">Quando o banco de dados é criado, os nomes de propriedade no modelo são usados para nomes de coluna (exceto quando o atributo `Column` é usado).</span><span class="sxs-lookup"><span data-stu-id="cdd0e-169">When the database is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span> <span data-ttu-id="cdd0e-170">O modelo `Student` usa `FirstMidName` para o campo de nome porque o campo também pode conter um sobrenome.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-170">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="cdd0e-171">Com o atributo `[Column]`, `Student.FirstMidName` no modelo de dados é mapeado para a coluna `FirstName` da tabela `Student`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-171">With the `[Column]` attribute, `Student.FirstMidName` in the data model maps to the `FirstName` column of the `Student` table.</span></span> <span data-ttu-id="cdd0e-172">A adição do atributo `Column` altera o modelo que dá suporte ao `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-172">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="cdd0e-173">O modelo que dá suporte ao `SchoolContext` não corresponde mais ao banco de dados.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-173">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="cdd0e-174">Essa discrepância será resolvida adicionando uma migração posteriormente neste tutorial.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-174">That discrepancy will be resolved by adding a migration later in this tutorial.</span></span>

### <a name="the-required-attribute"></a><span data-ttu-id="cdd0e-175">O atributo Required</span><span class="sxs-lookup"><span data-stu-id="cdd0e-175">The Required attribute</span></span>

```csharp
[Required]
```

<span data-ttu-id="cdd0e-176">O atributo `Required` torna as propriedades de nome campos obrigatórios.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-176">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="cdd0e-177">O atributo `Required` não é necessário para tipos que não permitem valor nulo, como tipos de valor (por exemplo, `DateTime`, `int` e `double`).</span><span class="sxs-lookup"><span data-stu-id="cdd0e-177">The `Required` attribute isn't needed for non-nullable types such as value types (for example, `DateTime`, `int`, and `double`).</span></span> <span data-ttu-id="cdd0e-178">Tipos que não podem ser nulos são tratados automaticamente como campos obrigatórios.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-178">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="cdd0e-179">O atributo `Required` precisa ser usado com `MinimumLength` para que `MinimumLength` seja imposto.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-179">The `Required` attribute must be used with `MinimumLength` for the `MinimumLength` to be enforced.</span></span>

```csharp
[Display(Name = "Last Name")]
[Required]
[StringLength(50, MinimumLength=2)]
public string LastName { get; set; }
```

<span data-ttu-id="cdd0e-180">`MinimumLength` e `Required` permitem que o espaço em branco atenda à validação.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-180">`MinimumLength` and `Required` allow whitespace to satisfy the validation.</span></span> <span data-ttu-id="cdd0e-181">Use o atributo `RegularExpression` para obter controle total sobre a cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-181">Use the `RegularExpression` attribute for full control over the string.</span></span>

### <a name="the-display-attribute"></a><span data-ttu-id="cdd0e-182">O atributo Display</span><span class="sxs-lookup"><span data-stu-id="cdd0e-182">The Display attribute</span></span>

```csharp
[Display(Name = "Last Name")]
```

<span data-ttu-id="cdd0e-183">O atributo `Display` especifica que a legenda para as caixas de texto deve ser "Nome", "Sobrenome", "Nome Completo" e "Data de Registro".</span><span class="sxs-lookup"><span data-stu-id="cdd0e-183">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="cdd0e-184">As legendas padrão não tinham nenhum espaço entre as palavras, por exemplo, "Lastname".</span><span class="sxs-lookup"><span data-stu-id="cdd0e-184">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="create-a-migration"></a><span data-ttu-id="cdd0e-185">Criar uma migração</span><span class="sxs-lookup"><span data-stu-id="cdd0e-185">Create a migration</span></span>

<span data-ttu-id="cdd0e-186">Execute o aplicativo e acesse a página Alunos.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-186">Run the app and go to the Students page.</span></span> <span data-ttu-id="cdd0e-187">Uma exceção é gerada.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-187">An exception is thrown.</span></span> <span data-ttu-id="cdd0e-188">O atributo `[Column]` faz com que o EF Espere encontrar uma coluna chamada `FirstName`, mas o nome da coluna no banco de dados ainda é `FirstMidName`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-188">The `[Column]` attribute causes EF to expect to find a column named `FirstName`, but the column name in the database is still `FirstMidName`.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="cdd0e-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cdd0e-189">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cdd0e-190">A mensagem de erro é semelhante ao exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-190">The error message is similar to the following example:</span></span>

```
SqlException: Invalid column name 'FirstName'.
```

* <span data-ttu-id="cdd0e-191">No PMC, insira os seguintes comandos para criar uma nova migração e atualizar o banco de dados:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-191">In the PMC, enter the following commands to create a new migration and update the database:</span></span>

  ```powershell
  Add-Migration ColumnFirstName
  Update-Database
  ```

  <span data-ttu-id="cdd0e-192">O primeiro desses comandos gera a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-192">The first of these commands generates the following warning message:</span></span>

  ```text
  An operation was scaffolded that may result in the loss of data.
  Please review the migration for accuracy.
  ```

  <span data-ttu-id="cdd0e-193">O aviso é gerado porque os campos de nome agora estão limitados a 50 caracteres.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-193">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="cdd0e-194">Se um nome no banco de dados tiver mais de 50 caracteres, o 51º caractere até o último caractere serão perdidos.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-194">If a name in the database had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="cdd0e-195">Abra a tabela Alunos no SSOX:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-195">Open the Student table in SSOX:</span></span>

  ![Tabela Alunos no SSOX após as migrações](complex-data-model/_static/ssox-after-migration.png)

  <span data-ttu-id="cdd0e-197">Antes de a migração ser aplicada, as colunas de nome eram do tipo [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span><span class="sxs-lookup"><span data-stu-id="cdd0e-197">Before the migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="cdd0e-198">As colunas de nome agora são `nvarchar(50)`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-198">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="cdd0e-199">O nome da coluna foi alterado de `FirstMidName` para `FirstName`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-199">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="cdd0e-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cdd0e-200">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="cdd0e-201">A mensagem de erro é semelhante ao exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-201">The error message is similar to the following example:</span></span>

```
SqliteException: SQLite Error 1: 'no such column: s.FirstName'.
```

* <span data-ttu-id="cdd0e-202">Abra uma janela Comando na pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-202">Open a command window in the project folder.</span></span> <span data-ttu-id="cdd0e-203">Insira os seguintes comandos para criar uma nova migração e atualizar o banco de dados:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-203">Enter the following commands to create a new migration and update the database:</span></span>

  ```dotnetcli
  dotnet ef migrations add ColumnFirstName
  dotnet ef database update
  ```

  <span data-ttu-id="cdd0e-204">O comando de atualização de banco de dados exibe um erro como o exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-204">The database update command displays an error like the following example:</span></span>

  ```text
  SQLite does not support this migration operation ('AlterColumnOperation'). For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
  ```

<span data-ttu-id="cdd0e-205">Para este tutorial, a maneira de passar esse erro é excluir e recriar a migração inicial.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-205">For this tutorial, the way to get past this error is to delete and re-create the initial migration.</span></span> <span data-ttu-id="cdd0e-206">Para obter mais informações, confira a nota de aviso do SQLite na parte superior do [tutorial de migrações](xref:data/ef-rp/migrations).</span><span class="sxs-lookup"><span data-stu-id="cdd0e-206">For more information, see the SQLite warning note at the top of the [migrations tutorial](xref:data/ef-rp/migrations).</span></span>

* <span data-ttu-id="cdd0e-207">Exclua a pasta *Migração*.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-207">Delete the *Migrations* folder.</span></span>
* <span data-ttu-id="cdd0e-208">Execute os seguintes comandos para remover o banco de dados, criar uma nova migração inicial e aplicar a migração:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-208">Run the following commands to drop the database, create a new initial migration, and apply the migration:</span></span>

  ```dotnetcli
  dotnet ef database drop --force
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

* <span data-ttu-id="cdd0e-209">Examine a tabela Student em sua ferramenta do SQLite.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-209">Examine the Student table in your SQLite tool.</span></span> <span data-ttu-id="cdd0e-210">A coluna que era FirstMidName agora é FirstName.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-210">The column that was FirstMidName is now FirstName.</span></span>

---

* <span data-ttu-id="cdd0e-211">Execute o aplicativo e acesse a página Alunos.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-211">Run the app and go to the Students page.</span></span>
* <span data-ttu-id="cdd0e-212">Observe que os horários não são inseridos nem exibidos juntamente com datas.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-212">Notice that times are not input or displayed along with dates.</span></span>
* <span data-ttu-id="cdd0e-213">Selecione **Criar Novo** e tente inserir um nome com mais de 50 caracteres.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-213">Select **Create New**, and try to enter a name longer than 50 characters.</span></span>

> [!Note]
> <span data-ttu-id="cdd0e-214">Nas seções a seguir, a criação do aplicativo em alguns estágios gera erros do compilador.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-214">In the following sections, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="cdd0e-215">As instruções especificam quando compilar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-215">The instructions specify when to build the app.</span></span>

## <a name="the-instructor-entity"></a><span data-ttu-id="cdd0e-216">A entidade Instructor</span><span class="sxs-lookup"><span data-stu-id="cdd0e-216">The Instructor Entity</span></span>

![Entidade Instructor](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="cdd0e-218">Crie *Models/Instructor.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-218">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Instructor.cs)]

<span data-ttu-id="cdd0e-219">Vários atributos podem estar em uma linha.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-219">Multiple attributes can be on one line.</span></span> <span data-ttu-id="cdd0e-220">Os atributos `HireDate` podem ser escritos da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-220">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="navigation-properties"></a><span data-ttu-id="cdd0e-221">Propriedades de navegação</span><span class="sxs-lookup"><span data-stu-id="cdd0e-221">Navigation properties</span></span>

<span data-ttu-id="cdd0e-222">As propriedades `CourseAssignments` e `OfficeAssignment` são propriedades de navegação.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-222">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="cdd0e-223">Um instrutor pode ministrar qualquer quantidade de cursos e, portanto, `CourseAssignments` é definido como uma coleção.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-223">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="cdd0e-224">Um instrutor pode ter no máximo um escritório, portanto, a propriedade `OfficeAssignment` mantém uma única entidade `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-224">An instructor can have at most one office, so the `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="cdd0e-225">`OfficeAssignment` será nulo se nenhum escritório for atribuído.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-225">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="the-officeassignment-entity"></a><span data-ttu-id="cdd0e-226">A entidade OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="cdd0e-226">The OfficeAssignment entity</span></span>

![Entidade OfficeAssignment](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="cdd0e-228">Crie *Models/OfficeAssignment.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-228">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="cdd0e-229">O atributo Key</span><span class="sxs-lookup"><span data-stu-id="cdd0e-229">The Key attribute</span></span>

<span data-ttu-id="cdd0e-230">O atributo `[Key]` é usado para identificar uma propriedade como a PK (chave primária) quando o nome da propriedade é algo diferente de classnameID ou ID.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-230">The `[Key]` attribute is used to identify a property as the primary key (PK) when the property name is something other than classnameID or ID.</span></span>

<span data-ttu-id="cdd0e-231">Há uma relação um para zero ou um entre as entidades `Instructor` e `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-231">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="cdd0e-232">Uma atribuição de escritório existe apenas em relação ao instrutor ao qual ela é atribuída.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-232">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="cdd0e-233">A PK `OfficeAssignment` também é a FK (chave estrangeira) da entidade `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-233">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span>

<span data-ttu-id="cdd0e-234">O EF Core não pode reconhecer `InstructorID` automaticamente como a CP de `OfficeAssignment` porque `InstructorID` não segue a convenção de nomenclatura de ID ou classnameID.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-234">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because `InstructorID` doesn't follow the ID or classnameID naming convention.</span></span> <span data-ttu-id="cdd0e-235">Portanto, o atributo `Key` é usado para identificar `InstructorID` como a PK:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-235">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="cdd0e-236">Por padrão, o EF Core trata a chave como não gerada pelo banco de dados porque a coluna destina-se a uma relação de identificação.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-236">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="cdd0e-237">A propriedade de navegação Instructor</span><span class="sxs-lookup"><span data-stu-id="cdd0e-237">The Instructor navigation property</span></span>

<span data-ttu-id="cdd0e-238">A propriedade de navegação `Instructor.OfficeAssignment` pode ser nula porque pode não haver uma linha `OfficeAssignment` para um determinado instrutor.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-238">The `Instructor.OfficeAssignment` navigation property can be null because there might not be an `OfficeAssignment` row for a given instructor.</span></span> <span data-ttu-id="cdd0e-239">Um instrutor pode não ter uma atribuição de escritório.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-239">An instructor might not have an office assignment.</span></span>

<span data-ttu-id="cdd0e-240">A propriedade de navegação `OfficeAssignment.Instructor` sempre terá uma entidade de instrutor porque o tipo `InstructorID` de chave estrangeira é `int`, um tipo de valor não anulável.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-240">The `OfficeAssignment.Instructor` navigation property will always have an instructor entity because the foreign key `InstructorID` type is `int`, a non-nullable value type.</span></span> <span data-ttu-id="cdd0e-241">Uma atribuição de escritório não pode existir sem um instrutor.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-241">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="cdd0e-242">Quando uma entidade `Instructor` tem uma entidade `OfficeAssignment` relacionada, cada entidade tem uma referência à outra em sua propriedade de navegação.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-242">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="cdd0e-243">A entidade Course</span><span class="sxs-lookup"><span data-stu-id="cdd0e-243">The Course Entity</span></span>

![Entidade Course](complex-data-model/_static/course-entity.png)

<span data-ttu-id="cdd0e-245">Atualize *Models/Course.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-245">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Course.cs?highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="cdd0e-246">A entidade `Course` tem uma propriedade de FK (chave estrangeira) `DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-246">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="cdd0e-247">`DepartmentID` aponta para a entidade `Department` relacionada.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-247">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="cdd0e-248">A entidade `Course` tem uma propriedade de navegação `Department`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-248">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="cdd0e-249">O EF Core não exige uma propriedade de chave estrangeira para um modelo de dados quando o modelo tem uma propriedade de navegação para uma entidade relacionada.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-249">EF Core doesn't require a foreign key property for a data model when the model has a navigation property for a related entity.</span></span> <span data-ttu-id="cdd0e-250">O EF Core cria automaticamente FKs no banco de dados sempre que forem necessárias.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-250">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="cdd0e-251">O EF Core cria [propriedades de sombra](/ef/core/modeling/shadow-properties) para FKs criadas automaticamente.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-251">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="cdd0e-252">Porém, incluir explicitamente a FK no modelo de dados pode tornar as atualizações mais simples e mais eficientes.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-252">However, explicitly including the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="cdd0e-253">Por exemplo, considere um modelo em que a propriedade de FK `DepartmentID`*não* é incluída.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-253">For example, consider a model where the FK property `DepartmentID` is *not* included.</span></span> <span data-ttu-id="cdd0e-254">Quando uma entidade de curso é buscada para editar:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-254">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="cdd0e-255">A propriedade `Department` será nula se não for carregada de forma explícita.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-255">The `Department` property is null if it's not explicitly loaded.</span></span>
* <span data-ttu-id="cdd0e-256">Para atualizar a entidade de curso, a entidade `Department` primeiro deve ser buscada.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-256">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="cdd0e-257">Quando a propriedade de FK `DepartmentID` está incluída no modelo de dados, não é necessário buscar a entidade `Department` antes de uma atualização.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-257">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="cdd0e-258">O atributo DatabaseGenerated</span><span class="sxs-lookup"><span data-stu-id="cdd0e-258">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="cdd0e-259">O atributo `[DatabaseGenerated(DatabaseGeneratedOption.None)]` especifica que a PK é fornecida pelo aplicativo em vez de ser gerada pelo banco de dados.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-259">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="cdd0e-260">Por padrão, o EF Core supõe que os valores de PK sejam gerados pelo banco de dados.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-260">By default, EF Core assumes that PK values are generated by the database.</span></span> <span data-ttu-id="cdd0e-261">O banco de dados gerado costuma ser a melhor abordagem.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-261">Database-generated is generally the best approach.</span></span> <span data-ttu-id="cdd0e-262">Para entidades `Course`, o usuário especifica o PK.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-262">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="cdd0e-263">Por exemplo, um número de curso, como uma série 1000 para o departamento de matemática e uma série 2000 para o departamento em inglês.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-263">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="cdd0e-264">O atributo `DatabaseGenerated` também pode ser usado para gerar valores padrão.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-264">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="cdd0e-265">Por exemplo, o banco de dados pode gerar automaticamente um campo de data para registrar a data em que uma linha foi criada ou atualizada.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-265">For example, the database can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="cdd0e-266">Para obter mais informações, consulte [Propriedades geradas](/ef/core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="cdd0e-266">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="cdd0e-267">Propriedades de navegação e de chave estrangeira</span><span class="sxs-lookup"><span data-stu-id="cdd0e-267">Foreign key and navigation properties</span></span>

<span data-ttu-id="cdd0e-268">As propriedades de navegação e de FK (chave estrangeira) na entidade `Course` refletem as seguintes relações:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-268">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="cdd0e-269">Um curso é atribuído a um departamento; portanto, há uma FK `DepartmentID` e uma propriedade de navegação `Department`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-269">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="cdd0e-270">Um curso pode ter qualquer quantidade de estudantes inscritos; portanto, a propriedade de navegação `Enrollments` é uma coleção:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-270">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="cdd0e-271">Um curso pode ser ministrado por vários instrutores; portanto, a propriedade de navegação `CourseAssignments` é uma coleção:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-271">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection:</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="cdd0e-272">`CourseAssignment` é explicado [posteriormente](#many-to-many-relationships).</span><span class="sxs-lookup"><span data-stu-id="cdd0e-272">`CourseAssignment` is explained [later](#many-to-many-relationships).</span></span>

## <a name="the-department-entity"></a><span data-ttu-id="cdd0e-273">A entidade Department</span><span class="sxs-lookup"><span data-stu-id="cdd0e-273">The Department entity</span></span>

![Entidade Department](complex-data-model/_static/department-entity.png)

<span data-ttu-id="cdd0e-275">Crie *Models/Department.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-275">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Models/Department1.cs)]

### <a name="the-column-attribute"></a><span data-ttu-id="cdd0e-276">O atributo Column</span><span class="sxs-lookup"><span data-stu-id="cdd0e-276">The Column attribute</span></span>

<span data-ttu-id="cdd0e-277">Anteriormente, o atributo `Column` foi usado para alterar o mapeamento de nome de coluna.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-277">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="cdd0e-278">No código da entidade `Department`, o atributo `Column` é usado para alterar o mapeamento de tipo de dados SQL.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-278">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="cdd0e-279">A coluna `Budget` é definida usando o tipo de dinheiro do SQL Server no banco de dados:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-279">The `Budget` column is defined using the SQL Server money type in the database:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="cdd0e-280">Em geral, o mapeamento de coluna não é necessário.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-280">Column mapping is generally not required.</span></span> <span data-ttu-id="cdd0e-281">O EF Core escolhe o tipo de dados do SQL Server apropriado com base no tipo CLR da propriedade.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-281">EF Core chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="cdd0e-282">O tipo `decimal` CLR é mapeado para um tipo `decimal` SQL Server.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-282">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="cdd0e-283">`Budget` refere-se à moeda e o tipo de dados de dinheiro é mais apropriado para moeda.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-283">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="cdd0e-284">Propriedades de navegação e de chave estrangeira</span><span class="sxs-lookup"><span data-stu-id="cdd0e-284">Foreign key and navigation properties</span></span>

<span data-ttu-id="cdd0e-285">As propriedades de navegação e de FK refletem as seguintes relações:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-285">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="cdd0e-286">Um departamento pode ou não ter um administrador.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-286">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="cdd0e-287">Um administrador é sempre um instrutor.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-287">An administrator is always an instructor.</span></span> <span data-ttu-id="cdd0e-288">Portanto, a propriedade `InstructorID` está incluída como a FK da entidade `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-288">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="cdd0e-289">A propriedade de navegação é chamada `Administrator`, mas contém uma entidade `Instructor`:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-289">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="cdd0e-290">O ponto de interrogação (?) no código anterior especifica que a propriedade permite valor nulo.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-290">The question mark (?) in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="cdd0e-291">Um departamento pode ter vários cursos e, portanto, há uma propriedade de navegação Courses:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-291">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="cdd0e-292">por convenção, o EF Core habilita a exclusão em cascata em FKs que não permitem valor nulo e em relações muitos para muitos.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-292">By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="cdd0e-293">Esse comportamento padrão pode resultar em regras circulares de exclusão em cascata.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-293">This default behavior can result in circular cascade delete rules.</span></span> <span data-ttu-id="cdd0e-294">As regras de exclusão em cascata circular causam uma exceção quando uma migração é adicionada.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-294">Circular cascade delete rules cause an exception when a migration is added.</span></span>

<span data-ttu-id="cdd0e-295">Por exemplo, se a propriedade `Department.InstructorID` tiver sido definida como não anulável, o EF Core configurará uma regra de exclusão em cascata.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-295">For example, if the `Department.InstructorID` property was defined as non-nullable, EF Core would configure a cascade delete rule.</span></span> <span data-ttu-id="cdd0e-296">Nesse caso, o departamento seria excluído quando o instrutor atribuído como seu administrador fosse excluído.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-296">In that case, the department would be deleted when the instructor assigned as its administrator is deleted.</span></span> <span data-ttu-id="cdd0e-297">Nesse cenário, uma regra restrita fará mais sentido.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-297">In this scenario, a restrict rule would make more sense.</span></span> <span data-ttu-id="cdd0e-298">A [API fluente](#fluent-api-alternative-to-attributes) a seguir definiria uma regra restrita e desabilitará a exclusão em cascata.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-298">The following [fluent API](#fluent-api-alternative-to-attributes) would set a restrict rule and disable cascade delete.</span></span>

  ```csharp
  modelBuilder.Entity<Department>()
     .HasOne(d => d.Administrator)
     .WithMany()
     .OnDelete(DeleteBehavior.Restrict)
  ```

## <a name="the-enrollment-entity"></a><span data-ttu-id="cdd0e-299">A entidade Enrollment</span><span class="sxs-lookup"><span data-stu-id="cdd0e-299">The Enrollment entity</span></span>

<span data-ttu-id="cdd0e-300">Um registro se refere a um curso feito por um aluno.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-300">An enrollment record is for one course taken by one student.</span></span>

![Entidade Enrollment](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="cdd0e-302">Atualize *Models/Enrollment.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-302">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Enrollment.cs?highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="cdd0e-303">Propriedades de navegação e de chave estrangeira</span><span class="sxs-lookup"><span data-stu-id="cdd0e-303">Foreign key and navigation properties</span></span>

<span data-ttu-id="cdd0e-304">As propriedades de navegação e de FK refletem as seguintes relações:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-304">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="cdd0e-305">Um registro destina-se a um curso e, portanto, há uma propriedade de FK `CourseID` e uma propriedade de navegação `Course`:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-305">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="cdd0e-306">Um registro destina-se a um aluno e, portanto, há uma propriedade de FK `StudentID` e uma propriedade de navegação `Student`:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-306">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="cdd0e-307">Relações muitos para muitos</span><span class="sxs-lookup"><span data-stu-id="cdd0e-307">Many-to-Many Relationships</span></span>

<span data-ttu-id="cdd0e-308">Há uma relação muitos para muitos entre as entidades `Student` e `Course`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-308">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="cdd0e-309">A entidade `Enrollment` funciona como uma tabela de junção muitos para muitos *com conteúdo* no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-309">The `Enrollment` entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="cdd0e-310">"Com conteúdo" significa que a tabela `Enrollment` contém dados adicionais além das FKs das tabelas unidas (nesse caso, a FK e `Grade`).</span><span class="sxs-lookup"><span data-stu-id="cdd0e-310">"With payload" means that the `Enrollment` table contains additional data besides FKs for the joined tables (in this case, the PK and `Grade`).</span></span>

<span data-ttu-id="cdd0e-311">A ilustração a seguir mostra a aparência dessas relações em um diagrama de entidades.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-311">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="cdd0e-312">(Esse diagrama foi gerado com o [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) para EF 6.x.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-312">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="cdd0e-313">A criação do diagrama não faz parte do tutorial.)</span><span class="sxs-lookup"><span data-stu-id="cdd0e-313">Creating the diagram isn't part of the tutorial.)</span></span>

![Relação muitos para muitos de Student-Course](complex-data-model/_static/student-course.png)

<span data-ttu-id="cdd0e-315">Cada linha de relação tem um 1 em uma extremidade e um asterisco (\*) na outra, indicando uma relação um para muitos.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-315">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="cdd0e-316">Se a tabela `Enrollment` não incluir informações de nota, ela apenas precisará conter as duas FKs (`CourseID` e `StudentID`).</span><span class="sxs-lookup"><span data-stu-id="cdd0e-316">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs (`CourseID` and `StudentID`).</span></span> <span data-ttu-id="cdd0e-317">Uma tabela de junção muitos para muitos sem conteúdo é às vezes chamada de PJT (uma tabela de junção pura).</span><span class="sxs-lookup"><span data-stu-id="cdd0e-317">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="cdd0e-318">As entidades `Instructor` e `Course` têm uma relação muitos para muitos usando uma tabela de junção pura.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-318">The `Instructor` and `Course` entities have a many-to-many relationship using a pure join table.</span></span>

<span data-ttu-id="cdd0e-319">Observação: o EF 6.x é compatível com tabelas de junção implícita para relações muitos para muitos, ao contrário do EF Core.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-319">Note: EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="cdd0e-320">Para obter mais informações, consulte [Relações muitos para muitos no EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span><span class="sxs-lookup"><span data-stu-id="cdd0e-320">For more information, see [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="cdd0e-321">A entidade CourseAssignment</span><span class="sxs-lookup"><span data-stu-id="cdd0e-321">The CourseAssignment entity</span></span>

![Entidade CourseAssignment](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="cdd0e-323">Crie *Models/CourseAssignment.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-323">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/CourseAssignment.cs)]

<span data-ttu-id="cdd0e-324">O relacionamento de muitos para muitos do instrutor para cursos requer uma tabela de junção e a entidade para essa tabela de junção é CourseAssignment.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-324">The Instructor-to-Courses many-to-many relationship requires a join table, and the entity for that join table is CourseAssignment.</span></span>

![Instrutor para Cursos m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="cdd0e-326">É comum nomear uma entidade de junção `EntityName1EntityName2`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-326">It's common to name a join entity `EntityName1EntityName2`.</span></span> <span data-ttu-id="cdd0e-327">Por exemplo, a tabela de junção Instrutor para Cursos com esse padrão seria `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-327">For example, the Instructor-to-Courses join table using this pattern would be `CourseInstructor`.</span></span> <span data-ttu-id="cdd0e-328">No entanto, recomendamos que você use um nome que descreve a relação.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-328">However, we recommend using a name that describes the relationship.</span></span>

<span data-ttu-id="cdd0e-329">Modelos de dados começam simples e aumentam.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-329">Data models start out simple and grow.</span></span> <span data-ttu-id="cdd0e-330">As PJTs (tabelas de junção sem payload) costumam evoluir para incluir a payload.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-330">Join tables without payload (PJTs) frequently evolve to include payload.</span></span> <span data-ttu-id="cdd0e-331">Começando com um nome descritivo de entidade, o nome não precisa ser alterado quando a tabela de junção é alterada.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-331">By starting with a descriptive entity name, the name doesn't need to change when the join table changes.</span></span> <span data-ttu-id="cdd0e-332">O ideal é que a entidade de junção tenha seu próprio nome natural (possivelmente, uma única palavra) no domínio de negócios.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-332">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="cdd0e-333">Por exemplo, Manuais e Clientes podem ser vinculados com uma entidade de junção chamada Ratings.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-333">For example, Books and Customers could be linked with a join entity called Ratings.</span></span> <span data-ttu-id="cdd0e-334">Para a relação muitos para muitos de Instrutor para Cursos, `CourseAssignment` é preferível a `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-334">For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="cdd0e-335">Chave composta</span><span class="sxs-lookup"><span data-stu-id="cdd0e-335">Composite key</span></span>

<span data-ttu-id="cdd0e-336">As duas FKs em `CourseAssignment` (`InstructorID` e `CourseID`) juntas identificam exclusivamente cada linha da tabela `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-336">The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table.</span></span> <span data-ttu-id="cdd0e-337">`CourseAssignment` não exige um PK dedicado.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-337">`CourseAssignment` doesn't require a dedicated PK.</span></span> <span data-ttu-id="cdd0e-338">As propriedades `InstructorID` e `CourseID` funcionam como uma PK composta.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-338">The `InstructorID` and `CourseID` properties function as a composite PK.</span></span> <span data-ttu-id="cdd0e-339">A única maneira de especificar PKs compostas no EF Core é com a *API fluente*.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-339">The only way to specify composite PKs to EF Core is with the *fluent API*.</span></span> <span data-ttu-id="cdd0e-340">A próxima seção mostra como configurar a PK composta.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-340">The next section shows how to configure the composite PK.</span></span>

<span data-ttu-id="cdd0e-341">A chave composta garante que:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-341">The composite key ensures that:</span></span>

* <span data-ttu-id="cdd0e-342">Várias linhas são permitidas para um curso.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-342">Multiple rows are allowed for one course.</span></span>
* <span data-ttu-id="cdd0e-343">Várias linhas são permitidas para um instrutor.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-343">Multiple rows are allowed for one instructor.</span></span>
* <span data-ttu-id="cdd0e-344">Não sejam permitidas várias linhas para o mesmo instrutor e curso.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-344">Multiple rows aren't allowed for the same instructor and course.</span></span>

<span data-ttu-id="cdd0e-345">A entidade de junção `Enrollment` define sua própria PK e, portanto, duplicatas desse tipo são possíveis.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-345">The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible.</span></span> <span data-ttu-id="cdd0e-346">Para impedir duplicatas como essas:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-346">To prevent such duplicates:</span></span>

* <span data-ttu-id="cdd0e-347">Adicione um índice exclusivo nos campos de FK ou</span><span class="sxs-lookup"><span data-stu-id="cdd0e-347">Add a unique index on the FK fields, or</span></span>
* <span data-ttu-id="cdd0e-348">Configure `Enrollment` com uma chave primária composta semelhante a `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-348">Configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="cdd0e-349">Para obter mais informações, consulte [Índices](/ef/core/modeling/indexes).</span><span class="sxs-lookup"><span data-stu-id="cdd0e-349">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-database-context"></a><span data-ttu-id="cdd0e-350">Atualizar o contexto de banco de dados</span><span class="sxs-lookup"><span data-stu-id="cdd0e-350">Update the database context</span></span>

<span data-ttu-id="cdd0e-351">Atualize *Data/SchoolContext.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-351">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Data/SchoolContext.cs?highlight=15-18,25-31)]

<span data-ttu-id="cdd0e-352">O código anterior adiciona novas entidades e configura a PK composta da entidade `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-352">The preceding code adds the new entities and configures the `CourseAssignment` entity's composite PK.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="cdd0e-353">Alternativa de API fluente para atributos</span><span class="sxs-lookup"><span data-stu-id="cdd0e-353">Fluent API alternative to attributes</span></span>

<span data-ttu-id="cdd0e-354">O método `OnModelCreating` no código anterior usa a *API fluente* para configurar o comportamento do EF Core.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-354">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="cdd0e-355">A API é chamada "fluente" porque geralmente é usada pelo encadeamento de uma série de chamadas de método em uma única instrução.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-355">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="cdd0e-356">O [seguinte código](/ef/core/modeling/#use-fluent-api-to-configure-a-model) é um exemplo da API fluente:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-356">The [following code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="cdd0e-357">Neste tutorial, a API fluente é usada apenas para o mapeamento do banco de dados que não pode ser feito com atributos.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-357">In this tutorial, the fluent API is used only for database mapping that can't be done with attributes.</span></span> <span data-ttu-id="cdd0e-358">No entanto, a API fluente pode especificar a maioria das regras de formatação, validação e mapeamento que pode ser feita com atributos.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-358">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="cdd0e-359">Alguns atributos como `MinimumLength` não podem ser aplicados com a API fluente.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-359">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="cdd0e-360">`MinimumLength` não altera o esquema; apenas aplica uma regra de validação de tamanho mínimo.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-360">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="cdd0e-361">Alguns desenvolvedores preferem usar a API fluente exclusivamente para que possam manter suas classes de entidade "limpas".</span><span class="sxs-lookup"><span data-stu-id="cdd0e-361">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="cdd0e-362">Atributos e a API fluente podem ser combinados.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-362">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="cdd0e-363">Há algumas configurações que apenas podem ser feitas com a API fluente (especificando uma PK composta).</span><span class="sxs-lookup"><span data-stu-id="cdd0e-363">There are some configurations that can only be done with the fluent API (specifying a composite PK).</span></span> <span data-ttu-id="cdd0e-364">Há algumas configurações que apenas podem ser feitas com atributos (`MinimumLength`).</span><span class="sxs-lookup"><span data-stu-id="cdd0e-364">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="cdd0e-365">A prática recomendada para uso de atributos ou da API fluente:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-365">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="cdd0e-366">Escolha uma dessas duas abordagens.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-366">Choose one of these two approaches.</span></span>
* <span data-ttu-id="cdd0e-367">Use a abordagem escolhida da forma mais consistente possível.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-367">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="cdd0e-368">Alguns dos atributos usados neste tutorial são usados para:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-368">Some of the attributes used in this tutorial are used for:</span></span>

* <span data-ttu-id="cdd0e-369">Somente validação (por exemplo, `MinimumLength`).</span><span class="sxs-lookup"><span data-stu-id="cdd0e-369">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="cdd0e-370">Apenas configuração do EF Core (por exemplo, `HasKey`).</span><span class="sxs-lookup"><span data-stu-id="cdd0e-370">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="cdd0e-371">Validação e configuração do EF Core (por exemplo, `[StringLength(50)]`).</span><span class="sxs-lookup"><span data-stu-id="cdd0e-371">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="cdd0e-372">Para obter mais informações sobre atributos vs. API fluente, consulte [Métodos de configuração](/ef/core/modeling/).</span><span class="sxs-lookup"><span data-stu-id="cdd0e-372">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram"></a><span data-ttu-id="cdd0e-373">Diagrama de entidade</span><span class="sxs-lookup"><span data-stu-id="cdd0e-373">Entity diagram</span></span>

<span data-ttu-id="cdd0e-374">A ilustração a seguir mostra o diagrama criado pelo EF Power Tools para o modelo Escola concluído.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-374">The following illustration shows the diagram that EF Power Tools create for the completed School model.</span></span>

![Diagrama de entidade](complex-data-model/_static/diagram.png)

<span data-ttu-id="cdd0e-376">O diagrama anterior mostra:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-376">The preceding diagram shows:</span></span>

* <span data-ttu-id="cdd0e-377">Várias linhas de relação um-para-muitos (1 para \*).</span><span class="sxs-lookup"><span data-stu-id="cdd0e-377">Several one-to-many relationship lines (1 to \*).</span></span>
* <span data-ttu-id="cdd0e-378">A linha de relação um para zero ou um (1 para 0..1) entre as entidades `Instructor` e `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-378">The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.</span></span>
* <span data-ttu-id="cdd0e-379">A linha de relação zero-ou-um-para-muitos (0..1 para \*) entre as entidades `Instructor` e `Department`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-379">The zero-or-one-to-many relationship line (0..1 to \*) between the `Instructor` and `Department` entities.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="cdd0e-380">Propagar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="cdd0e-380">Seed the database</span></span>

<span data-ttu-id="cdd0e-381">Atualize o código em *Data/DbInitializer.cs*:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-381">Update the code in *Data/DbInitializer.cs*:</span></span>

[!code-csharp[](intro/samples/cu30/Data/DbInitializer.cs)]

<span data-ttu-id="cdd0e-382">O código anterior fornece dados de semente para as novas entidades.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-382">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="cdd0e-383">A maioria desse código cria novos objetos de entidade e carrega dados de exemplo.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-383">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="cdd0e-384">Os dados de exemplo são usados para teste.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-384">The sample data is used for testing.</span></span> <span data-ttu-id="cdd0e-385">Consulte `Enrollments` e `CourseAssignments` para obter exemplos de como tabelas de junção muitos para muitos podem ser propagadas.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-385">See `Enrollments` and `CourseAssignments` for examples of how many-to-many join tables can be seeded.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="cdd0e-386">Adicionar uma migração</span><span class="sxs-lookup"><span data-stu-id="cdd0e-386">Add a migration</span></span>

<span data-ttu-id="cdd0e-387">Crie o projeto.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-387">Build the project.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="cdd0e-388">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cdd0e-388">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cdd0e-389">No PMC, execute o seguinte comando.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-389">In PMC, run the following command.</span></span>

```powershell
Add-Migration ComplexDataModel
```

<span data-ttu-id="cdd0e-390">O comando anterior exibe um aviso sobre a possível perda de dados.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-390">The preceding command displays a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="cdd0e-391">Se o comando `database update` é executado, o seguinte erro é produzido:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-391">If the `database update` command is run, the following error is produced:</span></span>

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

<span data-ttu-id="cdd0e-392">Na próxima seção, você verá o que fazer sobre esse erro.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-392">In the next section, you see what to do about this error.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="cdd0e-393">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cdd0e-393">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="cdd0e-394">Se você adicionar uma migração e executar o comando `database update`, o seguinte erro será produzido:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-394">If you add a migration and run the `database update` command, the following error would be produced:</span></span>

```text
SQLite does not support this migration operation ('DropForeignKeyOperation').
For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
```

<span data-ttu-id="cdd0e-395">Na próxima seção, você verá como evitar esse erro.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-395">In the next section, you see how to avoid this error.</span></span>

---

## <a name="apply-the-migration-or-drop-and-re-create"></a><span data-ttu-id="cdd0e-396">Aplicar a migração ou remover e recriar</span><span class="sxs-lookup"><span data-stu-id="cdd0e-396">Apply the migration or drop and re-create</span></span>

<span data-ttu-id="cdd0e-397">Agora que você tem um banco de dados existente, precisa pensar sobre como aplicar as alterações a ele.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-397">Now that you have an existing database, you need to think about how to apply changes to it.</span></span> <span data-ttu-id="cdd0e-398">Este tutorial mostra duas alternativas:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-398">This tutorial shows two alternatives:</span></span>

* <span data-ttu-id="cdd0e-399">[Remover e recriar o banco de dados](#drop).</span><span class="sxs-lookup"><span data-stu-id="cdd0e-399">[Drop and re-create the database](#drop).</span></span> <span data-ttu-id="cdd0e-400">Escolha esta seção se você estiver usando o SQLite.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-400">Choose this section if you're using SQLite.</span></span>
* <span data-ttu-id="cdd0e-401">[Aplicar a migração ao banco de dados existente](#applyexisting).</span><span class="sxs-lookup"><span data-stu-id="cdd0e-401">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="cdd0e-402">As instruções nesta seção funcionam apenas para SQL Server, **não para o SQLite**.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-402">The instructions in this section work for SQL Server only, **not for SQLite**.</span></span> 

<span data-ttu-id="cdd0e-403">Qualquer opção funciona para o SQL Server.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-403">Either choice works for SQL Server.</span></span> <span data-ttu-id="cdd0e-404">Embora o método apply-migration seja mais complexo e demorado, é a abordagem preferencial para ambientes de produção do mundo real.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-404">While the apply-migration method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span> 

<a name="drop"></a>

## <a name="drop-and-re-create-the-database"></a><span data-ttu-id="cdd0e-405">Remover e recriar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="cdd0e-405">Drop and re-create the database</span></span>

<span data-ttu-id="cdd0e-406">[Ignore esta seção](#apply-the-migration) se você estiver usando SQL Server e desejar fazer a abordagem de migração de aplicação na seção a seguir.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-406">[Skip this section](#apply-the-migration) if you're using SQL Server and want to do the apply-migration approach in the following section.</span></span>

<span data-ttu-id="cdd0e-407">Para forçar o EF Core a criar um novo banco de dados, remova e atualize o banco de dados:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-407">To force EF Core to create a new database, drop and update the database:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="cdd0e-408">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cdd0e-408">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="cdd0e-409">No **PMC** (Console do Gerenciador de Pacotes), execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-409">In the **Package Manager Console** (PMC), run the following command:</span></span>

  ```powershell
  Drop-Database
  ```

* <span data-ttu-id="cdd0e-410">Exclua a pasta *Migrations* e execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-410">Delete the *Migrations* folder, then run the following command:</span></span>

  ```powershell
  Add-Migration InitialCreate
  Update-Database
  ```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="cdd0e-411">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cdd0e-411">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="cdd0e-412">Abra uma janela Comando e navegue para a pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-412">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="cdd0e-413">A pasta do projeto contém o arquivo *ContosoUniversity.csproj*.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-413">The project folder contains the *ContosoUniversity.csproj* file.</span></span>

* <span data-ttu-id="cdd0e-414">Execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-414">Run the following command:</span></span>

  ```dotnetcli
  dotnet ef database drop --force
  ```

* <span data-ttu-id="cdd0e-415">Exclua a pasta *Migrations* e execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-415">Delete the *Migrations* folder, then run the following command:</span></span>

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

<span data-ttu-id="cdd0e-416">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-416">Run the app.</span></span> <span data-ttu-id="cdd0e-417">A execução do aplicativo executa o método `DbInitializer.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-417">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="cdd0e-418">O `DbInitializer.Initialize` preenche o novo banco de dados.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-418">The `DbInitializer.Initialize` populates the new database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="cdd0e-419">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cdd0e-419">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cdd0e-420">Abra o banco de dados no SSOX:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-420">Open the database in SSOX:</span></span>

* <span data-ttu-id="cdd0e-421">Se o SSOX for aberto anteriormente, clique no botão **Atualizar**.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-421">If SSOX was opened previously, click the **Refresh** button.</span></span>
* <span data-ttu-id="cdd0e-422">Expanda o nó **Tabelas**.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-422">Expand the **Tables** node.</span></span> <span data-ttu-id="cdd0e-423">As tabelas criadas são exibidas.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-423">The created tables are displayed.</span></span>

  ![Tabelas no SSOX](complex-data-model/_static/ssox-tables.png)

* <span data-ttu-id="cdd0e-425">Examine a tabela **CourseAssignment**:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-425">Examine the **CourseAssignment** table:</span></span>

  * <span data-ttu-id="cdd0e-426">Clique com o botão direito do mouse na tabela **CourseAssignment** e selecione **Exibir Dados**.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-426">Right-click the **CourseAssignment** table and select **View Data**.</span></span>
  * <span data-ttu-id="cdd0e-427">Verifique se a tabela **CourseAssignment** contém dados.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-427">Verify the **CourseAssignment** table contains data.</span></span>

  ![Dados de CourseAssignment no SSOX](complex-data-model/_static/ssox-ci-data.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="cdd0e-429">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cdd0e-429">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="cdd0e-430">Use sua ferramenta SQLite para examinar o banco de dados:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-430">Use your SQLite tool to examine the database:</span></span>

* <span data-ttu-id="cdd0e-431">Novas tabelas e colunas.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-431">New tables and columns.</span></span>
* <span data-ttu-id="cdd0e-432">Dados propagados em tabelas, por exemplo, a tabela **CourseAssignment**.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-432">Seeded data in tables, for example the **CourseAssignment** table.</span></span>

---

<a name="applyexisting"></a>

## <a name="apply-the-migration"></a><span data-ttu-id="cdd0e-433">Aplicar a migração</span><span class="sxs-lookup"><span data-stu-id="cdd0e-433">Apply the migration</span></span>

<span data-ttu-id="cdd0e-434">Esta seção é opcional.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-434">This section is optional.</span></span> <span data-ttu-id="cdd0e-435">Estas etapas só funcionarão para o LocalDB do SQL Server e apenas se você tiver ignorado a seção [Remover e recriar o banco de dados](#drop) anterior.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-435">These steps work only for SQL Server LocalDB and only if you skipped the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="cdd0e-436">Quando as migrações são executadas com os dados existentes, pode haver restrições de FK que não são atendidas com os dados existentes.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-436">When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data.</span></span> <span data-ttu-id="cdd0e-437">Com os dados de produção, é necessário executar etapas para migrar os dados existentes.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-437">With production data, steps must be taken to migrate the existing data.</span></span> <span data-ttu-id="cdd0e-438">Esta seção fornece um exemplo de correção de violações de restrição de FK.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-438">This section provides an example of fixing FK constraint violations.</span></span> <span data-ttu-id="cdd0e-439">Não faça essas alterações de código sem um backup.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-439">Don't make these code changes without a backup.</span></span> <span data-ttu-id="cdd0e-440">Não faça essas alterações de código se você tiver concluído a seção [Remover e recriar o banco de dados](#drop) anterior.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-440">Don't make these code changes if you completed the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="cdd0e-441">O arquivo *{timestamp}_ComplexDataModel.cs* contém o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-441">The *{timestamp}_ComplexDataModel.cs* file contains the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_DepartmentID)]

<span data-ttu-id="cdd0e-442">O código anterior adiciona uma FK `DepartmentID` que não permite valor nulo à tabela `Course`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-442">The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table.</span></span> <span data-ttu-id="cdd0e-443">O banco de dados do tutorial anterior contém linhas em `Course` e, portanto, essa tabela não pode ser atualizada por migrações.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-443">The database from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.</span></span>

<span data-ttu-id="cdd0e-444">Para fazer a migração `ComplexDataModel` funcionar com os dados existentes:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-444">To make the `ComplexDataModel` migration work with existing data:</span></span>

* <span data-ttu-id="cdd0e-445">Altere o código para dar à nova coluna (`DepartmentID`) um valor padrão.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-445">Change the code to give the new column (`DepartmentID`) a default value.</span></span>
* <span data-ttu-id="cdd0e-446">Crie um departamento fictício chamado "Temp" para atuar como o departamento padrão.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-446">Create a fake department named "Temp" to act as the default department.</span></span>

#### <a name="fix-the-foreign-key-constraints"></a><span data-ttu-id="cdd0e-447">Corrigir as restrições de chave estrangeira</span><span class="sxs-lookup"><span data-stu-id="cdd0e-447">Fix the foreign key constraints</span></span>

<span data-ttu-id="cdd0e-448">Na classe de migração `ComplexDataModel`, atualize o método `Up`:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-448">In the `ComplexDataModel` migration class, update the `Up` method:</span></span>

* <span data-ttu-id="cdd0e-449">Abra o arquivo *{timestamp}_ComplexDataModel.cs*.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-449">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>
* <span data-ttu-id="cdd0e-450">Comente a linha de código que adiciona a coluna `DepartmentID` à tabela `Course`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-450">Comment out the line of code that adds the `DepartmentID` column to the `Course` table.</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

<span data-ttu-id="cdd0e-451">Adicione o código realçado a seguir.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-451">Add the following highlighted code.</span></span> <span data-ttu-id="cdd0e-452">O novo código é inserido após o bloco `.CreateTable( name: "Department"`:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-452">The new code goes after the `.CreateTable( name: "Department"` block:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=23-31)]

<span data-ttu-id="cdd0e-453">Com as alterações anteriores, as linhas `Course` existentes estarão relacionadas ao departamento "Temp" após a execução do método `ComplexDataModel.Up`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-453">With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel.Up` method runs.</span></span>

<span data-ttu-id="cdd0e-454">A maneira de lidar com a situação mostrada aqui é simplificada para este tutorial.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-454">The way of handling the situation shown here is simplified for this tutorial.</span></span> <span data-ttu-id="cdd0e-455">Um aplicativo de produção:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-455">A production app would:</span></span>

* <span data-ttu-id="cdd0e-456">Inclui código ou scripts para adicionar linhas `Department` e linhas `Course` relacionadas às novas linhas `Department`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-456">Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.</span></span>
* <span data-ttu-id="cdd0e-457">Não usa o departamento "Temp" nem o valor padrão para `Course.DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-457">Not use the "Temp" department or the default value for `Course.DepartmentID`.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="cdd0e-458">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cdd0e-458">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="cdd0e-459">No **PMC** (Console do Gerenciador de Pacotes), execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-459">In the **Package Manager Console** (PMC), run the following command:</span></span>

  ```powershell
  Update-Database
  ```

<span data-ttu-id="cdd0e-460">Como o método `DbInitializer.Initialize` foi projetado para funcionar apenas com um banco de dados vazio, use SSOX para excluir todas as linhas nas tabelas Student e Course.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-460">Because the `DbInitializer.Initialize` method is designed to work only with an empty database, use SSOX to delete all the rows in the Student and Course tables.</span></span> <span data-ttu-id="cdd0e-461">(A exclusão em cascata cuidará da tabela de Registro.)</span><span class="sxs-lookup"><span data-stu-id="cdd0e-461">(Cascade delete will take care of the Enrollment table.)</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="cdd0e-462">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cdd0e-462">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="cdd0e-463">Se você estiver usando o SQL Server LocalDB com o Visual Studio Code, execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-463">If you're using SQL Server LocalDB with Visual Studio Code, run the following command:</span></span>

  ```dotnetcli
  dotnet ef database update
  ```

---

<span data-ttu-id="cdd0e-464">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-464">Run the app.</span></span> <span data-ttu-id="cdd0e-465">A execução do aplicativo executa o método `DbInitializer.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-465">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="cdd0e-466">O `DbInitializer.Initialize` preenche o novo banco de dados.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-466">The `DbInitializer.Initialize` populates the new database.</span></span>

## <a name="next-steps"></a><span data-ttu-id="cdd0e-467">{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}</span><span class="sxs-lookup"><span data-stu-id="cdd0e-467">Next steps</span></span>

<span data-ttu-id="cdd0e-468">Os próximos dois tutoriais mostram como ler e atualizar dados relacionados.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-468">The next two tutorials show how to read and update related data.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="cdd0e-469">[Tutorial anterior](xref:data/ef-rp/migrations)
> [Próximo tutorial](xref:data/ef-rp/read-related-data)</span><span class="sxs-lookup"><span data-stu-id="cdd0e-469">[Previous tutorial](xref:data/ef-rp/migrations)
[Next tutorial](xref:data/ef-rp/read-related-data)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cdd0e-470">Os tutoriais anteriores trabalharam com um modelo de dados básico composto por três entidades.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-470">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="cdd0e-471">Neste tutorial:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-471">In this tutorial:</span></span>

* <span data-ttu-id="cdd0e-472">Mais entidades e relações são adicionadas.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-472">More entities and relationships are added.</span></span>
* <span data-ttu-id="cdd0e-473">O modelo de dados é personalizado com a especificação das regras de formatação, validação e mapeamento de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-473">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="cdd0e-474">As classes de entidade para o modelo de dados concluído são mostradas na seguinte ilustração:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-474">The entity classes for the completed data model are shown in the following illustration:</span></span>

![Diagrama de entidade](complex-data-model/_static/diagram.png)

<span data-ttu-id="cdd0e-476">Caso tenha problemas que não consiga resolver, baixe o [aplicativo concluído](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="cdd0e-476">If you run into problems you can't solve, download the [completed app](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span>

## <a name="customize-the-data-model-with-attributes"></a><span data-ttu-id="cdd0e-477">Personalizar o modelo de dados com atributos</span><span class="sxs-lookup"><span data-stu-id="cdd0e-477">Customize the data model with attributes</span></span>

<span data-ttu-id="cdd0e-478">Nesta seção, o modelo de dados é personalizado com atributos.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-478">In this section, the data model is customized using attributes.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="cdd0e-479">O atributo DataType</span><span class="sxs-lookup"><span data-stu-id="cdd0e-479">The DataType attribute</span></span>

<span data-ttu-id="cdd0e-480">As páginas de alunos atualmente exibem a hora da data de registro.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-480">The student pages currently displays the time of the enrollment date.</span></span> <span data-ttu-id="cdd0e-481">Normalmente, os campos de data mostram apenas a data e não a hora.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-481">Typically, date fields show only the date and not the time.</span></span>

<span data-ttu-id="cdd0e-482">Atualize *Models/Student.cs* com o seguinte código realçado:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-482">Update *Models/Student.cs* with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

<span data-ttu-id="cdd0e-483">O atributo [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) especifica um tipo de dados mais específico do que o tipo intrínseco de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-483">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="cdd0e-484">Neste caso, apenas a data deve ser exibida, não a data e a hora.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-484">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="cdd0e-485">A [Enumeração DataType](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) fornece vários tipos de dados, como data, hora, PhoneNumber, moeda, EmailAddress, etc. O atributo `DataType` também pode habilitar o aplicativo para fornecer automaticamente recursos específicos do tipo.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-485">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="cdd0e-486">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-486">For example:</span></span>

* <span data-ttu-id="cdd0e-487">O link `mailto:` é criado automaticamente para `DataType.EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-487">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="cdd0e-488">O seletor de data é fornecido para `DataType.Date` na maioria dos navegadores.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-488">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="cdd0e-489">O atributo `DataType` emite atributos `data-` HTML 5 (pronunciados “data dash”) que são consumidos pelos navegadores HTML 5.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-489">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers consume.</span></span> <span data-ttu-id="cdd0e-490">Os atributos `DataType` não fornecem validação.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-490">The `DataType` attributes don't provide validation.</span></span>

<span data-ttu-id="cdd0e-491">`DataType.Date` não especifica o formato da data exibida.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-491">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="cdd0e-492">Por padrão, o campo de dados é exibido de acordo com os formatos padrão com base nas [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support) do servidor.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-492">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="cdd0e-493">O atributo `DisplayFormat` é usado para especificar explicitamente o formato de data:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-493">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="cdd0e-494">A configuração `ApplyFormatInEditMode` especifica que a formatação também deve ser aplicada à interface do usuário de edição.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-494">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="cdd0e-495">Alguns campos não devem usar `ApplyFormatInEditMode`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-495">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="cdd0e-496">Por exemplo, o símbolo de moeda geralmente não deve ser exibido em uma caixa de texto de edição.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-496">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="cdd0e-497">O atributo `DisplayFormat` pode ser usado por si só.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-497">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="cdd0e-498">Geralmente, é uma boa ideia usar o atributo `DataType` com o atributo `DisplayFormat`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-498">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="cdd0e-499">O atributo `DataType` transmite a semântica dos dados em vez de como renderizá-los em uma tela.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-499">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="cdd0e-500">O atributo `DataType` oferece os seguintes benefícios que não estão disponíveis em `DisplayFormat`:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-500">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="cdd0e-501">O navegador pode habilitar recursos do HTML5.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-501">The browser can enable HTML5 features.</span></span> <span data-ttu-id="cdd0e-502">Por exemplo, mostra um controle de calendário, o símbolo de moeda apropriado à localidade, links de email, validação de entrada do lado do cliente, etc.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-502">For example, show a calendar control, the locale-appropriate currency symbol, email links, client-side input validation, etc.</span></span>
* <span data-ttu-id="cdd0e-503">Por padrão, o navegador renderiza os dados usando o formato correto de acordo com a localidade.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-503">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="cdd0e-504">Para obter mais informações, consulte a [documentação do Auxiliar de Marcação \<input>](xref:mvc/views/working-with-forms#the-input-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="cdd0e-504">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

<span data-ttu-id="cdd0e-505">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-505">Run the app.</span></span> <span data-ttu-id="cdd0e-506">Navegue para a página Índice de Alunos.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-506">Navigate to the Students Index page.</span></span> <span data-ttu-id="cdd0e-507">As horas não são mais exibidas.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-507">Times are no longer displayed.</span></span> <span data-ttu-id="cdd0e-508">Cada exibição que usa o modelo `Student` exibe a data sem a hora.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-508">Every view that uses the `Student` model displays the date without time.</span></span>

![Página Índice de Alunos mostrando datas sem horas](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a><span data-ttu-id="cdd0e-510">O atributo StringLength</span><span class="sxs-lookup"><span data-stu-id="cdd0e-510">The StringLength attribute</span></span>

<span data-ttu-id="cdd0e-511">Regras de validação de dados e mensagens de erro de validação podem ser especificadas com atributos.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-511">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="cdd0e-512">O atributo [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) especifica o tamanho mínimo e máximo de caracteres permitidos em um campo de dados.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-512">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="cdd0e-513">O atributo `StringLength` também fornece a validação do lado do cliente e do servidor.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-513">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="cdd0e-514">O valor mínimo não tem impacto sobre o esquema de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-514">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="cdd0e-515">Atualize o modelo `Student` com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-515">Update the `Student` model with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

<span data-ttu-id="cdd0e-516">O código anterior limita os nomes a, no máximo, 50 caracteres.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-516">The preceding code limits names to no more than 50 characters.</span></span> <span data-ttu-id="cdd0e-517">O atributo `StringLength` não impede que um usuário insira um espaço em branco em um nome.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-517">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="cdd0e-518">O atributo [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) é usado para aplicar restrições à entrada.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-518">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) attribute is used to apply restrictions to the input.</span></span> <span data-ttu-id="cdd0e-519">Por exemplo, o seguinte código exige que o primeiro caractere esteja em maiúscula e os caracteres restantes estejam em ordem alfabética:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-519">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

<span data-ttu-id="cdd0e-520">Execute o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-520">Run the app:</span></span>

* <span data-ttu-id="cdd0e-521">Navegue para a página Alunos.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-521">Navigate to the Students page.</span></span>
* <span data-ttu-id="cdd0e-522">Selecione **Criar Novo** e insira um nome com mais de 50 caracteres.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-522">Select **Create New**, and enter a name longer than 50 characters.</span></span>
* <span data-ttu-id="cdd0e-523">Selecione **Criar** e a validação do lado do cliente mostrará uma mensagem de erro.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-523">Select **Create**, client-side validation shows an error message.</span></span>

![Página Índice de Alunos mostrando erros de tamanho de cadeia de caracteres](complex-data-model/_static/string-length-errors.png)

<span data-ttu-id="cdd0e-525">No **SSOX** (Pesquisador de Objetos do SQL Server), abra o designer de tabela Aluno clicando duas vezes na tabela **Aluno**.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-525">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![Tabela Alunos no SSOX antes das migrações](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="cdd0e-527">A imagem anterior mostra o esquema para a tabela `Student`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-527">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="cdd0e-528">Os campos de nome têm o tipo `nvarchar(MAX)` porque as migrações não foram executadas no BD.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-528">The name fields have type `nvarchar(MAX)` because migrations has not been run on the DB.</span></span> <span data-ttu-id="cdd0e-529">Quando as migrações forem executadas mais adiante neste tutorial, os campos de nome se tornarão `nvarchar(50)`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-529">When migrations are run later in this tutorial, the name fields become `nvarchar(50)`.</span></span>

### <a name="the-column-attribute"></a><span data-ttu-id="cdd0e-530">O atributo Column</span><span class="sxs-lookup"><span data-stu-id="cdd0e-530">The Column attribute</span></span>

<span data-ttu-id="cdd0e-531">Os atributos podem controlar como as classes e propriedades são mapeadas para o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-531">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="cdd0e-532">Nesta seção, o atributo `Column` é usado para mapear o nome da propriedade `FirstMidName` como "FirstName" no BD.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-532">In this section, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the DB.</span></span>

<span data-ttu-id="cdd0e-533">Quando o BD é criado, os nomes de propriedade no modelo são usados para nomes de coluna (exceto quando o atributo `Column` é usado).</span><span class="sxs-lookup"><span data-stu-id="cdd0e-533">When the DB is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span>

<span data-ttu-id="cdd0e-534">O modelo `Student` usa `FirstMidName` para o campo de nome porque o campo também pode conter um sobrenome.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-534">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="cdd0e-535">Atualize o arquivo *Student.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-535">Update the *Student.cs* file with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Column&highlight=4,14)]

<span data-ttu-id="cdd0e-536">Com a alteração anterior, `Student.FirstMidName` no aplicativo é mapeado para a coluna `FirstName` da tabela `Student`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-536">With the preceding change, `Student.FirstMidName` in the app maps to the `FirstName` column of the `Student` table.</span></span>

<span data-ttu-id="cdd0e-537">A adição do atributo `Column` altera o modelo que dá suporte ao `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-537">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="cdd0e-538">O modelo que dá suporte ao `SchoolContext` não corresponde mais ao banco de dados.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-538">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="cdd0e-539">Se o aplicativo for executado antes da aplicação das migrações, a seguinte exceção será gerada:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-539">If the app is run before applying migrations, the following exception is generated:</span></span>

```
SqlException: Invalid column name 'FirstName'.
```

<span data-ttu-id="cdd0e-540">Para atualizar o BD:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-540">To update the DB:</span></span>

* <span data-ttu-id="cdd0e-541">Crie o projeto.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-541">Build the project.</span></span>
* <span data-ttu-id="cdd0e-542">Abra uma janela Comando na pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-542">Open a command window in the project folder.</span></span> <span data-ttu-id="cdd0e-543">Insira os seguintes comandos para criar uma nova migração e atualizar o BD:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-543">Enter the following commands to create a new migration and update the DB:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="cdd0e-544">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cdd0e-544">Visual Studio</span></span>](#tab/visual-studio)

```powershell
Add-Migration ColumnFirstName
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="cdd0e-545">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cdd0e-545">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add ColumnFirstName
dotnet ef database update
```

---

<span data-ttu-id="cdd0e-546">O comando `migrations add ColumnFirstName` gera a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-546">The `migrations add ColumnFirstName` command generates the following warning message:</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
```

<span data-ttu-id="cdd0e-547">O aviso é gerado porque os campos de nome agora estão limitados a 50 caracteres.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-547">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="cdd0e-548">Se um nome no BD tiver mais de 50 caracteres, o 51º caractere até o último caractere serão perdidos.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-548">If a name in the DB had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="cdd0e-549">Teste o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-549">Test the app.</span></span>

<span data-ttu-id="cdd0e-550">Abra a tabela Alunos no SSOX:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-550">Open the Student table in SSOX:</span></span>

![Tabela Alunos no SSOX após as migrações](complex-data-model/_static/ssox-after-migration.png)

<span data-ttu-id="cdd0e-552">Antes de a migração ser aplicada, as colunas de nome eram do tipo [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span><span class="sxs-lookup"><span data-stu-id="cdd0e-552">Before migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="cdd0e-553">As colunas de nome agora são `nvarchar(50)`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-553">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="cdd0e-554">O nome da coluna foi alterado de `FirstMidName` para `FirstName`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-554">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

> [!Note]
> <span data-ttu-id="cdd0e-555">Na seção a seguir, a criação do aplicativo em alguns estágios gera erros do compilador.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-555">In the following section, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="cdd0e-556">As instruções especificam quando compilar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-556">The instructions specify when to build the app.</span></span>

## <a name="student-entity-update"></a><span data-ttu-id="cdd0e-557">Atualização da entidade Student</span><span class="sxs-lookup"><span data-stu-id="cdd0e-557">Student entity update</span></span>

![Entidade Student](complex-data-model/_static/student-entity.png)

<span data-ttu-id="cdd0e-559">Atualize *Models/Student.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-559">Update *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a><span data-ttu-id="cdd0e-560">O atributo Required</span><span class="sxs-lookup"><span data-stu-id="cdd0e-560">The Required attribute</span></span>

<span data-ttu-id="cdd0e-561">O atributo `Required` torna as propriedades de nome campos obrigatórios.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-561">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="cdd0e-562">O atributo `Required` não é necessário para tipos que não permitem valor nulo, como tipos de valor (`DateTime`, `int`, `double`, etc.).</span><span class="sxs-lookup"><span data-stu-id="cdd0e-562">The `Required` attribute isn't needed for non-nullable types such as value types (`DateTime`, `int`, `double`, etc.).</span></span> <span data-ttu-id="cdd0e-563">Tipos que não podem ser nulos são tratados automaticamente como campos obrigatórios.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-563">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="cdd0e-564">O atributo `Required` pode ser substituído por um parâmetro de tamanho mínimo no atributo `StringLength`:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-564">The `Required` attribute could be replaced with a minimum length parameter in the `StringLength` attribute:</span></span>

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a><span data-ttu-id="cdd0e-565">O atributo Display</span><span class="sxs-lookup"><span data-stu-id="cdd0e-565">The Display attribute</span></span>

<span data-ttu-id="cdd0e-566">O atributo `Display` especifica que a legenda para as caixas de texto deve ser "Nome", "Sobrenome", "Nome Completo" e "Data de Registro".</span><span class="sxs-lookup"><span data-stu-id="cdd0e-566">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="cdd0e-567">As legendas padrão não tinham nenhum espaço entre as palavras, por exemplo, "Lastname".</span><span class="sxs-lookup"><span data-stu-id="cdd0e-567">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="cdd0e-568">A propriedade calculada FullName</span><span class="sxs-lookup"><span data-stu-id="cdd0e-568">The FullName calculated property</span></span>

<span data-ttu-id="cdd0e-569">`FullName` é uma propriedade calculada que retorna um valor criado pela concatenação de duas outras propriedades.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-569">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="cdd0e-570">`FullName` não pode ser definido; ele apenas tem um acessador get.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-570">`FullName` cannot be set, it has only a get accessor.</span></span> <span data-ttu-id="cdd0e-571">Nenhuma coluna `FullName` é criada no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-571">No `FullName` column is created in the database.</span></span>

## <a name="create-the-instructor-entity"></a><span data-ttu-id="cdd0e-572">Criar a entidade Instructor</span><span class="sxs-lookup"><span data-stu-id="cdd0e-572">Create the Instructor Entity</span></span>

![Entidade Instructor](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="cdd0e-574">Crie *Models/Instructor.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-574">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Instructor.cs)]

<span data-ttu-id="cdd0e-575">Vários atributos podem estar em uma linha.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-575">Multiple attributes can be on one line.</span></span> <span data-ttu-id="cdd0e-576">Os atributos `HireDate` podem ser escritos da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-576">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a><span data-ttu-id="cdd0e-577">As propriedades de navegação CourseAssignments e OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="cdd0e-577">The CourseAssignments and OfficeAssignment navigation properties</span></span>

<span data-ttu-id="cdd0e-578">As propriedades `CourseAssignments` e `OfficeAssignment` são propriedades de navegação.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-578">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="cdd0e-579">Um instrutor pode ministrar qualquer quantidade de cursos e, portanto, `CourseAssignments` é definido como uma coleção.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-579">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="cdd0e-580">Se uma propriedade de navegação armazenar várias entidades:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-580">If a navigation property holds multiple entities:</span></span>

* <span data-ttu-id="cdd0e-581">Ele deve ser um tipo de lista no qual as entradas possam ser adicionadas, excluídas e atualizadas.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-581">It must be a list type where the entries can be added, deleted, and updated.</span></span>

<span data-ttu-id="cdd0e-582">Os tipos de propriedade de navegação incluem:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-582">Navigation property types include:</span></span>

* `ICollection<T>`
* `List<T>`
* `HashSet<T>`

<span data-ttu-id="cdd0e-583">Se `ICollection<T>` for especificado, o EF Core criará uma coleção `HashSet<T>` por padrão.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-583">If `ICollection<T>` is specified, EF Core creates a `HashSet<T>` collection by default.</span></span>

<span data-ttu-id="cdd0e-584">A entidade `CourseAssignment` é explicada na seção sobre relações muitos para muitos.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-584">The `CourseAssignment` entity is explained in the section on many-to-many relationships.</span></span>

<span data-ttu-id="cdd0e-585">Regras de negócio do Contoso University indicam que um instrutor pode ter, no máximo, um escritório.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-585">Contoso University business rules state that an instructor can have at most one office.</span></span> <span data-ttu-id="cdd0e-586">A propriedade `OfficeAssignment` contém uma única entidade `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-586">The `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="cdd0e-587">`OfficeAssignment` será nulo se nenhum escritório for atribuído.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-587">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-the-officeassignment-entity"></a><span data-ttu-id="cdd0e-588">Criar a entidade OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="cdd0e-588">Create the OfficeAssignment entity</span></span>

![Entidade OfficeAssignment](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="cdd0e-590">Crie *Models/OfficeAssignment.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-590">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="cdd0e-591">O atributo Key</span><span class="sxs-lookup"><span data-stu-id="cdd0e-591">The Key attribute</span></span>

<span data-ttu-id="cdd0e-592">O atributo `[Key]` é usado para identificar uma propriedade como a PK (chave primária) quando o nome da propriedade é algo diferente de classnameID ou ID.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-592">The `[Key]` attribute is used to identify a property as the primary key (PK) when the property name is something other than classnameID or ID.</span></span>

<span data-ttu-id="cdd0e-593">Há uma relação um para zero ou um entre as entidades `Instructor` e `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-593">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="cdd0e-594">Uma atribuição de escritório existe apenas em relação ao instrutor ao qual ela é atribuída.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-594">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="cdd0e-595">A PK `OfficeAssignment` também é a FK (chave estrangeira) da entidade `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-595">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span> <span data-ttu-id="cdd0e-596">O EF Core não pode reconhecer `InstructorID` automaticamente como o PK de `OfficeAssignment` porque:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-596">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because:</span></span>

* <span data-ttu-id="cdd0e-597">`InstructorID` não segue a convenção de nomenclatura de ID nem de classnameID.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-597">`InstructorID` doesn't follow the ID or classnameID naming convention.</span></span>

<span data-ttu-id="cdd0e-598">Portanto, o atributo `Key` é usado para identificar `InstructorID` como a PK:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-598">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="cdd0e-599">Por padrão, o EF Core trata a chave como não gerada pelo banco de dados porque a coluna destina-se a uma relação de identificação.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-599">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="cdd0e-600">A propriedade de navegação Instructor</span><span class="sxs-lookup"><span data-stu-id="cdd0e-600">The Instructor navigation property</span></span>

<span data-ttu-id="cdd0e-601">A propriedade de navegação `OfficeAssignment` da entidade `Instructor` permite valor nulo porque:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-601">The `OfficeAssignment` navigation property for the `Instructor` entity is nullable because:</span></span>

* <span data-ttu-id="cdd0e-602">Tipos de referência (como classes que permitem valor nulo).</span><span class="sxs-lookup"><span data-stu-id="cdd0e-602">Reference types (such as classes are nullable).</span></span>
* <span data-ttu-id="cdd0e-603">Um instrutor pode não ter uma atribuição de escritório.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-603">An instructor might not have an office assignment.</span></span>

<span data-ttu-id="cdd0e-604">A entidade `OfficeAssignment` tem uma propriedade de navegação `Instructor` que não permite valor nulo porque:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-604">The `OfficeAssignment` entity has a non-nullable `Instructor` navigation property because:</span></span>

* <span data-ttu-id="cdd0e-605">`InstructorID` não permite valor nulo.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-605">`InstructorID` is non-nullable.</span></span>
* <span data-ttu-id="cdd0e-606">Uma atribuição de escritório não pode existir sem um instrutor.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-606">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="cdd0e-607">Quando uma entidade `Instructor` tem uma entidade `OfficeAssignment` relacionada, cada entidade tem uma referência à outra em sua propriedade de navegação.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-607">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

<span data-ttu-id="cdd0e-608">O atributo `[Required]` pode ser aplicado à propriedade de navegação `Instructor`:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-608">The `[Required]` attribute could be applied to the `Instructor` navigation property:</span></span>

```csharp
[Required]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="cdd0e-609">O código anterior especifica que deve haver um instrutor relacionado.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-609">The preceding code specifies that there must be a related instructor.</span></span> <span data-ttu-id="cdd0e-610">O código anterior é desnecessário porque a chave estrangeira `InstructorID` (que também é a PK) não permite valor nulo.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-610">The preceding code is unnecessary because the `InstructorID` foreign key (which is also the PK) is non-nullable.</span></span>

## <a name="modify-the-course-entity"></a><span data-ttu-id="cdd0e-611">Modificar a entidade Course</span><span class="sxs-lookup"><span data-stu-id="cdd0e-611">Modify the Course Entity</span></span>

![Entidade Course](complex-data-model/_static/course-entity.png)

<span data-ttu-id="cdd0e-613">Atualize *Models/Course.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-613">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="cdd0e-614">A entidade `Course` tem uma propriedade de FK (chave estrangeira) `DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-614">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="cdd0e-615">`DepartmentID` aponta para a entidade `Department` relacionada.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-615">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="cdd0e-616">A entidade `Course` tem uma propriedade de navegação `Department`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-616">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="cdd0e-617">O EF Core não exige uma propriedade de FK para um modelo de dados quando o modelo tem uma propriedade de navegação para uma entidade relacionada.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-617">EF Core doesn't require a FK property for a data model when the model has a navigation property for a related entity.</span></span>

<span data-ttu-id="cdd0e-618">O EF Core cria automaticamente FKs no banco de dados sempre que forem necessárias.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-618">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="cdd0e-619">O EF Core cria [propriedades de sombra](/ef/core/modeling/shadow-properties) para FKs criadas automaticamente.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-619">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="cdd0e-620">Ter a FK no modelo de dados pode tornar as atualizações mais simples e mais eficientes.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-620">Having the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="cdd0e-621">Por exemplo, considere um modelo em que a propriedade de FK `DepartmentID`*não* é incluída.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-621">For example, consider a model where the FK property `DepartmentID` is *not* included.</span></span> <span data-ttu-id="cdd0e-622">Quando uma entidade de curso é buscada para editar:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-622">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="cdd0e-623">A entidade `Department` será nula se não for carregada de forma explícita.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-623">The `Department` entity is null if it's not explicitly loaded.</span></span>
* <span data-ttu-id="cdd0e-624">Para atualizar a entidade de curso, a entidade `Department` primeiro deve ser buscada.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-624">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="cdd0e-625">Quando a propriedade de FK `DepartmentID` está incluída no modelo de dados, não é necessário buscar a entidade `Department` antes de uma atualização.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-625">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="cdd0e-626">O atributo DatabaseGenerated</span><span class="sxs-lookup"><span data-stu-id="cdd0e-626">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="cdd0e-627">O atributo `[DatabaseGenerated(DatabaseGeneratedOption.None)]` especifica que a PK é fornecida pelo aplicativo em vez de ser gerada pelo banco de dados.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-627">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="cdd0e-628">Por padrão, o EF Core supõe que os valores de PK sejam gerados pelo BD.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-628">By default, EF Core assumes that PK values are generated by the DB.</span></span> <span data-ttu-id="cdd0e-629">Os valores de PK gerados pelo BD geralmente são a melhor abordagem.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-629">DB generated PK values is generally the best approach.</span></span> <span data-ttu-id="cdd0e-630">Para entidades `Course`, o usuário especifica o PK.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-630">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="cdd0e-631">Por exemplo, um número de curso, como uma série 1000 para o departamento de matemática e uma série 2000 para o departamento em inglês.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-631">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="cdd0e-632">O atributo `DatabaseGenerated` também pode ser usado para gerar valores padrão.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-632">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="cdd0e-633">Por exemplo, o BD pode gerar automaticamente um campo de data para registrar a data em que uma linha foi criada ou atualizada.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-633">For example, the DB can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="cdd0e-634">Para obter mais informações, consulte [Propriedades geradas](/ef/core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="cdd0e-634">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="cdd0e-635">Propriedades de navegação e de chave estrangeira</span><span class="sxs-lookup"><span data-stu-id="cdd0e-635">Foreign key and navigation properties</span></span>

<span data-ttu-id="cdd0e-636">As propriedades de navegação e de FK (chave estrangeira) na entidade `Course` refletem as seguintes relações:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-636">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="cdd0e-637">Um curso é atribuído a um departamento; portanto, há uma FK `DepartmentID` e uma propriedade de navegação `Department`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-637">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="cdd0e-638">Um curso pode ter qualquer quantidade de estudantes inscritos; portanto, a propriedade de navegação `Enrollments` é uma coleção:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-638">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="cdd0e-639">Um curso pode ser ministrado por vários instrutores; portanto, a propriedade de navegação `CourseAssignments` é uma coleção:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-639">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection:</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="cdd0e-640">`CourseAssignment` é explicado [posteriormente](#many-to-many-relationships).</span><span class="sxs-lookup"><span data-stu-id="cdd0e-640">`CourseAssignment` is explained [later](#many-to-many-relationships).</span></span>

## <a name="create-the-department-entity"></a><span data-ttu-id="cdd0e-641">Criar a entidade Department</span><span class="sxs-lookup"><span data-stu-id="cdd0e-641">Create the Department entity</span></span>

![Entidade Department](complex-data-model/_static/department-entity.png)

<span data-ttu-id="cdd0e-643">Crie *Models/Department.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-643">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a><span data-ttu-id="cdd0e-644">O atributo Column</span><span class="sxs-lookup"><span data-stu-id="cdd0e-644">The Column attribute</span></span>

<span data-ttu-id="cdd0e-645">Anteriormente, o atributo `Column` foi usado para alterar o mapeamento de nome de coluna.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-645">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="cdd0e-646">No código da entidade `Department`, o atributo `Column` é usado para alterar o mapeamento de tipo de dados SQL.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-646">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="cdd0e-647">A coluna `Budget` é definida usando o tipo de dinheiro do SQL Server no BD:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-647">The `Budget` column is defined using the SQL Server money type in the DB:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="cdd0e-648">Em geral, o mapeamento de coluna não é necessário.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-648">Column mapping is generally not required.</span></span> <span data-ttu-id="cdd0e-649">Em geral, o EF Core escolhe o tipo de dados do SQL Server apropriado com base no tipo CLR da propriedade.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-649">EF Core generally chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="cdd0e-650">O tipo `decimal` CLR é mapeado para um tipo `decimal` SQL Server.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-650">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="cdd0e-651">`Budget` refere-se à moeda e o tipo de dados de dinheiro é mais apropriado para moeda.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-651">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="cdd0e-652">Propriedades de navegação e de chave estrangeira</span><span class="sxs-lookup"><span data-stu-id="cdd0e-652">Foreign key and navigation properties</span></span>

<span data-ttu-id="cdd0e-653">As propriedades de navegação e de FK refletem as seguintes relações:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-653">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="cdd0e-654">Um departamento pode ou não ter um administrador.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-654">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="cdd0e-655">Um administrador é sempre um instrutor.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-655">An administrator is always an instructor.</span></span> <span data-ttu-id="cdd0e-656">Portanto, a propriedade `InstructorID` está incluída como a FK da entidade `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-656">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="cdd0e-657">A propriedade de navegação é chamada `Administrator`, mas contém uma entidade `Instructor`:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-657">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="cdd0e-658">O ponto de interrogação (?) no código anterior especifica que a propriedade permite valor nulo.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-658">The question mark (?) in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="cdd0e-659">Um departamento pode ter vários cursos e, portanto, há uma propriedade de navegação Courses:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-659">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="cdd0e-660">Observação: por convenção, o EF Core habilita a exclusão em cascata em FKs que não permitem valor nulo e em relações muitos para muitos.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-660">Note: By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="cdd0e-661">A exclusão em cascata pode resultar em regras de exclusão em cascata circular.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-661">Cascading delete can result in circular cascade delete rules.</span></span> <span data-ttu-id="cdd0e-662">As regras de exclusão em cascata circular causam uma exceção quando uma migração é adicionada.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-662">Circular cascade delete rules causes an exception when a migration is added.</span></span>

<span data-ttu-id="cdd0e-663">Por exemplo, se a propriedade `Department.InstructorID` tiver sido definida como não anulável:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-663">For example, if the `Department.InstructorID` property was defined as non-nullable:</span></span>

* <span data-ttu-id="cdd0e-664">O EF Core configura uma regra de exclusão em cascata para excluir o departamento quando o instrutor é excluído.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-664">EF Core configures a cascade delete rule to delete the department when the instructor is deleted.</span></span>
* <span data-ttu-id="cdd0e-665">Excluir o departamento quando o instrutor é excluído não é o comportamento desejado.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-665">Deleting the department when the instructor is deleted isn't the intended behavior.</span></span>
* <span data-ttu-id="cdd0e-666">A [API fluente](#fluent-api-alternative-to-attributes) a seguir definiria uma regra restrita em vez de Cascade.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-666">The following [fluent API](#fluent-api-alternative-to-attributes) would set a restrict rule instead of cascade.</span></span>

   ```csharp
   modelBuilder.Entity<Department>()
      .HasOne(d => d.Administrator)
      .WithMany()
      .OnDelete(DeleteBehavior.Restrict)
  ```

<span data-ttu-id="cdd0e-667">O código anterior desabilita a exclusão em cascata na relação departamento-instrutor.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-667">The preceding code disables cascade delete on the department-instructor relationship.</span></span>

## <a name="update-the-enrollment-entity"></a><span data-ttu-id="cdd0e-668">Atualizar a entidade Enrollment</span><span class="sxs-lookup"><span data-stu-id="cdd0e-668">Update the Enrollment entity</span></span>

<span data-ttu-id="cdd0e-669">Um registro se refere a um curso feito por um aluno.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-669">An enrollment record is for one course taken by one student.</span></span>

![Entidade Enrollment](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="cdd0e-671">Atualize *Models/Enrollment.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-671">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="cdd0e-672">Propriedades de navegação e de chave estrangeira</span><span class="sxs-lookup"><span data-stu-id="cdd0e-672">Foreign key and navigation properties</span></span>

<span data-ttu-id="cdd0e-673">As propriedades de navegação e de FK refletem as seguintes relações:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-673">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="cdd0e-674">Um registro destina-se a um curso e, portanto, há uma propriedade de FK `CourseID` e uma propriedade de navegação `Course`:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-674">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="cdd0e-675">Um registro destina-se a um aluno e, portanto, há uma propriedade de FK `StudentID` e uma propriedade de navegação `Student`:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-675">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="cdd0e-676">Relações muitos para muitos</span><span class="sxs-lookup"><span data-stu-id="cdd0e-676">Many-to-Many Relationships</span></span>

<span data-ttu-id="cdd0e-677">Há uma relação muitos para muitos entre as entidades `Student` e `Course`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-677">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="cdd0e-678">A entidade `Enrollment` funciona como uma tabela de junção muitos para muitos *com conteúdo* no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-678">The `Enrollment` entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="cdd0e-679">"Com conteúdo" significa que a tabela `Enrollment` contém dados adicionais além das FKs das tabelas unidas (nesse caso, a FK e `Grade`).</span><span class="sxs-lookup"><span data-stu-id="cdd0e-679">"With payload" means that the `Enrollment` table contains additional data besides FKs for the joined tables (in this case, the PK and `Grade`).</span></span>

<span data-ttu-id="cdd0e-680">A ilustração a seguir mostra a aparência dessas relações em um diagrama de entidades.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-680">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="cdd0e-681">(Esse diagrama foi gerado com o [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) para EF 6.x.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-681">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="cdd0e-682">A criação do diagrama não faz parte do tutorial.)</span><span class="sxs-lookup"><span data-stu-id="cdd0e-682">Creating the diagram isn't part of the tutorial.)</span></span>

![Relação muitos para muitos de Student-Course](complex-data-model/_static/student-course.png)

<span data-ttu-id="cdd0e-684">Cada linha de relação tem um 1 em uma extremidade e um asterisco (\*) na outra, indicando uma relação um para muitos.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-684">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="cdd0e-685">Se a tabela `Enrollment` não incluir informações de nota, ela apenas precisará conter as duas FKs (`CourseID` e `StudentID`).</span><span class="sxs-lookup"><span data-stu-id="cdd0e-685">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs (`CourseID` and `StudentID`).</span></span> <span data-ttu-id="cdd0e-686">Uma tabela de junção muitos para muitos sem conteúdo é às vezes chamada de PJT (uma tabela de junção pura).</span><span class="sxs-lookup"><span data-stu-id="cdd0e-686">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="cdd0e-687">As entidades `Instructor` e `Course` têm uma relação muitos para muitos usando uma tabela de junção pura.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-687">The `Instructor` and `Course` entities have a many-to-many relationship using a pure join table.</span></span>

<span data-ttu-id="cdd0e-688">Observação: o EF 6.x é compatível com tabelas de junção implícita para relações muitos para muitos, ao contrário do EF Core.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-688">Note: EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="cdd0e-689">Para obter mais informações, consulte [Relações muitos para muitos no EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span><span class="sxs-lookup"><span data-stu-id="cdd0e-689">For more information, see [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="cdd0e-690">A entidade CourseAssignment</span><span class="sxs-lookup"><span data-stu-id="cdd0e-690">The CourseAssignment entity</span></span>

![Entidade CourseAssignment](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="cdd0e-692">Crie *Models/CourseAssignment.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-692">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/CourseAssignment.cs)]

### <a name="instructor-to-courses"></a><span data-ttu-id="cdd0e-693">Instrutor para Cursos</span><span class="sxs-lookup"><span data-stu-id="cdd0e-693">Instructor-to-Courses</span></span>

![Instrutor para Cursos m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="cdd0e-695">A relação muitos para muitos de Instrutor para Cursos:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-695">The Instructor-to-Courses many-to-many relationship:</span></span>

* <span data-ttu-id="cdd0e-696">Exige que uma tabela de junção seja representada por um conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-696">Requires a join table that must be represented by an entity set.</span></span>
* <span data-ttu-id="cdd0e-697">É uma tabela de junção pura (tabela sem conteúdo).</span><span class="sxs-lookup"><span data-stu-id="cdd0e-697">Is a pure join table (table without payload).</span></span>

<span data-ttu-id="cdd0e-698">É comum nomear uma entidade de junção `EntityName1EntityName2`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-698">It's common to name a join entity `EntityName1EntityName2`.</span></span> <span data-ttu-id="cdd0e-699">Por exemplo, a tabela de junção Instrutor para Cursos com esse padrão é `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-699">For example, the Instructor-to-Courses join table using this pattern is `CourseInstructor`.</span></span> <span data-ttu-id="cdd0e-700">No entanto, recomendamos que você use um nome que descreve a relação.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-700">However, we recommend using a name that describes the relationship.</span></span>

<span data-ttu-id="cdd0e-701">Modelos de dados começam simples e aumentam.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-701">Data models start out simple and grow.</span></span> <span data-ttu-id="cdd0e-702">PJTs (junções sem conteúdo) evoluem com frequência para incluir o conteúdo.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-702">No-payload joins (PJTs) frequently evolve to include payload.</span></span> <span data-ttu-id="cdd0e-703">Começando com um nome descritivo de entidade, o nome não precisa ser alterado quando a tabela de junção é alterada.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-703">By starting with a descriptive entity name, the name doesn't need to change when the join table changes.</span></span> <span data-ttu-id="cdd0e-704">O ideal é que a entidade de junção tenha seu próprio nome natural (possivelmente, uma única palavra) no domínio de negócios.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-704">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="cdd0e-705">Por exemplo, Manuais e Clientes podem ser vinculados com uma entidade de junção chamada Ratings.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-705">For example, Books and Customers could be linked with a join entity called Ratings.</span></span> <span data-ttu-id="cdd0e-706">Para a relação muitos para muitos de Instrutor para Cursos, `CourseAssignment` é preferível a `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-706">For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="cdd0e-707">Chave composta</span><span class="sxs-lookup"><span data-stu-id="cdd0e-707">Composite key</span></span>

<span data-ttu-id="cdd0e-708">As FKs não permitem valor nulo.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-708">FKs are not nullable.</span></span> <span data-ttu-id="cdd0e-709">As duas FKs em `CourseAssignment` (`InstructorID` e `CourseID`) juntas identificam exclusivamente cada linha da tabela `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-709">The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table.</span></span> <span data-ttu-id="cdd0e-710">`CourseAssignment` não exige um PK dedicado.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-710">`CourseAssignment` doesn't require a dedicated PK.</span></span> <span data-ttu-id="cdd0e-711">As propriedades `InstructorID` e `CourseID` funcionam como uma PK composta.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-711">The `InstructorID` and `CourseID` properties function as a composite PK.</span></span> <span data-ttu-id="cdd0e-712">A única maneira de especificar PKs compostas no EF Core é com a *API fluente*.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-712">The only way to specify composite PKs to EF Core is with the *fluent API*.</span></span> <span data-ttu-id="cdd0e-713">A próxima seção mostra como configurar a PK composta.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-713">The next section shows how to configure the composite PK.</span></span>

<span data-ttu-id="cdd0e-714">A chave composta garante:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-714">The composite key ensures:</span></span>

* <span data-ttu-id="cdd0e-715">Várias linhas são permitidas para um curso.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-715">Multiple rows are allowed for one course.</span></span>
* <span data-ttu-id="cdd0e-716">Várias linhas são permitidas para um instrutor.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-716">Multiple rows are allowed for one instructor.</span></span>
* <span data-ttu-id="cdd0e-717">Não é permitido ter várias linhas para o mesmo instrutor e curso.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-717">Multiple rows for the same instructor and course isn't allowed.</span></span>

<span data-ttu-id="cdd0e-718">A entidade de junção `Enrollment` define sua própria PK e, portanto, duplicatas desse tipo são possíveis.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-718">The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible.</span></span> <span data-ttu-id="cdd0e-719">Para impedir duplicatas como essas:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-719">To prevent such duplicates:</span></span>

* <span data-ttu-id="cdd0e-720">Adicione um índice exclusivo nos campos de FK ou</span><span class="sxs-lookup"><span data-stu-id="cdd0e-720">Add a unique index on the FK fields, or</span></span>
* <span data-ttu-id="cdd0e-721">Configure `Enrollment` com uma chave primária composta semelhante a `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-721">Configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="cdd0e-722">Para obter mais informações, consulte [Índices](/ef/core/modeling/indexes).</span><span class="sxs-lookup"><span data-stu-id="cdd0e-722">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-db-context"></a><span data-ttu-id="cdd0e-723">Atualizar o contexto de BD</span><span class="sxs-lookup"><span data-stu-id="cdd0e-723">Update the DB context</span></span>

<span data-ttu-id="cdd0e-724">Adicione o seguinte código realçado a *Data/SchoolContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-724">Add the following highlighted code to *Data/SchoolContext.cs*:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

<span data-ttu-id="cdd0e-725">O código anterior adiciona novas entidades e configura a PK composta da entidade `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-725">The preceding code adds the new entities and configures the `CourseAssignment` entity's composite PK.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="cdd0e-726">Alternativa de API fluente para atributos</span><span class="sxs-lookup"><span data-stu-id="cdd0e-726">Fluent API alternative to attributes</span></span>

<span data-ttu-id="cdd0e-727">O método `OnModelCreating` no código anterior usa a *API fluente* para configurar o comportamento do EF Core.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-727">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="cdd0e-728">A API é chamada "fluente" porque geralmente é usada pelo encadeamento de uma série de chamadas de método em uma única instrução.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-728">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="cdd0e-729">O [seguinte código](/ef/core/modeling/#use-fluent-api-to-configure-a-model) é um exemplo da API fluente:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-729">The [following code](/ef/core/modeling/#use-fluent-api-to-configure-a-model) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="cdd0e-730">Neste tutorial, a API fluente é usada apenas para o mapeamento do BD que não pode ser feito com atributos.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-730">In this tutorial, the fluent API is used only for DB mapping that can't be done with attributes.</span></span> <span data-ttu-id="cdd0e-731">No entanto, a API fluente pode especificar a maioria das regras de formatação, validação e mapeamento que pode ser feita com atributos.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-731">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="cdd0e-732">Alguns atributos como `MinimumLength` não podem ser aplicados com a API fluente.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-732">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="cdd0e-733">`MinimumLength` não altera o esquema; apenas aplica uma regra de validação de tamanho mínimo.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-733">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="cdd0e-734">Alguns desenvolvedores preferem usar a API fluente exclusivamente para que possam manter suas classes de entidade "limpas".</span><span class="sxs-lookup"><span data-stu-id="cdd0e-734">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="cdd0e-735">Atributos e a API fluente podem ser combinados.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-735">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="cdd0e-736">Há algumas configurações que apenas podem ser feitas com a API fluente (especificando uma PK composta).</span><span class="sxs-lookup"><span data-stu-id="cdd0e-736">There are some configurations that can only be done with the fluent API (specifying a composite PK).</span></span> <span data-ttu-id="cdd0e-737">Há algumas configurações que apenas podem ser feitas com atributos (`MinimumLength`).</span><span class="sxs-lookup"><span data-stu-id="cdd0e-737">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="cdd0e-738">A prática recomendada para uso de atributos ou da API fluente:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-738">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="cdd0e-739">Escolha uma dessas duas abordagens.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-739">Choose one of these two approaches.</span></span>
* <span data-ttu-id="cdd0e-740">Use a abordagem escolhida da forma mais consistente possível.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-740">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="cdd0e-741">Alguns dos atributos usados neste tutorial são usados para:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-741">Some of the attributes used in the this tutorial are used for:</span></span>

* <span data-ttu-id="cdd0e-742">Somente validação (por exemplo, `MinimumLength`).</span><span class="sxs-lookup"><span data-stu-id="cdd0e-742">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="cdd0e-743">Apenas configuração do EF Core (por exemplo, `HasKey`).</span><span class="sxs-lookup"><span data-stu-id="cdd0e-743">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="cdd0e-744">Validação e configuração do EF Core (por exemplo, `[StringLength(50)]`).</span><span class="sxs-lookup"><span data-stu-id="cdd0e-744">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="cdd0e-745">Para obter mais informações sobre atributos vs. API fluente, consulte [Métodos de configuração](/ef/core/modeling/).</span><span class="sxs-lookup"><span data-stu-id="cdd0e-745">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/).</span></span>

## <a name="entity-diagram-showing-relationships"></a><span data-ttu-id="cdd0e-746">Diagrama de entidade mostrando relações</span><span class="sxs-lookup"><span data-stu-id="cdd0e-746">Entity Diagram Showing Relationships</span></span>

<span data-ttu-id="cdd0e-747">A ilustração a seguir mostra o diagrama criado pelo EF Power Tools para o modelo Escola concluído.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-747">The following illustration shows the diagram that EF Power Tools create for the completed School model.</span></span>

![Diagrama de entidade](complex-data-model/_static/diagram.png)

<span data-ttu-id="cdd0e-749">O diagrama anterior mostra:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-749">The preceding diagram shows:</span></span>

* <span data-ttu-id="cdd0e-750">Várias linhas de relação um-para-muitos (1 para \*).</span><span class="sxs-lookup"><span data-stu-id="cdd0e-750">Several one-to-many relationship lines (1 to \*).</span></span>
* <span data-ttu-id="cdd0e-751">A linha de relação um para zero ou um (1 para 0..1) entre as entidades `Instructor` e `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-751">The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.</span></span>
* <span data-ttu-id="cdd0e-752">A linha de relação zero-ou-um-para-muitos (0..1 para \*) entre as entidades `Instructor` e `Department`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-752">The zero-or-one-to-many relationship line (0..1 to \*) between the `Instructor` and `Department` entities.</span></span>

## <a name="seed-the-db-with-test-data"></a><span data-ttu-id="cdd0e-753">Propagar o BD com os dados de teste</span><span class="sxs-lookup"><span data-stu-id="cdd0e-753">Seed the DB with Test Data</span></span>

<span data-ttu-id="cdd0e-754">Atualize o código em *Data/DbInitializer.cs*:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-754">Update the code in *Data/DbInitializer.cs*:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Final)]

<span data-ttu-id="cdd0e-755">O código anterior fornece dados de semente para as novas entidades.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-755">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="cdd0e-756">A maioria desse código cria novos objetos de entidade e carrega dados de exemplo.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-756">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="cdd0e-757">Os dados de exemplo são usados para teste.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-757">The sample data is used for testing.</span></span> <span data-ttu-id="cdd0e-758">Consulte `Enrollments` e `CourseAssignments` para obter exemplos de como tabelas de junção muitos para muitos podem ser propagadas.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-758">See `Enrollments` and `CourseAssignments` for examples of how many-to-many join tables can be seeded.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="cdd0e-759">Adicionar uma migração</span><span class="sxs-lookup"><span data-stu-id="cdd0e-759">Add a migration</span></span>

<span data-ttu-id="cdd0e-760">Crie o projeto.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-760">Build the project.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="cdd0e-761">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cdd0e-761">Visual Studio</span></span>](#tab/visual-studio)

```powershell
Add-Migration ComplexDataModel
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="cdd0e-762">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cdd0e-762">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add ComplexDataModel
```

---

<span data-ttu-id="cdd0e-763">O comando anterior exibe um aviso sobre a possível perda de dados.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-763">The preceding command displays a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="cdd0e-764">Se o comando `database update` é executado, o seguinte erro é produzido:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-764">If the `database update` command is run, the following error is produced:</span></span>

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

## <a name="apply-the-migration"></a><span data-ttu-id="cdd0e-765">Aplicar a migração</span><span class="sxs-lookup"><span data-stu-id="cdd0e-765">Apply the migration</span></span>

<span data-ttu-id="cdd0e-766">Agora que você tem um banco de dados existente, precisa pensar sobre como aplicar as alterações futuras a ele.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-766">Now that you have an existing database, you need to think about how to apply future changes to it.</span></span> <span data-ttu-id="cdd0e-767">Este tutorial mostra duas abordagens:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-767">This tutorial shows two approaches:</span></span>

* [<span data-ttu-id="cdd0e-768">Remover e recriar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="cdd0e-768">Drop and re-create the database</span></span>](#drop)
* <span data-ttu-id="cdd0e-769">[Aplicar a migração ao banco de dados existente](#applyexisting).</span><span class="sxs-lookup"><span data-stu-id="cdd0e-769">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="cdd0e-770">Embora esse método seja mais complexo e demorado, é a abordagem preferencial para ambientes de produção do mundo real.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-770">While this method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span> <span data-ttu-id="cdd0e-771">**Observação**: essa é uma seção opcional do tutorial.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-771">**Note**: This is an optional section of the tutorial.</span></span> <span data-ttu-id="cdd0e-772">Você pode remover e recriar etapas e ignorar esta seção.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-772">You can do the drop and re-create steps and skip this section.</span></span> <span data-ttu-id="cdd0e-773">Se você quiser seguir as etapas nesta seção, não realize as etapas de remover e recriar.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-773">If you do want to follow the steps in this section, don't do the drop and re-create steps.</span></span> 

<a name="drop"></a>

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="cdd0e-774">Remover e recriar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="cdd0e-774">Drop and re-create the database</span></span>

<span data-ttu-id="cdd0e-775">O código no `DbInitializer` atualizado adiciona dados de semente às novas entidades.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-775">The code in the updated `DbInitializer` adds seed data for the new entities.</span></span> <span data-ttu-id="cdd0e-776">Para forçar o EF Core a criar um novo BD, remova e atualize o BD:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-776">To force EF Core to create a new  DB, drop and update the DB:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="cdd0e-777">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cdd0e-777">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cdd0e-778">No **PMC** (Console do Gerenciador de Pacotes), execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-778">In the **Package Manager Console** (PMC), run the following command:</span></span>

```powershell
Drop-Database
Update-Database
```

<span data-ttu-id="cdd0e-779">Execute `Get-Help about_EntityFrameworkCore` no PMC para obter informações de ajuda.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-779">Run `Get-Help about_EntityFrameworkCore` from the PMC to get help information.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="cdd0e-780">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cdd0e-780">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="cdd0e-781">Abra uma janela Comando e navegue para a pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-781">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="cdd0e-782">A pasta do projeto contém o arquivo *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-782">The project folder contains the *Startup.cs* file.</span></span>

<span data-ttu-id="cdd0e-783">Insira o seguinte na janela Comando:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-783">Enter the following in the command window:</span></span>

```dotnetcli
dotnet ef database drop
dotnet ef database update
```

---

<span data-ttu-id="cdd0e-784">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-784">Run the app.</span></span> <span data-ttu-id="cdd0e-785">A execução do aplicativo executa o método `DbInitializer.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-785">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="cdd0e-786">O `DbInitializer.Initialize` popula o novo BD.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-786">The `DbInitializer.Initialize` populates the new DB.</span></span>

<span data-ttu-id="cdd0e-787">Abra o BD no SSOX:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-787">Open the DB in SSOX:</span></span>

* <span data-ttu-id="cdd0e-788">Se o SSOX for aberto anteriormente, clique no botão **Atualizar**.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-788">If SSOX was opened previously, click the **Refresh** button.</span></span>
* <span data-ttu-id="cdd0e-789">Expanda o nó **Tabelas**.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-789">Expand the **Tables** node.</span></span> <span data-ttu-id="cdd0e-790">As tabelas criadas são exibidas.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-790">The created tables are displayed.</span></span>

![Tabelas no SSOX](complex-data-model/_static/ssox-tables.png)

<span data-ttu-id="cdd0e-792">Examine a tabela **CourseAssignment**:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-792">Examine the **CourseAssignment** table:</span></span>

* <span data-ttu-id="cdd0e-793">Clique com o botão direito do mouse na tabela **CourseAssignment** e selecione **Exibir Dados**.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-793">Right-click the **CourseAssignment** table and select **View Data**.</span></span>
* <span data-ttu-id="cdd0e-794">Verifique se a tabela **CourseAssignment** contém dados.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-794">Verify the **CourseAssignment** table contains data.</span></span>

![Dados de CourseAssignment no SSOX](complex-data-model/_static/ssox-ci-data.png)

<a name="applyexisting"></a>

### <a name="apply-the-migration-to-the-existing-database"></a><span data-ttu-id="cdd0e-796">Aplicar a migração ao banco de dados existente</span><span class="sxs-lookup"><span data-stu-id="cdd0e-796">Apply the migration to the existing database</span></span>

<span data-ttu-id="cdd0e-797">Esta seção é opcional.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-797">This section is optional.</span></span> <span data-ttu-id="cdd0e-798">Estas etapas só funcionarão se você tiver ignorado a seção [Remover e recriar o banco de dados](#drop) anterior.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-798">These steps work only if you skipped the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="cdd0e-799">Quando as migrações são executadas com os dados existentes, pode haver restrições de FK que não são atendidas com os dados existentes.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-799">When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data.</span></span> <span data-ttu-id="cdd0e-800">Com os dados de produção, é necessário executar etapas para migrar os dados existentes.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-800">With production data, steps must be taken to migrate the existing data.</span></span> <span data-ttu-id="cdd0e-801">Esta seção fornece um exemplo de correção de violações de restrição de FK.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-801">This section provides an example of fixing FK constraint violations.</span></span> <span data-ttu-id="cdd0e-802">Não faça essas alterações de código sem um backup.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-802">Don't make these code changes without a backup.</span></span> <span data-ttu-id="cdd0e-803">Não faça essas alterações de código se você concluir a seção anterior e atualizou o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-803">Don't make these code changes if you completed the previous section and updated the database.</span></span>

<span data-ttu-id="cdd0e-804">O arquivo *{timestamp}_ComplexDataModel.cs* contém o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-804">The *{timestamp}_ComplexDataModel.cs* file contains the following code:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_DepartmentID)]

<span data-ttu-id="cdd0e-805">O código anterior adiciona uma FK `DepartmentID` que não permite valor nulo à tabela `Course`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-805">The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table.</span></span> <span data-ttu-id="cdd0e-806">O BD do tutorial anterior contém linhas em `Course` e, portanto, essa tabela não pode ser atualizada por migrações.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-806">The DB from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.</span></span>

<span data-ttu-id="cdd0e-807">Para fazer a migração `ComplexDataModel` funcionar com os dados existentes:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-807">To make the `ComplexDataModel` migration work with existing data:</span></span>

* <span data-ttu-id="cdd0e-808">Altere o código para dar à nova coluna (`DepartmentID`) um valor padrão.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-808">Change the code to give the new column (`DepartmentID`) a default value.</span></span>
* <span data-ttu-id="cdd0e-809">Crie um departamento fictício chamado "Temp" para atuar como o departamento padrão.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-809">Create a fake department named "Temp" to act as the default department.</span></span>

#### <a name="fix-the-foreign-key-constraints"></a><span data-ttu-id="cdd0e-810">Corrigir as restrições de chave estrangeira</span><span class="sxs-lookup"><span data-stu-id="cdd0e-810">Fix the foreign key constraints</span></span>

<span data-ttu-id="cdd0e-811">Atualize o método `ComplexDataModel` das classes `Up`:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-811">Update the `ComplexDataModel` classes `Up` method:</span></span>

* <span data-ttu-id="cdd0e-812">Abra o arquivo *{timestamp}_ComplexDataModel.cs*.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-812">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>
* <span data-ttu-id="cdd0e-813">Comente a linha de código que adiciona a coluna `DepartmentID` à tabela `Course`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-813">Comment out the line of code that adds the `DepartmentID` column to the `Course` table.</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

<span data-ttu-id="cdd0e-814">Adicione o código realçado a seguir.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-814">Add the following highlighted code.</span></span> <span data-ttu-id="cdd0e-815">O novo código é inserido após o bloco `.CreateTable( name: "Department"`:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-815">The new code goes after the `.CreateTable( name: "Department"` block:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

<span data-ttu-id="cdd0e-816">Com as alterações anteriores, as linhas de `Course` existentes estarão relacionadas ao departamento "Temp" após a execução do método de `Up` de `ComplexDataModel`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-816">With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel` `Up` method runs.</span></span>

<span data-ttu-id="cdd0e-817">Um aplicativo de produção:</span><span class="sxs-lookup"><span data-stu-id="cdd0e-817">A production app would:</span></span>

* <span data-ttu-id="cdd0e-818">Inclui código ou scripts para adicionar linhas `Department` e linhas `Course` relacionadas às novas linhas `Department`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-818">Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.</span></span>
* <span data-ttu-id="cdd0e-819">Não usa o departamento "Temp" nem o valor padrão para `Course.DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-819">Not use the "Temp" department or the default value for `Course.DepartmentID`.</span></span>

<span data-ttu-id="cdd0e-820">O próximo tutorial abrange os dados relacionados.</span><span class="sxs-lookup"><span data-stu-id="cdd0e-820">The next tutorial covers related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cdd0e-821">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="cdd0e-821">Additional resources</span></span>

* [<span data-ttu-id="cdd0e-822">Versão do YouTube deste tutorial (Parte 1)</span><span class="sxs-lookup"><span data-stu-id="cdd0e-822">YouTube version of this tutorial(Part 1)</span></span>](https://www.youtube.com/watch?v=0n2f0ObgCoA)
* [<span data-ttu-id="cdd0e-823">Versão do YouTube deste tutorial (Parte 2)</span><span class="sxs-lookup"><span data-stu-id="cdd0e-823">YouTube version of this tutorial(Part 2)</span></span>](https://www.youtube.com/watch?v=Je0Z5K1TNmY)

> [!div class="step-by-step"]
> <span data-ttu-id="cdd0e-824">[Anterior](xref:data/ef-rp/migrations)
> [Próximo](xref:data/ef-rp/read-related-data)</span><span class="sxs-lookup"><span data-stu-id="cdd0e-824">[Previous](xref:data/ef-rp/migrations)
[Next](xref:data/ef-rp/read-related-data)</span></span>

::: moniker-end
