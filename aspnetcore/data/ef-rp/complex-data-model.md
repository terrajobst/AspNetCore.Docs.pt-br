---
title: Páginas Razor com o EF Core no ASP.NET Core – Modelo de dados – 5 de 8
author: rick-anderson
description: Neste tutorial, você adiciona mais entidades e relações e personaliza o modelo de dados especificando formatação, validação e regras de mapeamento.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: data/ef-rp/complex-data-model
ms.openlocfilehash: 311f72699b6291996a43d56247bd3d2bfab596e6
ms.sourcegitcommit: 088e6744cd67a62f214f25146313a53949b17d35
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58320242"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---data-model---5-of-8"></a><span data-ttu-id="67dce-103">Páginas Razor com o EF Core no ASP.NET Core – Modelo de dados – 5 de 8</span><span class="sxs-lookup"><span data-stu-id="67dce-103">Razor Pages with EF Core in ASP.NET Core - Data Model - 5 of 8</span></span>

[!INCLUDE[2.0 version](~/includes/RP-EF/20-pdf.md)]

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="67dce-104">Por [Tom Dykstra](https://github.com/tdykstra) e [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="67dce-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

<span data-ttu-id="67dce-105">Os tutoriais anteriores trabalharam com um modelo de dados básico composto por três entidades.</span><span class="sxs-lookup"><span data-stu-id="67dce-105">The previous tutorials worked with a basic data model that was composed of three entities.</span></span> <span data-ttu-id="67dce-106">Neste tutorial:</span><span class="sxs-lookup"><span data-stu-id="67dce-106">In this tutorial:</span></span>

* <span data-ttu-id="67dce-107">Mais entidades e relações são adicionadas.</span><span class="sxs-lookup"><span data-stu-id="67dce-107">More entities and relationships are added.</span></span>
* <span data-ttu-id="67dce-108">O modelo de dados é personalizado com a especificação das regras de formatação, validação e mapeamento de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="67dce-108">The data model is customized by specifying formatting, validation, and database mapping rules.</span></span>

<span data-ttu-id="67dce-109">As classes de entidade para o modelo de dados concluído são mostradas na seguinte ilustração:</span><span class="sxs-lookup"><span data-stu-id="67dce-109">The entity classes for the completed data model is shown in the following illustration:</span></span>

![Diagrama de entidade](complex-data-model/_static/diagram.png)

<span data-ttu-id="67dce-111">Caso tenha problemas que não consiga resolver, baixe o [aplicativo concluído](
https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="67dce-111">If you run into problems you can't solve, download the [completed app](
https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span>

## <a name="customize-the-data-model-with-attributes"></a><span data-ttu-id="67dce-112">Personalizar o modelo de dados com atributos</span><span class="sxs-lookup"><span data-stu-id="67dce-112">Customize the data model with attributes</span></span>

<span data-ttu-id="67dce-113">Nesta seção, o modelo de dados é personalizado com atributos.</span><span class="sxs-lookup"><span data-stu-id="67dce-113">In this section, the data model is customized using attributes.</span></span>

### <a name="the-datatype-attribute"></a><span data-ttu-id="67dce-114">O atributo DataType</span><span class="sxs-lookup"><span data-stu-id="67dce-114">The DataType attribute</span></span>

<span data-ttu-id="67dce-115">As páginas de alunos atualmente exibem a hora da data de registro.</span><span class="sxs-lookup"><span data-stu-id="67dce-115">The student pages currently displays the time of the enrollment date.</span></span> <span data-ttu-id="67dce-116">Normalmente, os campos de data mostram apenas a data e não a hora.</span><span class="sxs-lookup"><span data-stu-id="67dce-116">Typically, date fields show only the date and not the time.</span></span>

<span data-ttu-id="67dce-117">Atualize *Models/Student.cs* com o seguinte código realçado:</span><span class="sxs-lookup"><span data-stu-id="67dce-117">Update *Models/Student.cs* with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

<span data-ttu-id="67dce-118">O atributo [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) especifica um tipo de dados mais específico do que o tipo intrínseco de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="67dce-118">The [DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute?view=netframework-4.7.1) attribute specifies a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="67dce-119">Neste caso, apenas a data deve ser exibida, não a data e a hora.</span><span class="sxs-lookup"><span data-stu-id="67dce-119">In this case only the date should be displayed, not the date and time.</span></span> <span data-ttu-id="67dce-120">A [Enumeração DataType](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) fornece muitos tipos de dados, como Date, Time, PhoneNumber, Currency, EmailAddress, etc. O atributo `DataType` também pode permitir que o aplicativo forneça automaticamente recursos específicos a um tipo.</span><span class="sxs-lookup"><span data-stu-id="67dce-120">The [DataType Enumeration](/dotnet/api/system.componentmodel.dataannotations.datatype?view=netframework-4.7.1) provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, etc. The `DataType` attribute can also enable the app to automatically provide type-specific features.</span></span> <span data-ttu-id="67dce-121">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="67dce-121">For example:</span></span>

* <span data-ttu-id="67dce-122">O link `mailto:` é criado automaticamente para `DataType.EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="67dce-122">The `mailto:` link is automatically created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="67dce-123">O seletor de data é fornecido para `DataType.Date` na maioria dos navegadores.</span><span class="sxs-lookup"><span data-stu-id="67dce-123">The date selector is provided for `DataType.Date` in most browsers.</span></span>

<span data-ttu-id="67dce-124">O atributo `DataType` emite atributos `data-` HTML 5 (pronunciados “data dash”) que são consumidos pelos navegadores HTML 5.</span><span class="sxs-lookup"><span data-stu-id="67dce-124">The `DataType` attribute emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers consume.</span></span> <span data-ttu-id="67dce-125">Os atributos `DataType` não fornecem validação.</span><span class="sxs-lookup"><span data-stu-id="67dce-125">The `DataType` attributes don't provide validation.</span></span>

<span data-ttu-id="67dce-126">`DataType.Date` não especifica o formato da data exibida.</span><span class="sxs-lookup"><span data-stu-id="67dce-126">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="67dce-127">Por padrão, o campo de dados é exibido de acordo com os formatos padrão com base nas [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support) do servidor.</span><span class="sxs-lookup"><span data-stu-id="67dce-127">By default, the date field is displayed according to the default formats based on the server's [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support).</span></span>

<span data-ttu-id="67dce-128">O atributo `DisplayFormat` é usado para especificar explicitamente o formato de data:</span><span class="sxs-lookup"><span data-stu-id="67dce-128">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

<span data-ttu-id="67dce-129">A configuração `ApplyFormatInEditMode` especifica que a formatação também deve ser aplicada à interface do usuário de edição.</span><span class="sxs-lookup"><span data-stu-id="67dce-129">The `ApplyFormatInEditMode` setting specifies that the formatting should also be applied to the edit UI.</span></span> <span data-ttu-id="67dce-130">Alguns campos não devem usar `ApplyFormatInEditMode`.</span><span class="sxs-lookup"><span data-stu-id="67dce-130">Some fields shouldn't use `ApplyFormatInEditMode`.</span></span> <span data-ttu-id="67dce-131">Por exemplo, o símbolo de moeda geralmente não deve ser exibido em uma caixa de texto de edição.</span><span class="sxs-lookup"><span data-stu-id="67dce-131">For example, the currency symbol should generally not be displayed in an edit text box.</span></span>

<span data-ttu-id="67dce-132">O atributo `DisplayFormat` pode ser usado por si só.</span><span class="sxs-lookup"><span data-stu-id="67dce-132">The `DisplayFormat` attribute can be used by itself.</span></span> <span data-ttu-id="67dce-133">Geralmente, é uma boa ideia usar o atributo `DataType` com o atributo `DisplayFormat`.</span><span class="sxs-lookup"><span data-stu-id="67dce-133">It's generally a good idea to use the `DataType` attribute with the `DisplayFormat` attribute.</span></span> <span data-ttu-id="67dce-134">O atributo `DataType` transmite a semântica dos dados em vez de como renderizá-los em uma tela.</span><span class="sxs-lookup"><span data-stu-id="67dce-134">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="67dce-135">O atributo `DataType` oferece os seguintes benefícios que não estão disponíveis em `DisplayFormat`:</span><span class="sxs-lookup"><span data-stu-id="67dce-135">The `DataType` attribute provides the following benefits that are not available in `DisplayFormat`:</span></span>

* <span data-ttu-id="67dce-136">O navegador pode habilitar recursos do HTML5.</span><span class="sxs-lookup"><span data-stu-id="67dce-136">The browser can enable HTML5 features.</span></span> <span data-ttu-id="67dce-137">Por exemplo, mostra um controle de calendário, o símbolo de moeda apropriado à localidade, links de email, validação de entrada do lado do cliente, etc.</span><span class="sxs-lookup"><span data-stu-id="67dce-137">For example, show a calendar control, the locale-appropriate currency symbol, email links, client-side input validation, etc.</span></span>
* <span data-ttu-id="67dce-138">Por padrão, o navegador renderiza os dados usando o formato correto de acordo com a localidade.</span><span class="sxs-lookup"><span data-stu-id="67dce-138">By default, the browser renders data using the correct format based on the locale.</span></span>

<span data-ttu-id="67dce-139">Para obter mais informações, consulte a [documentação do Auxiliar de Marcação \<input>](xref:mvc/views/working-with-forms#the-input-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="67dce-139">For more information, see the [\<input> Tag Helper documentation](xref:mvc/views/working-with-forms#the-input-tag-helper).</span></span>

<span data-ttu-id="67dce-140">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="67dce-140">Run the app.</span></span> <span data-ttu-id="67dce-141">Navegue para a página Índice de Alunos.</span><span class="sxs-lookup"><span data-stu-id="67dce-141">Navigate to the Students Index page.</span></span> <span data-ttu-id="67dce-142">As horas não são mais exibidas.</span><span class="sxs-lookup"><span data-stu-id="67dce-142">Times are no longer displayed.</span></span> <span data-ttu-id="67dce-143">Cada exibição que usa o modelo `Student` exibe a data sem a hora.</span><span class="sxs-lookup"><span data-stu-id="67dce-143">Every view that uses the `Student` model displays the date without time.</span></span>

![Página Índice de Alunos mostrando datas sem horas](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a><span data-ttu-id="67dce-145">O atributo StringLength</span><span class="sxs-lookup"><span data-stu-id="67dce-145">The StringLength attribute</span></span>

<span data-ttu-id="67dce-146">Regras de validação de dados e mensagens de erro de validação podem ser especificadas com atributos.</span><span class="sxs-lookup"><span data-stu-id="67dce-146">Data validation rules and validation error messages can be specified with attributes.</span></span> <span data-ttu-id="67dce-147">O atributo [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) especifica o tamanho mínimo e máximo de caracteres permitidos em um campo de dados.</span><span class="sxs-lookup"><span data-stu-id="67dce-147">The [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute?view=netframework-4.7.1) attribute specifies the minimum and maximum length of characters that are allowed in a data field.</span></span> <span data-ttu-id="67dce-148">O atributo `StringLength` também fornece a validação do lado do cliente e do servidor.</span><span class="sxs-lookup"><span data-stu-id="67dce-148">The `StringLength` attribute also provides client-side and server-side validation.</span></span> <span data-ttu-id="67dce-149">O valor mínimo não tem impacto sobre o esquema de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="67dce-149">The minimum value has no impact on the database schema.</span></span>

<span data-ttu-id="67dce-150">Atualize o modelo `Student` com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="67dce-150">Update the `Student` model with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

<span data-ttu-id="67dce-151">O código anterior limita os nomes a, no máximo, 50 caracteres.</span><span class="sxs-lookup"><span data-stu-id="67dce-151">The preceding code limits names to no more than 50 characters.</span></span> <span data-ttu-id="67dce-152">O atributo `StringLength` não impede que um usuário insira um espaço em branco em um nome.</span><span class="sxs-lookup"><span data-stu-id="67dce-152">The `StringLength` attribute doesn't prevent a user from entering white space for a name.</span></span> <span data-ttu-id="67dce-153">O atributo [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) é usado para aplicar restrições à entrada.</span><span class="sxs-lookup"><span data-stu-id="67dce-153">The [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute?view=netframework-4.7.1) attribute is used to apply restrictions to the input.</span></span> <span data-ttu-id="67dce-154">Por exemplo, o seguinte código exige que o primeiro caractere esteja em maiúscula e os caracteres restantes estejam em ordem alfabética:</span><span class="sxs-lookup"><span data-stu-id="67dce-154">For example, the following code requires the first character to be upper case and the remaining characters to be alphabetical:</span></span>

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]
```

<span data-ttu-id="67dce-155">Execute o aplicativo:</span><span class="sxs-lookup"><span data-stu-id="67dce-155">Run the app:</span></span>

* <span data-ttu-id="67dce-156">Navegue para a página Alunos.</span><span class="sxs-lookup"><span data-stu-id="67dce-156">Navigate to the Students page.</span></span>
* <span data-ttu-id="67dce-157">Selecione **Criar Novo** e insira um nome com mais de 50 caracteres.</span><span class="sxs-lookup"><span data-stu-id="67dce-157">Select **Create New**, and enter a name longer than 50 characters.</span></span>
* <span data-ttu-id="67dce-158">Selecione **Criar** e a validação do lado do cliente mostrará uma mensagem de erro.</span><span class="sxs-lookup"><span data-stu-id="67dce-158">Select **Create**, client-side validation shows an error message.</span></span>

![Página Índice de Alunos mostrando erros de tamanho de cadeia de caracteres](complex-data-model/_static/string-length-errors.png)

<span data-ttu-id="67dce-160">No **SSOX** (Pesquisador de Objetos do SQL Server), abra o designer de tabela Aluno clicando duas vezes na tabela **Aluno**.</span><span class="sxs-lookup"><span data-stu-id="67dce-160">In **SQL Server Object Explorer** (SSOX), open the Student table designer by double-clicking the **Student** table.</span></span>

![Tabela Alunos no SSOX antes das migrações](complex-data-model/_static/ssox-before-migration.png)

<span data-ttu-id="67dce-162">A imagem anterior mostra o esquema para a tabela `Student`.</span><span class="sxs-lookup"><span data-stu-id="67dce-162">The preceding image shows the schema for the `Student` table.</span></span> <span data-ttu-id="67dce-163">Os campos de nome têm o tipo `nvarchar(MAX)` porque as migrações não foram executadas no BD.</span><span class="sxs-lookup"><span data-stu-id="67dce-163">The name fields have type `nvarchar(MAX)` because migrations has not been run on the DB.</span></span> <span data-ttu-id="67dce-164">Quando as migrações forem executadas mais adiante neste tutorial, os campos de nome se tornarão `nvarchar(50)`.</span><span class="sxs-lookup"><span data-stu-id="67dce-164">When migrations are run later in this tutorial, the name fields become `nvarchar(50)`.</span></span>

### <a name="the-column-attribute"></a><span data-ttu-id="67dce-165">O atributo Column</span><span class="sxs-lookup"><span data-stu-id="67dce-165">The Column attribute</span></span>

<span data-ttu-id="67dce-166">Os atributos podem controlar como as classes e propriedades são mapeadas para o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="67dce-166">Attributes can control how classes and properties are mapped to the database.</span></span> <span data-ttu-id="67dce-167">Nesta seção, o atributo `Column` é usado para mapear o nome da propriedade `FirstMidName` como "FirstName" no BD.</span><span class="sxs-lookup"><span data-stu-id="67dce-167">In this section, the `Column` attribute is used to map the name of the `FirstMidName` property to "FirstName" in the DB.</span></span>

<span data-ttu-id="67dce-168">Quando o BD é criado, os nomes de propriedade no modelo são usados para nomes de coluna (exceto quando o atributo `Column` é usado).</span><span class="sxs-lookup"><span data-stu-id="67dce-168">When the DB is created, property names on the model are used for column names (except when the `Column` attribute is used).</span></span>

<span data-ttu-id="67dce-169">O modelo `Student` usa `FirstMidName` para o campo de nome porque o campo também pode conter um sobrenome.</span><span class="sxs-lookup"><span data-stu-id="67dce-169">The `Student` model uses `FirstMidName` for the first-name field because the field might also contain a middle name.</span></span>

<span data-ttu-id="67dce-170">Atualize o arquivo *Student.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="67dce-170">Update the *Student.cs* file with the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Column&highlight=4,14)]

<span data-ttu-id="67dce-171">Com a alteração anterior, `Student.FirstMidName` no aplicativo é mapeado para a coluna `FirstName` da tabela `Student`.</span><span class="sxs-lookup"><span data-stu-id="67dce-171">With the preceding change, `Student.FirstMidName` in the app maps to the `FirstName` column of the `Student` table.</span></span>

<span data-ttu-id="67dce-172">A adição do atributo `Column` altera o modelo que dá suporte ao `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="67dce-172">The addition of the `Column` attribute changes the model backing the `SchoolContext`.</span></span> <span data-ttu-id="67dce-173">O modelo que dá suporte ao `SchoolContext` não corresponde mais ao banco de dados.</span><span class="sxs-lookup"><span data-stu-id="67dce-173">The model backing the `SchoolContext` no longer matches the database.</span></span> <span data-ttu-id="67dce-174">Se o aplicativo for executado antes da aplicação das migrações, a seguinte exceção será gerada:</span><span class="sxs-lookup"><span data-stu-id="67dce-174">If the app is run before applying migrations, the following exception is generated:</span></span>

```SQL
SqlException: Invalid column name 'FirstName'.
```

<span data-ttu-id="67dce-175">Para atualizar o BD:</span><span class="sxs-lookup"><span data-stu-id="67dce-175">To update the DB:</span></span>

* <span data-ttu-id="67dce-176">Compile o projeto.</span><span class="sxs-lookup"><span data-stu-id="67dce-176">Build the project.</span></span>
* <span data-ttu-id="67dce-177">Abra uma janela Comando na pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="67dce-177">Open a command window in the project folder.</span></span> <span data-ttu-id="67dce-178">Insira os seguintes comandos para criar uma nova migração e atualizar o BD:</span><span class="sxs-lookup"><span data-stu-id="67dce-178">Enter the following commands to create a new migration and update the DB:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="67dce-179">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="67dce-179">Visual Studio</span></span>](#tab/visual-studio)

```PMC
Add-Migration ColumnFirstName
Update-Database
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="67dce-180">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="67dce-180">.NET Core CLI</span></span>](#tab/netcore-cli)

```console
dotnet ef migrations add ColumnFirstName
dotnet ef database update
```

---

<span data-ttu-id="67dce-181">O comando `migrations add ColumnFirstName` gera a seguinte mensagem de aviso:</span><span class="sxs-lookup"><span data-stu-id="67dce-181">The `migrations add ColumnFirstName` command generates the following warning message:</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
```

<span data-ttu-id="67dce-182">O aviso é gerado porque os campos de nome agora estão limitados a 50 caracteres.</span><span class="sxs-lookup"><span data-stu-id="67dce-182">The warning is generated because the name fields are now limited to 50 characters.</span></span> <span data-ttu-id="67dce-183">Se um nome no BD tiver mais de 50 caracteres, o 51º caractere até o último caractere serão perdidos.</span><span class="sxs-lookup"><span data-stu-id="67dce-183">If a name in the DB had more than 50 characters, the 51 to last character would be lost.</span></span>

* <span data-ttu-id="67dce-184">Teste o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="67dce-184">Test the app.</span></span>

<span data-ttu-id="67dce-185">Abra a tabela Alunos no SSOX:</span><span class="sxs-lookup"><span data-stu-id="67dce-185">Open the Student table in SSOX:</span></span>

![Tabela Alunos no SSOX após as migrações](complex-data-model/_static/ssox-after-migration.png)

<span data-ttu-id="67dce-187">Antes de a migração ser aplicada, as colunas de nome eram do tipo [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span><span class="sxs-lookup"><span data-stu-id="67dce-187">Before migration was applied, the name columns were of type [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql).</span></span> <span data-ttu-id="67dce-188">As colunas de nome agora são `nvarchar(50)`.</span><span class="sxs-lookup"><span data-stu-id="67dce-188">The name columns are now `nvarchar(50)`.</span></span> <span data-ttu-id="67dce-189">O nome da coluna foi alterado de `FirstMidName` para `FirstName`.</span><span class="sxs-lookup"><span data-stu-id="67dce-189">The column name has changed from `FirstMidName` to `FirstName`.</span></span>

> [!Note]
> <span data-ttu-id="67dce-190">Na seção a seguir, a criação do aplicativo em alguns estágios gera erros do compilador.</span><span class="sxs-lookup"><span data-stu-id="67dce-190">In the following section, building the app at some stages generates compiler errors.</span></span> <span data-ttu-id="67dce-191">As instruções especificam quando compilar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="67dce-191">The instructions specify when to build the app.</span></span>

## <a name="student-entity-update"></a><span data-ttu-id="67dce-192">Atualização da entidade Student</span><span class="sxs-lookup"><span data-stu-id="67dce-192">Student entity update</span></span>

![Entidade Student](complex-data-model/_static/student-entity.png)

<span data-ttu-id="67dce-194">Atualize *Models/Student.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="67dce-194">Update *Models/Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a><span data-ttu-id="67dce-195">O atributo Required</span><span class="sxs-lookup"><span data-stu-id="67dce-195">The Required attribute</span></span>

<span data-ttu-id="67dce-196">O atributo `Required` torna as propriedades de nome campos obrigatórios.</span><span class="sxs-lookup"><span data-stu-id="67dce-196">The `Required` attribute makes the name properties required fields.</span></span> <span data-ttu-id="67dce-197">O atributo `Required` não é necessário para tipos que não permitem valor nulo, como tipos de valor (`DateTime`, `int`, `double`, etc.).</span><span class="sxs-lookup"><span data-stu-id="67dce-197">The `Required` attribute isn't needed for non-nullable types such as value types (`DateTime`, `int`, `double`, etc.).</span></span> <span data-ttu-id="67dce-198">Tipos que não podem ser nulos são tratados automaticamente como campos obrigatórios.</span><span class="sxs-lookup"><span data-stu-id="67dce-198">Types that can't be null are automatically treated as required fields.</span></span>

<span data-ttu-id="67dce-199">O atributo `Required` pode ser substituído por um parâmetro de tamanho mínimo no atributo `StringLength`:</span><span class="sxs-lookup"><span data-stu-id="67dce-199">The `Required` attribute could be replaced with a minimum length parameter in the `StringLength` attribute:</span></span>

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a><span data-ttu-id="67dce-200">O atributo Display</span><span class="sxs-lookup"><span data-stu-id="67dce-200">The Display attribute</span></span>

<span data-ttu-id="67dce-201">O atributo `Display` especifica que a legenda para as caixas de texto deve ser "Nome", "Sobrenome", "Nome Completo" e "Data de Registro".</span><span class="sxs-lookup"><span data-stu-id="67dce-201">The `Display` attribute specifies that the caption for the text boxes should be "First Name", "Last Name", "Full Name", and "Enrollment Date."</span></span> <span data-ttu-id="67dce-202">As legendas padrão não tinham nenhum espaço entre as palavras, por exemplo, "Lastname".</span><span class="sxs-lookup"><span data-stu-id="67dce-202">The default captions had no space dividing the words, for example "Lastname."</span></span>

### <a name="the-fullname-calculated-property"></a><span data-ttu-id="67dce-203">A propriedade calculada FullName</span><span class="sxs-lookup"><span data-stu-id="67dce-203">The FullName calculated property</span></span>

<span data-ttu-id="67dce-204">`FullName` é uma propriedade calculada que retorna um valor criado pela concatenação de duas outras propriedades.</span><span class="sxs-lookup"><span data-stu-id="67dce-204">`FullName` is a calculated property that returns a value that's created by concatenating two other properties.</span></span> <span data-ttu-id="67dce-205">`FullName` não pode ser definido; ele apenas tem um acessador get.</span><span class="sxs-lookup"><span data-stu-id="67dce-205">`FullName` cannot be set, it has only a get accessor.</span></span> <span data-ttu-id="67dce-206">Nenhuma coluna `FullName` é criada no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="67dce-206">No `FullName` column is created in the database.</span></span>

## <a name="create-the-instructor-entity"></a><span data-ttu-id="67dce-207">Criar a entidade Instructor</span><span class="sxs-lookup"><span data-stu-id="67dce-207">Create the Instructor Entity</span></span>

![Entidade Instructor](complex-data-model/_static/instructor-entity.png)

<span data-ttu-id="67dce-209">Crie *Models/Instructor.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="67dce-209">Create *Models/Instructor.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Instructor.cs)]

<span data-ttu-id="67dce-210">Vários atributos podem estar em uma linha.</span><span class="sxs-lookup"><span data-stu-id="67dce-210">Multiple attributes can be on one line.</span></span> <span data-ttu-id="67dce-211">Os atributos `HireDate` podem ser escritos da seguinte maneira:</span><span class="sxs-lookup"><span data-stu-id="67dce-211">The `HireDate` attributes could be written as follows:</span></span>

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a><span data-ttu-id="67dce-212">As propriedades de navegação CourseAssignments e OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="67dce-212">The CourseAssignments and OfficeAssignment navigation properties</span></span>

<span data-ttu-id="67dce-213">As propriedades `CourseAssignments` e `OfficeAssignment` são propriedades de navegação.</span><span class="sxs-lookup"><span data-stu-id="67dce-213">The `CourseAssignments` and `OfficeAssignment` properties are navigation properties.</span></span>

<span data-ttu-id="67dce-214">Um instrutor pode ministrar qualquer quantidade de cursos e, portanto, `CourseAssignments` é definido como uma coleção.</span><span class="sxs-lookup"><span data-stu-id="67dce-214">An instructor can teach any number of courses, so `CourseAssignments` is defined as a collection.</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="67dce-215">Se uma propriedade de navegação armazenar várias entidades:</span><span class="sxs-lookup"><span data-stu-id="67dce-215">If a navigation property holds multiple entities:</span></span>

* <span data-ttu-id="67dce-216">Ele deve ser um tipo de lista no qual as entradas possam ser adicionadas, excluídas e atualizadas.</span><span class="sxs-lookup"><span data-stu-id="67dce-216">It must be a list type where the entries can be added, deleted, and updated.</span></span>

<span data-ttu-id="67dce-217">Os tipos de propriedade de navegação incluem:</span><span class="sxs-lookup"><span data-stu-id="67dce-217">Navigation property types include:</span></span>

* `ICollection<T>`
* `List<T>`
* `HashSet<T>`

<span data-ttu-id="67dce-218">Se `ICollection<T>` for especificado, o EF Core criará uma coleção `HashSet<T>` por padrão.</span><span class="sxs-lookup"><span data-stu-id="67dce-218">If `ICollection<T>` is specified, EF Core creates a `HashSet<T>` collection by default.</span></span>

<span data-ttu-id="67dce-219">A entidade `CourseAssignment` é explicada na seção sobre relações muitos para muitos.</span><span class="sxs-lookup"><span data-stu-id="67dce-219">The `CourseAssignment` entity is explained in the section on many-to-many relationships.</span></span>

<span data-ttu-id="67dce-220">Regras de negócio do Contoso University indicam que um instrutor pode ter, no máximo, um escritório.</span><span class="sxs-lookup"><span data-stu-id="67dce-220">Contoso University business rules state that an instructor can have at most one office.</span></span> <span data-ttu-id="67dce-221">A propriedade `OfficeAssignment` contém uma única entidade `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="67dce-221">The `OfficeAssignment` property holds a single `OfficeAssignment` entity.</span></span> <span data-ttu-id="67dce-222">`OfficeAssignment` será nulo se nenhum escritório for atribuído.</span><span class="sxs-lookup"><span data-stu-id="67dce-222">`OfficeAssignment` is null if no office is assigned.</span></span>

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-the-officeassignment-entity"></a><span data-ttu-id="67dce-223">Criar a entidade OfficeAssignment</span><span class="sxs-lookup"><span data-stu-id="67dce-223">Create the OfficeAssignment entity</span></span>

![Entidade OfficeAssignment](complex-data-model/_static/officeassignment-entity.png)

<span data-ttu-id="67dce-225">Crie *Models/OfficeAssignment.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="67dce-225">Create *Models/OfficeAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a><span data-ttu-id="67dce-226">O atributo Key</span><span class="sxs-lookup"><span data-stu-id="67dce-226">The Key attribute</span></span>

<span data-ttu-id="67dce-227">O atributo `[Key]` é usado para identificar uma propriedade como a PK (chave primária) quando o nome da propriedade é algo diferente de classnameID ou ID.</span><span class="sxs-lookup"><span data-stu-id="67dce-227">The `[Key]` attribute is used to identify a property as the primary key (PK) when the property name is something other than classnameID or ID.</span></span>

<span data-ttu-id="67dce-228">Há uma relação um para zero ou um entre as entidades `Instructor` e `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="67dce-228">There's a one-to-zero-or-one relationship between the `Instructor` and `OfficeAssignment` entities.</span></span> <span data-ttu-id="67dce-229">Uma atribuição de escritório existe apenas em relação ao instrutor ao qual ela é atribuída.</span><span class="sxs-lookup"><span data-stu-id="67dce-229">An office assignment only exists in relation to the instructor it's assigned to.</span></span> <span data-ttu-id="67dce-230">A PK `OfficeAssignment` também é a FK (chave estrangeira) da entidade `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="67dce-230">The `OfficeAssignment` PK is also its foreign key (FK) to the `Instructor` entity.</span></span> <span data-ttu-id="67dce-231">O EF Core não pode reconhecer `InstructorID` automaticamente como o PK de `OfficeAssignment` porque:</span><span class="sxs-lookup"><span data-stu-id="67dce-231">EF Core can't automatically recognize `InstructorID` as the PK of `OfficeAssignment` because:</span></span>

* <span data-ttu-id="67dce-232">`InstructorID` não segue a convenção de nomenclatura de ID nem de classnameID.</span><span class="sxs-lookup"><span data-stu-id="67dce-232">`InstructorID` doesn't follow the ID or classnameID naming convention.</span></span>

<span data-ttu-id="67dce-233">Portanto, o atributo `Key` é usado para identificar `InstructorID` como a PK:</span><span class="sxs-lookup"><span data-stu-id="67dce-233">Therefore, the `Key` attribute is used to identify `InstructorID` as the PK:</span></span>

```csharp
[Key]
public int InstructorID { get; set; }
```

<span data-ttu-id="67dce-234">Por padrão, o EF Core trata a chave como não gerada pelo banco de dados porque a coluna destina-se a uma relação de identificação.</span><span class="sxs-lookup"><span data-stu-id="67dce-234">By default, EF Core treats the key as non-database-generated because the column is for an identifying relationship.</span></span>

### <a name="the-instructor-navigation-property"></a><span data-ttu-id="67dce-235">A propriedade de navegação Instructor</span><span class="sxs-lookup"><span data-stu-id="67dce-235">The Instructor navigation property</span></span>

<span data-ttu-id="67dce-236">A propriedade de navegação `OfficeAssignment` da entidade `Instructor` permite valor nulo porque:</span><span class="sxs-lookup"><span data-stu-id="67dce-236">The `OfficeAssignment` navigation property for the `Instructor` entity is nullable because:</span></span>

* <span data-ttu-id="67dce-237">Tipos de referência (como classes que permitem valor nulo).</span><span class="sxs-lookup"><span data-stu-id="67dce-237">Reference types (such as classes are nullable).</span></span>
* <span data-ttu-id="67dce-238">Um instrutor pode não ter uma atribuição de escritório.</span><span class="sxs-lookup"><span data-stu-id="67dce-238">An instructor might not have an office assignment.</span></span>

<span data-ttu-id="67dce-239">A entidade `OfficeAssignment` tem uma propriedade de navegação `Instructor` que não permite valor nulo porque:</span><span class="sxs-lookup"><span data-stu-id="67dce-239">The `OfficeAssignment` entity has a non-nullable `Instructor` navigation property because:</span></span>

* <span data-ttu-id="67dce-240">`InstructorID` não permite valor nulo.</span><span class="sxs-lookup"><span data-stu-id="67dce-240">`InstructorID` is non-nullable.</span></span>
* <span data-ttu-id="67dce-241">Uma atribuição de escritório não pode existir sem um instrutor.</span><span class="sxs-lookup"><span data-stu-id="67dce-241">An office assignment can't exist without an instructor.</span></span>

<span data-ttu-id="67dce-242">Quando uma entidade `Instructor` tem uma entidade `OfficeAssignment` relacionada, cada entidade tem uma referência à outra em sua propriedade de navegação.</span><span class="sxs-lookup"><span data-stu-id="67dce-242">When an `Instructor` entity has a related `OfficeAssignment` entity, each entity has a reference to the other one in its navigation property.</span></span>

<span data-ttu-id="67dce-243">O atributo `[Required]` pode ser aplicado à propriedade de navegação `Instructor`:</span><span class="sxs-lookup"><span data-stu-id="67dce-243">The `[Required]` attribute could be applied to the `Instructor` navigation property:</span></span>

```csharp
[Required]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="67dce-244">O código anterior especifica que deve haver um instrutor relacionado.</span><span class="sxs-lookup"><span data-stu-id="67dce-244">The preceding code specifies that there must be a related instructor.</span></span> <span data-ttu-id="67dce-245">O código anterior é desnecessário porque a chave estrangeira `InstructorID` (que também é a PK) não permite valor nulo.</span><span class="sxs-lookup"><span data-stu-id="67dce-245">The preceding code is unnecessary because the `InstructorID` foreign key (which is also the PK) is non-nullable.</span></span>

## <a name="modify-the-course-entity"></a><span data-ttu-id="67dce-246">Modificar a entidade Course</span><span class="sxs-lookup"><span data-stu-id="67dce-246">Modify the Course Entity</span></span>

![Entidade Course](complex-data-model/_static/course-entity.png)

<span data-ttu-id="67dce-248">Atualize *Models/Course.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="67dce-248">Update *Models/Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

<span data-ttu-id="67dce-249">A entidade `Course` tem uma propriedade de FK (chave estrangeira) `DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="67dce-249">The `Course` entity has a foreign key (FK) property `DepartmentID`.</span></span> <span data-ttu-id="67dce-250">`DepartmentID` aponta para a entidade `Department` relacionada.</span><span class="sxs-lookup"><span data-stu-id="67dce-250">`DepartmentID` points to the related `Department` entity.</span></span> <span data-ttu-id="67dce-251">A entidade `Course` tem uma propriedade de navegação `Department`.</span><span class="sxs-lookup"><span data-stu-id="67dce-251">The `Course` entity has a `Department` navigation property.</span></span>

<span data-ttu-id="67dce-252">O EF Core não exige uma propriedade de FK para um modelo de dados quando o modelo tem uma propriedade de navegação para uma entidade relacionada.</span><span class="sxs-lookup"><span data-stu-id="67dce-252">EF Core doesn't require a FK property for a data model when the model has a navigation property for a related entity.</span></span>

<span data-ttu-id="67dce-253">O EF Core cria automaticamente FKs no banco de dados sempre que forem necessárias.</span><span class="sxs-lookup"><span data-stu-id="67dce-253">EF Core automatically creates FKs in the database wherever they're needed.</span></span> <span data-ttu-id="67dce-254">O EF Core cria [propriedades de sombra](/ef/core/modeling/shadow-properties) para FKs criadas automaticamente.</span><span class="sxs-lookup"><span data-stu-id="67dce-254">EF Core creates [shadow properties](/ef/core/modeling/shadow-properties) for automatically created FKs.</span></span> <span data-ttu-id="67dce-255">Ter a FK no modelo de dados pode tornar as atualizações mais simples e mais eficientes.</span><span class="sxs-lookup"><span data-stu-id="67dce-255">Having the FK in the data model can make updates simpler and more efficient.</span></span> <span data-ttu-id="67dce-256">Por exemplo, considere um modelo em que a propriedade de FK `DepartmentID` *não* é incluída.</span><span class="sxs-lookup"><span data-stu-id="67dce-256">For example, consider a model where the FK property `DepartmentID` is *not* included.</span></span> <span data-ttu-id="67dce-257">Quando uma entidade de curso é buscada para editar:</span><span class="sxs-lookup"><span data-stu-id="67dce-257">When a course entity is fetched to edit:</span></span>

* <span data-ttu-id="67dce-258">A entidade `Department` será nula se não for carregada de forma explícita.</span><span class="sxs-lookup"><span data-stu-id="67dce-258">The `Department` entity is null if it's not explicitly loaded.</span></span>
* <span data-ttu-id="67dce-259">Para atualizar a entidade de curso, a entidade `Department` primeiro deve ser buscada.</span><span class="sxs-lookup"><span data-stu-id="67dce-259">To update the course entity, the `Department` entity must first be fetched.</span></span>

<span data-ttu-id="67dce-260">Quando a propriedade de FK `DepartmentID` está incluída no modelo de dados, não é necessário buscar a entidade `Department` antes de uma atualização.</span><span class="sxs-lookup"><span data-stu-id="67dce-260">When the FK property `DepartmentID` is included in the data model, there's no need to fetch the `Department` entity before an update.</span></span>

### <a name="the-databasegenerated-attribute"></a><span data-ttu-id="67dce-261">O atributo DatabaseGenerated</span><span class="sxs-lookup"><span data-stu-id="67dce-261">The DatabaseGenerated attribute</span></span>

<span data-ttu-id="67dce-262">O atributo `[DatabaseGenerated(DatabaseGeneratedOption.None)]` especifica que a PK é fornecida pelo aplicativo em vez de ser gerada pelo banco de dados.</span><span class="sxs-lookup"><span data-stu-id="67dce-262">The `[DatabaseGenerated(DatabaseGeneratedOption.None)]` attribute specifies that the PK is provided by the application rather than generated by the database.</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

<span data-ttu-id="67dce-263">Por padrão, o EF Core supõe que os valores de PK sejam gerados pelo BD.</span><span class="sxs-lookup"><span data-stu-id="67dce-263">By default, EF Core assumes that PK values are generated by the DB.</span></span> <span data-ttu-id="67dce-264">Os valores de PK gerados pelo BD geralmente são a melhor abordagem.</span><span class="sxs-lookup"><span data-stu-id="67dce-264">DB generated PK values is generally the best approach.</span></span> <span data-ttu-id="67dce-265">Para entidades `Course`, o usuário especifica o PK.</span><span class="sxs-lookup"><span data-stu-id="67dce-265">For `Course` entities, the user specifies the PK.</span></span> <span data-ttu-id="67dce-266">Por exemplo, um número de curso, como uma série 1000 para o departamento de matemática e uma série 2000 para o departamento em inglês.</span><span class="sxs-lookup"><span data-stu-id="67dce-266">For example, a course number such as a 1000 series for the math department, a 2000 series for the English department.</span></span>

<span data-ttu-id="67dce-267">O atributo `DatabaseGenerated` também pode ser usado para gerar valores padrão.</span><span class="sxs-lookup"><span data-stu-id="67dce-267">The `DatabaseGenerated` attribute can also be used to generate default values.</span></span> <span data-ttu-id="67dce-268">Por exemplo, o BD pode gerar automaticamente um campo de data para registrar a data em que uma linha foi criada ou atualizada.</span><span class="sxs-lookup"><span data-stu-id="67dce-268">For example, the DB can automatically generate a date field to record the date a row was created or updated.</span></span> <span data-ttu-id="67dce-269">Para obter mais informações, consulte [Propriedades geradas](/ef/core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="67dce-269">For more information, see [Generated Properties](/ef/core/modeling/generated-properties).</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="67dce-270">Propriedades de navegação e de chave estrangeira</span><span class="sxs-lookup"><span data-stu-id="67dce-270">Foreign key and navigation properties</span></span>

<span data-ttu-id="67dce-271">As propriedades de navegação e de FK (chave estrangeira) na entidade `Course` refletem as seguintes relações:</span><span class="sxs-lookup"><span data-stu-id="67dce-271">The foreign key (FK) properties and navigation properties in the `Course` entity reflect the following relationships:</span></span>

<span data-ttu-id="67dce-272">Um curso é atribuído a um departamento; portanto, há uma FK `DepartmentID` e uma propriedade de navegação `Department`.</span><span class="sxs-lookup"><span data-stu-id="67dce-272">A course is assigned to one department, so there's a `DepartmentID` FK and a `Department` navigation property.</span></span>

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

<span data-ttu-id="67dce-273">Um curso pode ter qualquer quantidade de estudantes inscritos; portanto, a propriedade de navegação `Enrollments` é uma coleção:</span><span class="sxs-lookup"><span data-stu-id="67dce-273">A course can have any number of students enrolled in it, so the `Enrollments` navigation property is a collection:</span></span>

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

<span data-ttu-id="67dce-274">Um curso pode ser ministrado por vários instrutores; portanto, a propriedade de navegação `CourseAssignments` é uma coleção:</span><span class="sxs-lookup"><span data-stu-id="67dce-274">A course may be taught by multiple instructors, so the `CourseAssignments` navigation property is a collection:</span></span>

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

<span data-ttu-id="67dce-275">`CourseAssignment` é explicado [posteriormente](#many-to-many-relationships).</span><span class="sxs-lookup"><span data-stu-id="67dce-275">`CourseAssignment` is explained [later](#many-to-many-relationships).</span></span>

## <a name="create-the-department-entity"></a><span data-ttu-id="67dce-276">Criar a entidade Department</span><span class="sxs-lookup"><span data-stu-id="67dce-276">Create the Department entity</span></span>

![Entidade Department](complex-data-model/_static/department-entity.png)

<span data-ttu-id="67dce-278">Crie *Models/Department.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="67dce-278">Create *Models/Department.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a><span data-ttu-id="67dce-279">O atributo Column</span><span class="sxs-lookup"><span data-stu-id="67dce-279">The Column attribute</span></span>

<span data-ttu-id="67dce-280">Anteriormente, o atributo `Column` foi usado para alterar o mapeamento de nome de coluna.</span><span class="sxs-lookup"><span data-stu-id="67dce-280">Previously the `Column` attribute was used to change column name mapping.</span></span> <span data-ttu-id="67dce-281">No código da entidade `Department`, o atributo `Column` é usado para alterar o mapeamento de tipo de dados SQL.</span><span class="sxs-lookup"><span data-stu-id="67dce-281">In the code for the `Department` entity, the `Column` attribute is used to change SQL data type mapping.</span></span> <span data-ttu-id="67dce-282">A coluna `Budget` é definida usando o tipo de dinheiro do SQL Server no BD:</span><span class="sxs-lookup"><span data-stu-id="67dce-282">The `Budget` column is defined using the SQL Server money type in the DB:</span></span>

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

<span data-ttu-id="67dce-283">Em geral, o mapeamento de coluna não é necessário.</span><span class="sxs-lookup"><span data-stu-id="67dce-283">Column mapping is generally not required.</span></span> <span data-ttu-id="67dce-284">Em geral, o EF Core escolhe o tipo de dados do SQL Server apropriado com base no tipo CLR da propriedade.</span><span class="sxs-lookup"><span data-stu-id="67dce-284">EF Core generally chooses the appropriate SQL Server data type based on the CLR type for the property.</span></span> <span data-ttu-id="67dce-285">O tipo `decimal` CLR é mapeado para um tipo `decimal` SQL Server.</span><span class="sxs-lookup"><span data-stu-id="67dce-285">The CLR `decimal` type maps to a SQL Server `decimal` type.</span></span> <span data-ttu-id="67dce-286">`Budget` refere-se à moeda e o tipo de dados de dinheiro é mais apropriado para moeda.</span><span class="sxs-lookup"><span data-stu-id="67dce-286">`Budget` is for currency, and the money data type is more appropriate for currency.</span></span>

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="67dce-287">Propriedades de navegação e de chave estrangeira</span><span class="sxs-lookup"><span data-stu-id="67dce-287">Foreign key and navigation properties</span></span>

<span data-ttu-id="67dce-288">As propriedades de navegação e de FK refletem as seguintes relações:</span><span class="sxs-lookup"><span data-stu-id="67dce-288">The FK and navigation properties reflect the following relationships:</span></span>

* <span data-ttu-id="67dce-289">Um departamento pode ou não ter um administrador.</span><span class="sxs-lookup"><span data-stu-id="67dce-289">A department may or may not have an administrator.</span></span>
* <span data-ttu-id="67dce-290">Um administrador é sempre um instrutor.</span><span class="sxs-lookup"><span data-stu-id="67dce-290">An administrator is always an instructor.</span></span> <span data-ttu-id="67dce-291">Portanto, a propriedade `InstructorID` está incluída como a FK da entidade `Instructor`.</span><span class="sxs-lookup"><span data-stu-id="67dce-291">Therefore the `InstructorID` property is included as the FK to the `Instructor` entity.</span></span>

<span data-ttu-id="67dce-292">A propriedade de navegação é chamada `Administrator`, mas contém uma entidade `Instructor`:</span><span class="sxs-lookup"><span data-stu-id="67dce-292">The navigation property is named `Administrator` but holds an `Instructor` entity:</span></span>

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

<span data-ttu-id="67dce-293">O ponto de interrogação (?) no código anterior especifica que a propriedade permite valor nulo.</span><span class="sxs-lookup"><span data-stu-id="67dce-293">The question mark (?) in the preceding code specifies the property is nullable.</span></span>

<span data-ttu-id="67dce-294">Um departamento pode ter vários cursos e, portanto, há uma propriedade de navegação Courses:</span><span class="sxs-lookup"><span data-stu-id="67dce-294">A department may have many courses, so there's a Courses navigation property:</span></span>

```csharp
public ICollection<Course> Courses { get; set; }
```

<span data-ttu-id="67dce-295">Observação: por convenção, o EF Core habilita a exclusão em cascata em FKs que não permitem valor nulo e em relações muitos para muitos.</span><span class="sxs-lookup"><span data-stu-id="67dce-295">Note: By convention, EF Core enables cascade delete for non-nullable FKs and for many-to-many relationships.</span></span> <span data-ttu-id="67dce-296">A exclusão em cascata pode resultar em regras de exclusão em cascata circular.</span><span class="sxs-lookup"><span data-stu-id="67dce-296">Cascading delete can result in circular cascade delete rules.</span></span> <span data-ttu-id="67dce-297">As regras de exclusão em cascata circular causam uma exceção quando uma migração é adicionada.</span><span class="sxs-lookup"><span data-stu-id="67dce-297">Circular cascade delete rules causes an exception when a migration is added.</span></span>

<span data-ttu-id="67dce-298">Por exemplo, se a propriedade `Department.InstructorID` não foi definida como uma propriedade que permite valor nulo:</span><span class="sxs-lookup"><span data-stu-id="67dce-298">For example, if the `Department.InstructorID` property wasn't defined as nullable:</span></span>

* <span data-ttu-id="67dce-299">O EF Core configura uma regra de exclusão em cascata para excluir o instrutor quando o departamento é excluído.</span><span class="sxs-lookup"><span data-stu-id="67dce-299">EF Core configures a cascade delete rule to delete the instructor when the department is deleted.</span></span>
* <span data-ttu-id="67dce-300">A exclusão do instrutor quando o departamento é excluído não é o comportamento pretendido.</span><span class="sxs-lookup"><span data-stu-id="67dce-300">Deleting the instructor when the department is deleted isn't the intended behavior.</span></span>

<span data-ttu-id="67dce-301">Se as regras de negócio exigirem que a propriedade `InstructorID` não permita valor nulo, use a seguinte instrução da API fluente:</span><span class="sxs-lookup"><span data-stu-id="67dce-301">If business rules required the `InstructorID` property be non-nullable, use the following fluent API statement:</span></span>

 ```csharp
 modelBuilder.Entity<Department>()
    .HasOne(d => d.Administrator)
    .WithMany()
    .OnDelete(DeleteBehavior.Restrict)
 ```

<span data-ttu-id="67dce-302">O código anterior desabilita a exclusão em cascata na relação departamento-instrutor.</span><span class="sxs-lookup"><span data-stu-id="67dce-302">The preceding code disables cascade delete on the department-instructor relationship.</span></span>

## <a name="update-the-enrollment-entity"></a><span data-ttu-id="67dce-303">Atualizar a entidade Enrollment</span><span class="sxs-lookup"><span data-stu-id="67dce-303">Update the Enrollment entity</span></span>

<span data-ttu-id="67dce-304">Um registro se refere a um curso feito por um aluno.</span><span class="sxs-lookup"><span data-stu-id="67dce-304">An enrollment record is for one course taken by one student.</span></span>

![Entidade Enrollment](complex-data-model/_static/enrollment-entity.png)

<span data-ttu-id="67dce-306">Atualize *Models/Enrollment.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="67dce-306">Update *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a><span data-ttu-id="67dce-307">Propriedades de navegação e de chave estrangeira</span><span class="sxs-lookup"><span data-stu-id="67dce-307">Foreign key and navigation properties</span></span>

<span data-ttu-id="67dce-308">As propriedades de navegação e de FK refletem as seguintes relações:</span><span class="sxs-lookup"><span data-stu-id="67dce-308">The FK properties and navigation properties reflect the following relationships:</span></span>

<span data-ttu-id="67dce-309">Um registro destina-se a um curso e, portanto, há uma propriedade de FK `CourseID` e uma propriedade de navegação `Course`:</span><span class="sxs-lookup"><span data-stu-id="67dce-309">An enrollment record is for one course, so there's a `CourseID` FK property and a `Course` navigation property:</span></span>

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

<span data-ttu-id="67dce-310">Um registro destina-se a um aluno e, portanto, há uma propriedade de FK `StudentID` e uma propriedade de navegação `Student`:</span><span class="sxs-lookup"><span data-stu-id="67dce-310">An enrollment record is for one student, so there's a `StudentID` FK property and a `Student` navigation property:</span></span>

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a><span data-ttu-id="67dce-311">Relações muitos para muitos</span><span class="sxs-lookup"><span data-stu-id="67dce-311">Many-to-Many Relationships</span></span>

<span data-ttu-id="67dce-312">Há uma relação muitos para muitos entre as entidades `Student` e `Course`.</span><span class="sxs-lookup"><span data-stu-id="67dce-312">There's a many-to-many relationship between the `Student` and `Course` entities.</span></span> <span data-ttu-id="67dce-313">A entidade `Enrollment` funciona como uma tabela de junção muitos para muitos *com conteúdo* no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="67dce-313">The `Enrollment` entity functions as a many-to-many join table *with payload* in the database.</span></span> <span data-ttu-id="67dce-314">"Com conteúdo" significa que a tabela `Enrollment` contém dados adicionais além das FKs das tabelas unidas (nesse caso, a FK e `Grade`).</span><span class="sxs-lookup"><span data-stu-id="67dce-314">"With payload" means that the `Enrollment` table contains additional data besides FKs for the joined tables (in this case, the PK and `Grade`).</span></span>

<span data-ttu-id="67dce-315">A ilustração a seguir mostra a aparência dessas relações em um diagrama de entidades.</span><span class="sxs-lookup"><span data-stu-id="67dce-315">The following illustration shows what these relationships look like in an entity diagram.</span></span> <span data-ttu-id="67dce-316">(Esse diagrama foi gerado com o [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) para EF 6.x.</span><span class="sxs-lookup"><span data-stu-id="67dce-316">(This diagram was generated using [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) for EF 6.x.</span></span> <span data-ttu-id="67dce-317">A criação do diagrama não faz parte do tutorial.)</span><span class="sxs-lookup"><span data-stu-id="67dce-317">Creating the diagram isn't part of the tutorial.)</span></span>

![Relação muitos para muitos de Student-Course](complex-data-model/_static/student-course.png)

<span data-ttu-id="67dce-319">Cada linha de relação tem um 1 em uma extremidade e um asterisco (\*) na outra, indicando uma relação um para muitos.</span><span class="sxs-lookup"><span data-stu-id="67dce-319">Each relationship line has a 1 at one end and an asterisk (\*) at the other, indicating a one-to-many relationship.</span></span>

<span data-ttu-id="67dce-320">Se a tabela `Enrollment` não incluir informações de nota, ela apenas precisará conter as duas FKs (`CourseID` e `StudentID`).</span><span class="sxs-lookup"><span data-stu-id="67dce-320">If the `Enrollment` table didn't include grade information, it would only need to contain the two FKs (`CourseID` and `StudentID`).</span></span> <span data-ttu-id="67dce-321">Uma tabela de junção muitos para muitos sem conteúdo é às vezes chamada de PJT (uma tabela de junção pura).</span><span class="sxs-lookup"><span data-stu-id="67dce-321">A many-to-many join table without payload is sometimes called a pure join table (PJT).</span></span>

<span data-ttu-id="67dce-322">As entidades `Instructor` e `Course` têm uma relação muitos para muitos usando uma tabela de junção pura.</span><span class="sxs-lookup"><span data-stu-id="67dce-322">The `Instructor` and `Course` entities have a many-to-many relationship using a pure join table.</span></span>

<span data-ttu-id="67dce-323">Observação: O EF 6.x é compatível com tabelas de junção implícita para relações muitos para muitos, ao contrário do EF Core.</span><span class="sxs-lookup"><span data-stu-id="67dce-323">Note: EF 6.x supports implicit join tables for many-to-many relationships, but EF Core doesn't.</span></span> <span data-ttu-id="67dce-324">Para obter mais informações, consulte [Relações muitos para muitos no EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span><span class="sxs-lookup"><span data-stu-id="67dce-324">For more information, see [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/).</span></span>

## <a name="the-courseassignment-entity"></a><span data-ttu-id="67dce-325">A entidade CourseAssignment</span><span class="sxs-lookup"><span data-stu-id="67dce-325">The CourseAssignment entity</span></span>

![Entidade CourseAssignment](complex-data-model/_static/courseassignment-entity.png)

<span data-ttu-id="67dce-327">Crie *Models/CourseAssignment.cs* com o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="67dce-327">Create *Models/CourseAssignment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/CourseAssignment.cs)]

### <a name="instructor-to-courses"></a><span data-ttu-id="67dce-328">Instrutor para Cursos</span><span class="sxs-lookup"><span data-stu-id="67dce-328">Instructor-to-Courses</span></span>

![Instrutor para Cursos m:M](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="67dce-330">A relação muitos para muitos de Instrutor para Cursos:</span><span class="sxs-lookup"><span data-stu-id="67dce-330">The Instructor-to-Courses many-to-many relationship:</span></span>

* <span data-ttu-id="67dce-331">Exige que uma tabela de junção seja representada por um conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="67dce-331">Requires a join table that must be represented by an entity set.</span></span>
* <span data-ttu-id="67dce-332">É uma tabela de junção pura (tabela sem conteúdo).</span><span class="sxs-lookup"><span data-stu-id="67dce-332">Is a pure join table (table without payload).</span></span>

<span data-ttu-id="67dce-333">É comum nomear uma entidade de junção `EntityName1EntityName2`.</span><span class="sxs-lookup"><span data-stu-id="67dce-333">It's common to name a join entity `EntityName1EntityName2`.</span></span> <span data-ttu-id="67dce-334">Por exemplo, a tabela de junção Instrutor para Cursos com esse padrão é `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="67dce-334">For example, the Instructor-to-Courses join table using this pattern is `CourseInstructor`.</span></span> <span data-ttu-id="67dce-335">No entanto, recomendamos que você use um nome que descreve a relação.</span><span class="sxs-lookup"><span data-stu-id="67dce-335">However, we recommend using a name that describes the relationship.</span></span>

<span data-ttu-id="67dce-336">Modelos de dados começam simples e aumentam.</span><span class="sxs-lookup"><span data-stu-id="67dce-336">Data models start out simple and grow.</span></span> <span data-ttu-id="67dce-337">PJTs (junções sem conteúdo) evoluem com frequência para incluir o conteúdo.</span><span class="sxs-lookup"><span data-stu-id="67dce-337">No-payload joins (PJTs) frequently evolve to include payload.</span></span> <span data-ttu-id="67dce-338">Começando com um nome descritivo de entidade, o nome não precisa ser alterado quando a tabela de junção é alterada.</span><span class="sxs-lookup"><span data-stu-id="67dce-338">By starting with a descriptive entity name, the name doesn't need to change when the join table changes.</span></span> <span data-ttu-id="67dce-339">O ideal é que a entidade de junção tenha seu próprio nome natural (possivelmente, uma única palavra) no domínio de negócios.</span><span class="sxs-lookup"><span data-stu-id="67dce-339">Ideally, the join entity would have its own natural (possibly single word) name in the business domain.</span></span> <span data-ttu-id="67dce-340">Por exemplo, Manuais e Clientes podem ser vinculados com uma entidade de junção chamada Ratings.</span><span class="sxs-lookup"><span data-stu-id="67dce-340">For example, Books and Customers could be linked with a join entity called Ratings.</span></span> <span data-ttu-id="67dce-341">Para a relação muitos para muitos de Instrutor para Cursos, `CourseAssignment` é preferível a `CourseInstructor`.</span><span class="sxs-lookup"><span data-stu-id="67dce-341">For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.</span></span>

### <a name="composite-key"></a><span data-ttu-id="67dce-342">Chave composta</span><span class="sxs-lookup"><span data-stu-id="67dce-342">Composite key</span></span>

<span data-ttu-id="67dce-343">As FKs não permitem valor nulo.</span><span class="sxs-lookup"><span data-stu-id="67dce-343">FKs are not nullable.</span></span> <span data-ttu-id="67dce-344">As duas FKs em `CourseAssignment` (`InstructorID` e `CourseID`) juntas identificam exclusivamente cada linha da tabela `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="67dce-344">The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table.</span></span> <span data-ttu-id="67dce-345">`CourseAssignment` não exige um PK dedicado.</span><span class="sxs-lookup"><span data-stu-id="67dce-345">`CourseAssignment` doesn't require a dedicated PK.</span></span> <span data-ttu-id="67dce-346">As propriedades `InstructorID` e `CourseID` funcionam como uma PK composta.</span><span class="sxs-lookup"><span data-stu-id="67dce-346">The `InstructorID` and `CourseID` properties function as a composite PK.</span></span> <span data-ttu-id="67dce-347">A única maneira de especificar PKs compostas no EF Core é com a *API fluente*.</span><span class="sxs-lookup"><span data-stu-id="67dce-347">The only way to specify composite PKs to EF Core is with the *fluent API*.</span></span> <span data-ttu-id="67dce-348">A próxima seção mostra como configurar a PK composta.</span><span class="sxs-lookup"><span data-stu-id="67dce-348">The next section shows how to configure the composite PK.</span></span>

<span data-ttu-id="67dce-349">A chave composta garante:</span><span class="sxs-lookup"><span data-stu-id="67dce-349">The composite key ensures:</span></span>

* <span data-ttu-id="67dce-350">Várias linhas são permitidas para um curso.</span><span class="sxs-lookup"><span data-stu-id="67dce-350">Multiple rows are allowed for one course.</span></span>
* <span data-ttu-id="67dce-351">Várias linhas são permitidas para um instrutor.</span><span class="sxs-lookup"><span data-stu-id="67dce-351">Multiple rows are allowed for one instructor.</span></span>
* <span data-ttu-id="67dce-352">Não é permitido ter várias linhas para o mesmo instrutor e curso.</span><span class="sxs-lookup"><span data-stu-id="67dce-352">Multiple rows for the same instructor and course isn't allowed.</span></span>

<span data-ttu-id="67dce-353">A entidade de junção `Enrollment` define sua própria PK e, portanto, duplicatas desse tipo são possíveis.</span><span class="sxs-lookup"><span data-stu-id="67dce-353">The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible.</span></span> <span data-ttu-id="67dce-354">Para impedir duplicatas como essas:</span><span class="sxs-lookup"><span data-stu-id="67dce-354">To prevent such duplicates:</span></span>

* <span data-ttu-id="67dce-355">Adicione um índice exclusivo nos campos de FK ou</span><span class="sxs-lookup"><span data-stu-id="67dce-355">Add a unique index on the FK fields, or</span></span>
* <span data-ttu-id="67dce-356">Configure `Enrollment` com uma chave primária composta semelhante a `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="67dce-356">Configure `Enrollment` with a primary composite key similar to `CourseAssignment`.</span></span> <span data-ttu-id="67dce-357">Para obter mais informações, consulte [Índices](/ef/core/modeling/indexes).</span><span class="sxs-lookup"><span data-stu-id="67dce-357">For more information, see [Indexes](/ef/core/modeling/indexes).</span></span>

## <a name="update-the-db-context"></a><span data-ttu-id="67dce-358">Atualizar o contexto de BD</span><span class="sxs-lookup"><span data-stu-id="67dce-358">Update the DB context</span></span>

<span data-ttu-id="67dce-359">Adicione o seguinte código realçado a *Data/SchoolContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="67dce-359">Add the following highlighted code to *Data/SchoolContext.cs*:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

<span data-ttu-id="67dce-360">O código anterior adiciona novas entidades e configura a PK composta da entidade `CourseAssignment`.</span><span class="sxs-lookup"><span data-stu-id="67dce-360">The preceding code adds the new entities and configures the `CourseAssignment` entity's composite PK.</span></span>

## <a name="fluent-api-alternative-to-attributes"></a><span data-ttu-id="67dce-361">Alternativa de API fluente para atributos</span><span class="sxs-lookup"><span data-stu-id="67dce-361">Fluent API alternative to attributes</span></span>

<span data-ttu-id="67dce-362">O método `OnModelCreating` no código anterior usa a *API fluente* para configurar o comportamento do EF Core.</span><span class="sxs-lookup"><span data-stu-id="67dce-362">The `OnModelCreating` method in the preceding code uses the *fluent API* to configure EF Core behavior.</span></span> <span data-ttu-id="67dce-363">A API é chamada "fluente" porque geralmente é usada pelo encadeamento de uma série de chamadas de método em uma única instrução.</span><span class="sxs-lookup"><span data-stu-id="67dce-363">The API is called "fluent" because it's often used by stringing a series of method calls together into a single statement.</span></span> <span data-ttu-id="67dce-364">O [seguinte código](/ef/core/modeling/#methods-of-configuration) é um exemplo da API fluente:</span><span class="sxs-lookup"><span data-stu-id="67dce-364">The [following code](/ef/core/modeling/#methods-of-configuration) is an example of the fluent API:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

<span data-ttu-id="67dce-365">Neste tutorial, a API fluente é usada apenas para o mapeamento do BD que não pode ser feito com atributos.</span><span class="sxs-lookup"><span data-stu-id="67dce-365">In this tutorial, the fluent API is used only for DB mapping that can't be done with attributes.</span></span> <span data-ttu-id="67dce-366">No entanto, a API fluente pode especificar a maioria das regras de formatação, validação e mapeamento que pode ser feita com atributos.</span><span class="sxs-lookup"><span data-stu-id="67dce-366">However, the fluent API can specify most of the formatting, validation, and mapping rules that can be done with attributes.</span></span>

<span data-ttu-id="67dce-367">Alguns atributos como `MinimumLength` não podem ser aplicados com a API fluente.</span><span class="sxs-lookup"><span data-stu-id="67dce-367">Some attributes such as `MinimumLength` can't be applied with the fluent API.</span></span> <span data-ttu-id="67dce-368">`MinimumLength` não altera o esquema; apenas aplica uma regra de validação de tamanho mínimo.</span><span class="sxs-lookup"><span data-stu-id="67dce-368">`MinimumLength` doesn't change the schema, it only applies a minimum length validation rule.</span></span>

<span data-ttu-id="67dce-369">Alguns desenvolvedores preferem usar a API fluente exclusivamente para que possam manter suas classes de entidade "limpas".</span><span class="sxs-lookup"><span data-stu-id="67dce-369">Some developers prefer to use the fluent API exclusively so that they can keep their entity classes "clean."</span></span> <span data-ttu-id="67dce-370">Atributos e a API fluente podem ser combinados.</span><span class="sxs-lookup"><span data-stu-id="67dce-370">Attributes and the fluent API can be mixed.</span></span> <span data-ttu-id="67dce-371">Há algumas configurações que apenas podem ser feitas com a API fluente (especificando uma PK composta).</span><span class="sxs-lookup"><span data-stu-id="67dce-371">There are some configurations that can only be done with the fluent API (specifying a composite PK).</span></span> <span data-ttu-id="67dce-372">Há algumas configurações que apenas podem ser feitas com atributos (`MinimumLength`).</span><span class="sxs-lookup"><span data-stu-id="67dce-372">There are some configurations that can only be done with attributes (`MinimumLength`).</span></span> <span data-ttu-id="67dce-373">A prática recomendada para uso de atributos ou da API fluente:</span><span class="sxs-lookup"><span data-stu-id="67dce-373">The recommended practice for using fluent API or attributes:</span></span>

* <span data-ttu-id="67dce-374">Escolha uma dessas duas abordagens.</span><span class="sxs-lookup"><span data-stu-id="67dce-374">Choose one of these two approaches.</span></span>
* <span data-ttu-id="67dce-375">Use a abordagem escolhida da forma mais consistente possível.</span><span class="sxs-lookup"><span data-stu-id="67dce-375">Use the chosen approach consistently as much as possible.</span></span>

<span data-ttu-id="67dce-376">Alguns dos atributos usados neste tutorial são usados para:</span><span class="sxs-lookup"><span data-stu-id="67dce-376">Some of the attributes used in the this tutorial are used for:</span></span>

* <span data-ttu-id="67dce-377">Somente validação (por exemplo, `MinimumLength`).</span><span class="sxs-lookup"><span data-stu-id="67dce-377">Validation only (for example, `MinimumLength`).</span></span>
* <span data-ttu-id="67dce-378">Apenas configuração do EF Core (por exemplo, `HasKey`).</span><span class="sxs-lookup"><span data-stu-id="67dce-378">EF Core configuration only (for example, `HasKey`).</span></span>
* <span data-ttu-id="67dce-379">Validação e configuração do EF Core (por exemplo, `[StringLength(50)]`).</span><span class="sxs-lookup"><span data-stu-id="67dce-379">Validation and EF Core configuration (for example, `[StringLength(50)]`).</span></span>

<span data-ttu-id="67dce-380">Para obter mais informações sobre atributos vs. API fluente, consulte [Métodos de configuração](/ef/core/modeling/#methods-of-configuration).</span><span class="sxs-lookup"><span data-stu-id="67dce-380">For more information about attributes vs. fluent API, see [Methods of configuration](/ef/core/modeling/#methods-of-configuration).</span></span>

## <a name="entity-diagram-showing-relationships"></a><span data-ttu-id="67dce-381">Diagrama de entidade mostrando relações</span><span class="sxs-lookup"><span data-stu-id="67dce-381">Entity Diagram Showing Relationships</span></span>

<span data-ttu-id="67dce-382">A ilustração a seguir mostra o diagrama criado pelo EF Power Tools para o modelo Escola concluído.</span><span class="sxs-lookup"><span data-stu-id="67dce-382">The following illustration shows the diagram that EF Power Tools create for the completed School model.</span></span>

![Diagrama de entidade](complex-data-model/_static/diagram.png)

<span data-ttu-id="67dce-384">O diagrama anterior mostra:</span><span class="sxs-lookup"><span data-stu-id="67dce-384">The preceding diagram shows:</span></span>

* <span data-ttu-id="67dce-385">Várias linhas de relação um-para-muitos (1 para \*).</span><span class="sxs-lookup"><span data-stu-id="67dce-385">Several one-to-many relationship lines (1 to \*).</span></span>
* <span data-ttu-id="67dce-386">A linha de relação um para zero ou um (1 para 0..1) entre as entidades `Instructor` e `OfficeAssignment`.</span><span class="sxs-lookup"><span data-stu-id="67dce-386">The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.</span></span>
* <span data-ttu-id="67dce-387">A linha de relação zero-ou-um-para-muitos (0..1 para \*) entre as entidades `Instructor` e `Department`.</span><span class="sxs-lookup"><span data-stu-id="67dce-387">The zero-or-one-to-many relationship line (0..1 to \*) between the `Instructor` and `Department` entities.</span></span>

## <a name="seed-the-db-with-test-data"></a><span data-ttu-id="67dce-388">Propagar o BD com os dados de teste</span><span class="sxs-lookup"><span data-stu-id="67dce-388">Seed the DB with Test Data</span></span>

<span data-ttu-id="67dce-389">Atualize o código em *Data/DbInitializer.cs*:</span><span class="sxs-lookup"><span data-stu-id="67dce-389">Update the code in *Data/DbInitializer.cs*:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Final)]

<span data-ttu-id="67dce-390">O código anterior fornece dados de semente para as novas entidades.</span><span class="sxs-lookup"><span data-stu-id="67dce-390">The preceding code provides seed data for the new entities.</span></span> <span data-ttu-id="67dce-391">A maioria desse código cria novos objetos de entidade e carrega dados de exemplo.</span><span class="sxs-lookup"><span data-stu-id="67dce-391">Most of this code creates new entity objects and loads sample data.</span></span> <span data-ttu-id="67dce-392">Os dados de exemplo são usados para teste.</span><span class="sxs-lookup"><span data-stu-id="67dce-392">The sample data is used for testing.</span></span> <span data-ttu-id="67dce-393">Consulte `Enrollments` e `CourseAssignments` para obter exemplos de como tabelas de junção muitos para muitos podem ser propagadas.</span><span class="sxs-lookup"><span data-stu-id="67dce-393">See `Enrollments` and `CourseAssignments` for examples of how many-to-many join tables can be seeded.</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="67dce-394">Adicionar uma migração</span><span class="sxs-lookup"><span data-stu-id="67dce-394">Add a migration</span></span>

<span data-ttu-id="67dce-395">Compile o projeto.</span><span class="sxs-lookup"><span data-stu-id="67dce-395">Build the project.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="67dce-396">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="67dce-396">Visual Studio</span></span>](#tab/visual-studio)

```PMC
Add-Migration ComplexDataModel
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="67dce-397">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="67dce-397">.NET Core CLI</span></span>](#tab/netcore-cli)

```console
dotnet ef migrations add ComplexDataModel
```

---

<span data-ttu-id="67dce-398">O comando anterior exibe um aviso sobre a possível perda de dados.</span><span class="sxs-lookup"><span data-stu-id="67dce-398">The preceding command displays a warning about possible data loss.</span></span>

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="67dce-399">Se o comando `database update` é executado, o seguinte erro é produzido:</span><span class="sxs-lookup"><span data-stu-id="67dce-399">If the `database update` command is run, the following error is produced:</span></span>

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

## <a name="apply-the-migration"></a><span data-ttu-id="67dce-400">Aplicar a migração</span><span class="sxs-lookup"><span data-stu-id="67dce-400">Apply the migration</span></span>

<span data-ttu-id="67dce-401">Agora que você tem um banco de dados existente, precisa pensar sobre como aplicar as alterações futuras a ele.</span><span class="sxs-lookup"><span data-stu-id="67dce-401">Now that you have an existing database, you need to think about how to apply future changes to it.</span></span> <span data-ttu-id="67dce-402">Este tutorial mostra duas abordagens:</span><span class="sxs-lookup"><span data-stu-id="67dce-402">This tutorial shows two approaches:</span></span>

* [<span data-ttu-id="67dce-403">Remover e recriar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="67dce-403">Drop and re-create the database</span></span>](#drop)
* <span data-ttu-id="67dce-404">[Aplicar a migração ao banco de dados existente](#applyexisting).</span><span class="sxs-lookup"><span data-stu-id="67dce-404">[Apply the migration to the existing database](#applyexisting).</span></span> <span data-ttu-id="67dce-405">Embora esse método seja mais complexo e demorado, é a abordagem preferencial para ambientes de produção do mundo real.</span><span class="sxs-lookup"><span data-stu-id="67dce-405">While this method is more complex and time-consuming, it's the preferred approach for real-world, production environments.</span></span> <span data-ttu-id="67dce-406">**Observação**: essa é uma seção opcional do tutorial.</span><span class="sxs-lookup"><span data-stu-id="67dce-406">**Note**: This is an optional section of the tutorial.</span></span> <span data-ttu-id="67dce-407">Você pode remover e recriar etapas e ignorar esta seção.</span><span class="sxs-lookup"><span data-stu-id="67dce-407">You can do the drop and re-create steps and skip this section.</span></span> <span data-ttu-id="67dce-408">Se você quiser seguir as etapas nesta seção, não realize as etapas de remover e recriar.</span><span class="sxs-lookup"><span data-stu-id="67dce-408">If you do want to follow the steps in this section, don't do the drop and re-create steps.</span></span> 

<a name="drop"></a>

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="67dce-409">Remover e recriar o banco de dados</span><span class="sxs-lookup"><span data-stu-id="67dce-409">Drop and re-create the database</span></span>

<span data-ttu-id="67dce-410">O código no `DbInitializer` atualizado adiciona dados de semente às novas entidades.</span><span class="sxs-lookup"><span data-stu-id="67dce-410">The code in the updated `DbInitializer` adds seed data for the new entities.</span></span> <span data-ttu-id="67dce-411">Para forçar o EF Core a criar um novo BD, remova e atualize o BD:</span><span class="sxs-lookup"><span data-stu-id="67dce-411">To force EF Core to create a new  DB, drop and update the DB:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="67dce-412">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="67dce-412">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="67dce-413">No **PMC** (Console do Gerenciador de Pacotes), execute o seguinte comando:</span><span class="sxs-lookup"><span data-stu-id="67dce-413">In the **Package Manager Console** (PMC), run the following command:</span></span>

```PMC
Drop-Database
Update-Database
```

<span data-ttu-id="67dce-414">Execute `Get-Help about_EntityFrameworkCore` no PMC para obter informações de ajuda.</span><span class="sxs-lookup"><span data-stu-id="67dce-414">Run `Get-Help about_EntityFrameworkCore` from the PMC to get help information.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="67dce-415">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="67dce-415">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="67dce-416">Abra uma janela Comando e navegue para a pasta do projeto.</span><span class="sxs-lookup"><span data-stu-id="67dce-416">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="67dce-417">A pasta do projeto contém o arquivo *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="67dce-417">The project folder contains the *Startup.cs* file.</span></span>

<span data-ttu-id="67dce-418">Insira o seguinte na janela Comando:</span><span class="sxs-lookup"><span data-stu-id="67dce-418">Enter the following in the command window:</span></span>

 ```console
 dotnet ef database drop
dotnet ef database update
 ```

---

<span data-ttu-id="67dce-419">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="67dce-419">Run the app.</span></span> <span data-ttu-id="67dce-420">A execução do aplicativo executa o método `DbInitializer.Initialize`.</span><span class="sxs-lookup"><span data-stu-id="67dce-420">Running the app runs the `DbInitializer.Initialize` method.</span></span> <span data-ttu-id="67dce-421">O `DbInitializer.Initialize` popula o novo BD.</span><span class="sxs-lookup"><span data-stu-id="67dce-421">The `DbInitializer.Initialize` populates the new DB.</span></span>

<span data-ttu-id="67dce-422">Abra o BD no SSOX:</span><span class="sxs-lookup"><span data-stu-id="67dce-422">Open the DB in SSOX:</span></span>

* <span data-ttu-id="67dce-423">Se o SSOX for aberto anteriormente, clique no botão **Atualizar**.</span><span class="sxs-lookup"><span data-stu-id="67dce-423">If SSOX was opened previously, click the **Refresh** button.</span></span>
* <span data-ttu-id="67dce-424">Expanda o nó **Tabelas**.</span><span class="sxs-lookup"><span data-stu-id="67dce-424">Expand the **Tables** node.</span></span> <span data-ttu-id="67dce-425">As tabelas criadas são exibidas.</span><span class="sxs-lookup"><span data-stu-id="67dce-425">The created tables are displayed.</span></span>

![Tabelas no SSOX](complex-data-model/_static/ssox-tables.png)

<span data-ttu-id="67dce-427">Examine a tabela **CourseAssignment**:</span><span class="sxs-lookup"><span data-stu-id="67dce-427">Examine the **CourseAssignment** table:</span></span>

* <span data-ttu-id="67dce-428">Clique com o botão direito do mouse na tabela **CourseAssignment** e selecione **Exibir Dados**.</span><span class="sxs-lookup"><span data-stu-id="67dce-428">Right-click the **CourseAssignment** table and select **View Data**.</span></span>
* <span data-ttu-id="67dce-429">Verifique se a tabela **CourseAssignment** contém dados.</span><span class="sxs-lookup"><span data-stu-id="67dce-429">Verify the **CourseAssignment** table contains data.</span></span>

![Dados de CourseAssignment no SSOX](complex-data-model/_static/ssox-ci-data.png)

<a name="applyexisting"></a>

### <a name="apply-the-migration-to-the-existing-database"></a><span data-ttu-id="67dce-431">Aplicar a migração ao banco de dados existente</span><span class="sxs-lookup"><span data-stu-id="67dce-431">Apply the migration to the existing database</span></span>

<span data-ttu-id="67dce-432">Esta seção é opcional.</span><span class="sxs-lookup"><span data-stu-id="67dce-432">This section is optional.</span></span> <span data-ttu-id="67dce-433">Estas etapas só funcionarão se você tiver ignorado a seção [Remover e recriar o banco de dados](#drop) anterior.</span><span class="sxs-lookup"><span data-stu-id="67dce-433">These steps work only if you skipped the preceding [Drop and re-create the database](#drop) section.</span></span>

<span data-ttu-id="67dce-434">Quando as migrações são executadas com os dados existentes, pode haver restrições de FK que não são atendidas com os dados existentes.</span><span class="sxs-lookup"><span data-stu-id="67dce-434">When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data.</span></span> <span data-ttu-id="67dce-435">Com os dados de produção, é necessário executar etapas para migrar os dados existentes.</span><span class="sxs-lookup"><span data-stu-id="67dce-435">With production data, steps must be taken to migrate the existing data.</span></span> <span data-ttu-id="67dce-436">Esta seção fornece um exemplo de correção de violações de restrição de FK.</span><span class="sxs-lookup"><span data-stu-id="67dce-436">This section provides an example of fixing FK constraint violations.</span></span> <span data-ttu-id="67dce-437">Não faça essas alterações de código sem um backup.</span><span class="sxs-lookup"><span data-stu-id="67dce-437">Don't make these code changes without a backup.</span></span> <span data-ttu-id="67dce-438">Não faça essas alterações de código se você concluir a seção anterior e atualizou o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="67dce-438">Don't make these code changes if you completed the previous section and updated the database.</span></span>

<span data-ttu-id="67dce-439">O arquivo *{timestamp}_ComplexDataModel.cs* contém o seguinte código:</span><span class="sxs-lookup"><span data-stu-id="67dce-439">The *{timestamp}_ComplexDataModel.cs* file contains the following code:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_DepartmentID)]

<span data-ttu-id="67dce-440">O código anterior adiciona uma FK `DepartmentID` que não permite valor nulo à tabela `Course`.</span><span class="sxs-lookup"><span data-stu-id="67dce-440">The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table.</span></span> <span data-ttu-id="67dce-441">O BD do tutorial anterior contém linhas em `Course` e, portanto, essa tabela não pode ser atualizada por migrações.</span><span class="sxs-lookup"><span data-stu-id="67dce-441">The DB from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.</span></span>

<span data-ttu-id="67dce-442">Para fazer a migração `ComplexDataModel` funcionar com os dados existentes:</span><span class="sxs-lookup"><span data-stu-id="67dce-442">To make the `ComplexDataModel` migration work with existing data:</span></span>

* <span data-ttu-id="67dce-443">Altere o código para dar à nova coluna (`DepartmentID`) um valor padrão.</span><span class="sxs-lookup"><span data-stu-id="67dce-443">Change the code to give the new column (`DepartmentID`) a default value.</span></span>
* <span data-ttu-id="67dce-444">Crie um departamento fictício chamado "Temp" para atuar como o departamento padrão.</span><span class="sxs-lookup"><span data-stu-id="67dce-444">Create a fake department named "Temp" to act as the default department.</span></span>

#### <a name="fix-the-foreign-key-constraints"></a><span data-ttu-id="67dce-445">Corrigir as restrições de chave estrangeira</span><span class="sxs-lookup"><span data-stu-id="67dce-445">Fix the foreign key constraints</span></span>

<span data-ttu-id="67dce-446">Atualize o método `Up` das classes `ComplexDataModel`:</span><span class="sxs-lookup"><span data-stu-id="67dce-446">Update the `ComplexDataModel` classes `Up` method:</span></span>

* <span data-ttu-id="67dce-447">Abra o arquivo *{timestamp}_ComplexDataModel.cs*.</span><span class="sxs-lookup"><span data-stu-id="67dce-447">Open the *{timestamp}_ComplexDataModel.cs* file.</span></span>
* <span data-ttu-id="67dce-448">Comente a linha de código que adiciona a coluna `DepartmentID` à tabela `Course`.</span><span class="sxs-lookup"><span data-stu-id="67dce-448">Comment out the line of code that adds the `DepartmentID` column to the `Course` table.</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

<span data-ttu-id="67dce-449">Adicione o código realçado a seguir.</span><span class="sxs-lookup"><span data-stu-id="67dce-449">Add the following highlighted code.</span></span> <span data-ttu-id="67dce-450">O novo código é inserido após o bloco `.CreateTable( name: "Department"`:</span><span class="sxs-lookup"><span data-stu-id="67dce-450">The new code goes after the `.CreateTable( name: "Department"` block:</span></span>

 [!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

<span data-ttu-id="67dce-451">Com as alterações anteriores, as linhas `Course` existentes estarão relacionadas ao departamento "Temp" após a execução do método `ComplexDataModel` `Up`.</span><span class="sxs-lookup"><span data-stu-id="67dce-451">With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel` `Up` method runs.</span></span>

<span data-ttu-id="67dce-452">Um aplicativo de produção:</span><span class="sxs-lookup"><span data-stu-id="67dce-452">A production app would:</span></span>

* <span data-ttu-id="67dce-453">Inclui código ou scripts para adicionar linhas `Department` e linhas `Course` relacionadas às novas linhas `Department`.</span><span class="sxs-lookup"><span data-stu-id="67dce-453">Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.</span></span>
* <span data-ttu-id="67dce-454">Não usa o departamento "Temp" nem o valor padrão para `Course.DepartmentID`.</span><span class="sxs-lookup"><span data-stu-id="67dce-454">Not use the "Temp" department or the default value for `Course.DepartmentID`.</span></span>

<span data-ttu-id="67dce-455">O próximo tutorial abrange os dados relacionados.</span><span class="sxs-lookup"><span data-stu-id="67dce-455">The next tutorial covers related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="67dce-456">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="67dce-456">Additional resources</span></span>

* [<span data-ttu-id="67dce-457">Versão do YouTube deste tutorial (Parte 1)</span><span class="sxs-lookup"><span data-stu-id="67dce-457">YouTube version of this tutorial(Part 1)</span></span>](https://www.youtube.com/watch?v=0n2f0ObgCoA)
* [<span data-ttu-id="67dce-458">Versão do YouTube deste tutorial (Parte 2)</span><span class="sxs-lookup"><span data-stu-id="67dce-458">YouTube version of this tutorial(Part 2)</span></span>](https://www.youtube.com/watch?v=Je0Z5K1TNmY)

::: moniker-end

> [!div class="step-by-step"]
> <span data-ttu-id="67dce-459">[Anterior](xref:data/ef-rp/migrations)
> [Próximo](xref:data/ef-rp/read-related-data)</span><span class="sxs-lookup"><span data-stu-id="67dce-459">[Previous](xref:data/ef-rp/migrations)
[Next](xref:data/ef-rp/read-related-data)</span></span>
