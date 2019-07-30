---
title: Adicionar validação a uma Página Razor do ASP.NET Core
author: rick-anderson
description: Saiba como adicionar validação a uma Página Razor no ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 7/23/2019
uid: tutorials/razor-pages/validation
ms.openlocfilehash: d6d45dc7154bf415c3b098299d066b6fb37cf64d
ms.sourcegitcommit: 16502797ea749e2690feaa5e652a65b89c007c89
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68483261"
---
# <a name="add-validation-to-an-aspnet-core-razor-page"></a><span data-ttu-id="8e102-103">Adicionar validação a uma Página Razor do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8e102-103">Add validation to an ASP.NET Core Razor Page</span></span>

<span data-ttu-id="8e102-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8e102-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="8e102-105">Nesta seção, a lógica de validação é adicionada para o modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="8e102-105">In this section, validation logic is added to the `Movie` model.</span></span> <span data-ttu-id="8e102-106">As regras de validação são impostas sempre que um usuário cria ou edita um filme.</span><span class="sxs-lookup"><span data-stu-id="8e102-106">The validation rules are enforced any time a user creates or edits a movie.</span></span>

## <a name="validation"></a><span data-ttu-id="8e102-107">Validação</span><span class="sxs-lookup"><span data-stu-id="8e102-107">Validation</span></span>

<span data-ttu-id="8e102-108">Um princípio-chave do desenvolvimento de software é chamado [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) (“**D**on't **R**epeat **Y**ourself”).</span><span class="sxs-lookup"><span data-stu-id="8e102-108">A key tenet of software development is called [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("**D**on't **R**epeat **Y**ourself").</span></span> <span data-ttu-id="8e102-109">As Páginas Razor incentivam o desenvolvimento quando a funcionalidade é especificada uma vez e ela é refletida em todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8e102-109">Razor Pages encourages development where functionality is specified once, and it's reflected throughout the app.</span></span> <span data-ttu-id="8e102-110">O DRY pode ajudar a:</span><span class="sxs-lookup"><span data-stu-id="8e102-110">DRY can help:</span></span>

* <span data-ttu-id="8e102-111">Reduzir a quantidade de código em um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8e102-111">Reduce the amount of code in an app.</span></span>
* <span data-ttu-id="8e102-112">Fazer com que o código seja menos propenso a erros e mais fácil de ser testado e mantido.</span><span class="sxs-lookup"><span data-stu-id="8e102-112">Make the code less error prone, and easier to test and maintain.</span></span>

<span data-ttu-id="8e102-113">O suporte de validação fornecido pelas Páginas Razor e pelo Entity Framework é um bom exemplo do princípio DRY.</span><span class="sxs-lookup"><span data-stu-id="8e102-113">The validation support provided by Razor Pages and Entity Framework is a good example of the DRY principle.</span></span> <span data-ttu-id="8e102-114">As regras de validação são especificadas de forma declarativa em um único lugar (na classe de modelo) e as regras são impostas em qualquer lugar no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8e102-114">Validation rules are declaratively specified in one place (in the model class), and the rules are enforced everywhere in the app.</span></span>

[!INCLUDE[](~/includes/RP-MVC/validation.md)]

### <a name="validation-error-ui-in-razor-pages"></a><span data-ttu-id="8e102-115">Interface do usuário do erro de validação nas Páginas Razor</span><span class="sxs-lookup"><span data-stu-id="8e102-115">Validation Error UI in Razor Pages</span></span>

<span data-ttu-id="8e102-116">Execute o aplicativo e navegue para Pages/Movies.</span><span class="sxs-lookup"><span data-stu-id="8e102-116">Run the app and navigate to Pages/Movies.</span></span>

<span data-ttu-id="8e102-117">Selecione o link **Criar Novo**.</span><span class="sxs-lookup"><span data-stu-id="8e102-117">Select the **Create New** link.</span></span> <span data-ttu-id="8e102-118">Preencha o formulário com alguns valores inválidos.</span><span class="sxs-lookup"><span data-stu-id="8e102-118">Complete the form with some invalid values.</span></span> <span data-ttu-id="8e102-119">Quando a validação do lado do cliente do jQuery detecta o erro, ela exibe uma mensagem de erro.</span><span class="sxs-lookup"><span data-stu-id="8e102-119">When jQuery client-side validation detects the error, it displays an error message.</span></span>

![Formulário da exibição de filmes com vários erros de validação do lado do cliente do jQuery](validation/_static/val.png)

[!INCLUDE[](~/includes/currency.md)]

<span data-ttu-id="8e102-121">Observe como o formulário renderizou automaticamente uma mensagem de erro de validação em cada campo que contém um valor inválido.</span><span class="sxs-lookup"><span data-stu-id="8e102-121">Notice how the form has automatically rendered a validation error message in each field containing an invalid value.</span></span> <span data-ttu-id="8e102-122">Os erros são impostos no lado do cliente (usando o JavaScript e o jQuery) e no lado do servidor (quando um usuário tem o JavaScript desabilitado).</span><span class="sxs-lookup"><span data-stu-id="8e102-122">The errors are enforced both client-side (using JavaScript and jQuery) and server-side (when a user has JavaScript disabled).</span></span>

<span data-ttu-id="8e102-123">Uma vantagem significativa é que **nenhuma** alteração de código foi necessária nas páginas Criar ou Editar.</span><span class="sxs-lookup"><span data-stu-id="8e102-123">A significant benefit is that **no** code changes were necessary in the Create  or Edit pages.</span></span> <span data-ttu-id="8e102-124">Depois que DataAnnotations foi aplicado ao modelo, a interface do usuário de validação foi habilitada.</span><span class="sxs-lookup"><span data-stu-id="8e102-124">Once DataAnnotations were applied to the model, the validation UI was enabled.</span></span> <span data-ttu-id="8e102-125">As Páginas Razor criadas neste tutorial selecionaram automaticamente as regras de validação (usando os atributos de validação nas propriedades da classe do modelo `Movie`).</span><span class="sxs-lookup"><span data-stu-id="8e102-125">The Razor Pages created in this tutorial automatically picked up the validation rules (using validation attributes on the properties of the `Movie` model class).</span></span> <span data-ttu-id="8e102-126">Validação do teste usando a página Editar: a mesma validação é aplicada.</span><span class="sxs-lookup"><span data-stu-id="8e102-126">Test validation using the Edit page, the same validation is applied.</span></span>

<span data-ttu-id="8e102-127">Os dados de formulário não serão postados no servidor enquanto houver erros de validação do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="8e102-127">The form data isn't posted to the server until there are no client-side validation errors.</span></span> <span data-ttu-id="8e102-128">Verifique se os dados de formulário não são postados por uma ou mais das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="8e102-128">Verify form data isn't posted by one or more of the following approaches:</span></span>

* <span data-ttu-id="8e102-129">Coloque um ponto de interrupção no método `OnPostAsync`.</span><span class="sxs-lookup"><span data-stu-id="8e102-129">Put a break point in the `OnPostAsync` method.</span></span> <span data-ttu-id="8e102-130">Envie o formulário (selecione **Criar** ou **Salvar**).</span><span class="sxs-lookup"><span data-stu-id="8e102-130">Submit the form (select **Create** or **Save**).</span></span> <span data-ttu-id="8e102-131">O ponto de interrupção nunca é atingido.</span><span class="sxs-lookup"><span data-stu-id="8e102-131">The break point is never hit.</span></span>
* <span data-ttu-id="8e102-132">Use a [ferramenta Fiddler](https://www.telerik.com/fiddler).</span><span class="sxs-lookup"><span data-stu-id="8e102-132">Use the [Fiddler tool](https://www.telerik.com/fiddler).</span></span>
* <span data-ttu-id="8e102-133">Use as ferramentas do desenvolvedor do navegador para monitorar o tráfego de rede.</span><span class="sxs-lookup"><span data-stu-id="8e102-133">Use the browser developer tools to monitor network traffic.</span></span>

### <a name="server-side-validation"></a><span data-ttu-id="8e102-134">Validação do servidor</span><span class="sxs-lookup"><span data-stu-id="8e102-134">Server-side validation</span></span>

<span data-ttu-id="8e102-135">Quando o JavaScript está desabilitado no navegador, o envio do formulário com erros será postado no servidor.</span><span class="sxs-lookup"><span data-stu-id="8e102-135">When JavaScript is disabled in the browser, submitting the form with errors will post to the server.</span></span>

<span data-ttu-id="8e102-136">(Opcional) Teste a validação do servidor:</span><span class="sxs-lookup"><span data-stu-id="8e102-136">Optional, test server-side validation:</span></span>

* <span data-ttu-id="8e102-137">Desabilite o JavaScript no navegador.</span><span class="sxs-lookup"><span data-stu-id="8e102-137">Disable JavaScript in the browser.</span></span> <span data-ttu-id="8e102-138">É possível desabilitar o JavaScript usando as ferramentas de desenvolvedor do navegador.</span><span class="sxs-lookup"><span data-stu-id="8e102-138">You can disable JavaScript using browser's developer tools.</span></span> <span data-ttu-id="8e102-139">Se você não conseguir desabilitar o JavaScript no navegador, tente outro navegador.</span><span class="sxs-lookup"><span data-stu-id="8e102-139">If you can't disable JavaScript in the browser, try another browser.</span></span>
* <span data-ttu-id="8e102-140">Defina um ponto de interrupção no método `OnPostAsync` da página Criar ou Editar.</span><span class="sxs-lookup"><span data-stu-id="8e102-140">Set a break point in the `OnPostAsync` method of the Create or Edit page.</span></span>
* <span data-ttu-id="8e102-141">Envie um formulário com dados inválidos.</span><span class="sxs-lookup"><span data-stu-id="8e102-141">Submit a form with invalid data.</span></span>
* <span data-ttu-id="8e102-142">Verifique se o estado do modelo é inválido:</span><span class="sxs-lookup"><span data-stu-id="8e102-142">Verify the model state is invalid:</span></span>

  ```csharp
   if (!ModelState.IsValid)
   {
      return Page();
   }
  ```

<span data-ttu-id="8e102-143">O código a seguir mostra uma parte da página *Create.cshtml* gerada anteriormente com scaffold no tutorial.</span><span class="sxs-lookup"><span data-stu-id="8e102-143">The following code shows a portion of the *Create.cshtml* page scaffolded earlier in the tutorial.</span></span> <span data-ttu-id="8e102-144">Ele é usado pelas páginas Criar e Editar para exibir o formulário inicial e exibir o formulário novamente, em caso de erro.</span><span class="sxs-lookup"><span data-stu-id="8e102-144">It's used by the Create and Edit pages to display the initial form and to redisplay the form in the event of an error.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=14-20)]

<span data-ttu-id="8e102-145">O [Auxiliar de Marcação de Entrada](xref:mvc/views/working-with-forms) usa os atributos de [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) e produz os atributos HTML necessários para a Validação do jQuery no lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="8e102-145">The [Input Tag Helper](xref:mvc/views/working-with-forms) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span> <span data-ttu-id="8e102-146">O [Auxiliar de Marcação de Validação](xref:mvc/views/working-with-forms#the-validation-tag-helpers) exibe erros de validação.</span><span class="sxs-lookup"><span data-stu-id="8e102-146">The [Validation Tag Helper](xref:mvc/views/working-with-forms#the-validation-tag-helpers) displays validation errors.</span></span> <span data-ttu-id="8e102-147">Consulte [Validação](xref:mvc/models/validation) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="8e102-147">See [Validation](xref:mvc/models/validation) for more information.</span></span>

<span data-ttu-id="8e102-148">As páginas Criar e Editar não têm nenhuma regra de validação.</span><span class="sxs-lookup"><span data-stu-id="8e102-148">The Create and Edit pages have no validation rules in them.</span></span> <span data-ttu-id="8e102-149">As regras de validação e as cadeias de caracteres de erro são especificadas somente na classe `Movie`.</span><span class="sxs-lookup"><span data-stu-id="8e102-149">The validation rules and the error strings are specified only in the `Movie` class.</span></span> <span data-ttu-id="8e102-150">Essas regras de validação são aplicadas automaticamente às Páginas Razor que editam o modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="8e102-150">These validation rules are automatically applied to Razor Pages that edit the `Movie` model.</span></span>

<span data-ttu-id="8e102-151">Quando a lógica de validação precisa ser alterada, ela é feita apenas no modelo.</span><span class="sxs-lookup"><span data-stu-id="8e102-151">When validation logic needs to change, it's done only in the model.</span></span> <span data-ttu-id="8e102-152">A validação é aplicada de forma consistente em todo o aplicativo (a lógica de validação é definida em um único lugar).</span><span class="sxs-lookup"><span data-stu-id="8e102-152">Validation is applied consistently throughout the application (validation logic is defined in one place).</span></span> <span data-ttu-id="8e102-153">A validação em um único lugar ajuda a manter o código limpo e facilita sua manutenção e atualização.</span><span class="sxs-lookup"><span data-stu-id="8e102-153">Validation in one place helps keep the code clean, and makes it easier to maintain and update.</span></span>

## <a name="using-datatype-attributes"></a><span data-ttu-id="8e102-154">Usando atributos DataType</span><span class="sxs-lookup"><span data-stu-id="8e102-154">Using DataType Attributes</span></span>

<span data-ttu-id="8e102-155">Examine a classe `Movie`.</span><span class="sxs-lookup"><span data-stu-id="8e102-155">Examine the `Movie` class.</span></span> <span data-ttu-id="8e102-156">O namespace `System.ComponentModel.DataAnnotations` fornece atributos de formatação, além do conjunto interno de atributos de validação.</span><span class="sxs-lookup"><span data-stu-id="8e102-156">The `System.ComponentModel.DataAnnotations` namespace provides formatting attributes in addition to the built-in set of validation attributes.</span></span> <span data-ttu-id="8e102-157">O atributo `DataType` é aplicado às propriedades `ReleaseDate` e `Price`.</span><span class="sxs-lookup"><span data-stu-id="8e102-157">The `DataType` attribute is applied to the `ReleaseDate` and `Price` properties.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

<span data-ttu-id="8e102-158">Os atributos `DataType` fornecem apenas dicas para que o mecanismo de exibição formate os dados (e fornece atributos como `<a>` para as URLs e `<a href="mailto:EmailAddress.com">` para o email).</span><span class="sxs-lookup"><span data-stu-id="8e102-158">The `DataType` attributes only provide hints for the view engine to format the data (and supplies attributes such as `<a>` for URL's and `<a href="mailto:EmailAddress.com">` for email).</span></span> <span data-ttu-id="8e102-159">Use o atributo `RegularExpression` para validar o formato dos dados.</span><span class="sxs-lookup"><span data-stu-id="8e102-159">Use the `RegularExpression` attribute to validate the format of the data.</span></span> <span data-ttu-id="8e102-160">O atributo `DataType` é usado para especificar um tipo de dados mais específico do que o tipo intrínseco de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="8e102-160">The `DataType` attribute is used to specify a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="8e102-161">Os atributos `DataType` não são atributos de validação.</span><span class="sxs-lookup"><span data-stu-id="8e102-161">`DataType` attributes are not validation attributes.</span></span> <span data-ttu-id="8e102-162">No aplicativo de exemplo, apenas a data é exibida, sem a hora.</span><span class="sxs-lookup"><span data-stu-id="8e102-162">In the sample application, only the date is displayed, without time.</span></span>

<span data-ttu-id="8e102-163">A Enumeração `DataType` fornece muitos tipos de dados, como Date, Time, PhoneNumber, Currency, EmailAddress e muito mais.</span><span class="sxs-lookup"><span data-stu-id="8e102-163">The `DataType` Enumeration provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, and more.</span></span> <span data-ttu-id="8e102-164">O atributo `DataType` também pode permitir que o aplicativo forneça automaticamente recursos específicos a um tipo.</span><span class="sxs-lookup"><span data-stu-id="8e102-164">The `DataType` attribute can also enable the application to automatically provide type-specific features.</span></span> <span data-ttu-id="8e102-165">Por exemplo, um link `mailto:` pode ser criado para `DataType.EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="8e102-165">For example, a `mailto:` link can be created for `DataType.EmailAddress`.</span></span> <span data-ttu-id="8e102-166">Um seletor de data pode ser fornecido para `DataType.Date` em navegadores que dão suporte a HTML5.</span><span class="sxs-lookup"><span data-stu-id="8e102-166">A date selector can be provided for `DataType.Date` in browsers that support HTML5.</span></span> <span data-ttu-id="8e102-167">Os atributos `DataType` emitem atributos `data-` HTML 5 (pronunciados “data dash”) que são consumidos pelos navegadores HTML 5.</span><span class="sxs-lookup"><span data-stu-id="8e102-167">The `DataType` attributes emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers consume.</span></span> <span data-ttu-id="8e102-168">Os atributos `DataType` **não** fornecem nenhuma validação.</span><span class="sxs-lookup"><span data-stu-id="8e102-168">The `DataType` attributes do **not** provide any validation.</span></span>

<span data-ttu-id="8e102-169">`DataType.Date` não especifica o formato da data exibida.</span><span class="sxs-lookup"><span data-stu-id="8e102-169">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="8e102-170">Por padrão, o campo de dados é exibido de acordo com os formatos padrão com base nas `CultureInfo` do servidor.</span><span class="sxs-lookup"><span data-stu-id="8e102-170">By default, the data field is displayed according to the default formats based on the server's `CultureInfo`.</span></span>

<span data-ttu-id="8e102-171">A anotação de dados `[Column(TypeName = "decimal(18, 2)")]` é necessária para que o Entity Framework Core possa mapear corretamente o `Price` para a moeda no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="8e102-171">The `[Column(TypeName = "decimal(18, 2)")]` data annotation is required so Entity Framework Core can correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="8e102-172">Para obter mais informações, veja [Tipos de Dados](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="8e102-172">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="8e102-173">O atributo `DisplayFormat` é usado para especificar explicitamente o formato de data:</span><span class="sxs-lookup"><span data-stu-id="8e102-173">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

<span data-ttu-id="8e102-174">A configuração `ApplyFormatInEditMode` especifica que a formatação deve ser aplicada quando o valor é exibido para edição.</span><span class="sxs-lookup"><span data-stu-id="8e102-174">The `ApplyFormatInEditMode` setting specifies that the formatting should be applied when the value is displayed for editing.</span></span> <span data-ttu-id="8e102-175">Não é recomendável ter esse comportamento em alguns campos.</span><span class="sxs-lookup"><span data-stu-id="8e102-175">You might not want that behavior for some fields.</span></span> <span data-ttu-id="8e102-176">Por exemplo, em valores de moeda, você provavelmente não deseja que o símbolo de moeda seja exibido na interface do usuário de edição.</span><span class="sxs-lookup"><span data-stu-id="8e102-176">For example, in currency values, you probably don't want the currency symbol in the edit UI.</span></span>

<span data-ttu-id="8e102-177">O atributo `DisplayFormat` pode ser usado por si só, mas geralmente é uma boa ideia usar o atributo `DataType`.</span><span class="sxs-lookup"><span data-stu-id="8e102-177">The `DisplayFormat` attribute can be used by itself, but it's generally a good idea to use the `DataType` attribute.</span></span> <span data-ttu-id="8e102-178">O atributo `DataType` transmite a semântica dos dados, ao invés de apresentar como renderizá-lo em uma tela e oferece os seguintes benefícios que você não obtém com DisplayFormat:</span><span class="sxs-lookup"><span data-stu-id="8e102-178">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen, and provides the following benefits that you don't get with DisplayFormat:</span></span>

* <span data-ttu-id="8e102-179">O navegador pode habilitar os recursos do HTML5 (por exemplo, mostrar um controle de calendário, o símbolo de moeda apropriado à localidade, links de email, etc.)</span><span class="sxs-lookup"><span data-stu-id="8e102-179">The browser can enable HTML5 features (for example to show a calendar control, the locale-appropriate currency symbol, email links, etc.)</span></span>
* <span data-ttu-id="8e102-180">Por padrão, o navegador renderizará os dados usando o formato correto de acordo com a localidade.</span><span class="sxs-lookup"><span data-stu-id="8e102-180">By default, the browser will render data using the correct format based on your locale.</span></span>
* <span data-ttu-id="8e102-181">O atributo `DataType` pode permitir que a estrutura ASP.NET Core escolha o modelo de campo correto para renderizar os dados.</span><span class="sxs-lookup"><span data-stu-id="8e102-181">The `DataType` attribute can enable the ASP.NET Core framework to choose the right field template to render the data.</span></span> <span data-ttu-id="8e102-182">O `DisplayFormat`, se usado por si só, usa o modelo de cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="8e102-182">The `DisplayFormat` if used by itself uses the string template.</span></span>

<span data-ttu-id="8e102-183">Observação: a validação do jQuery não funciona com os atributos `Range` e `DateTime`.</span><span class="sxs-lookup"><span data-stu-id="8e102-183">Note: jQuery validation doesn't work with the `Range` attribute and `DateTime`.</span></span> <span data-ttu-id="8e102-184">Por exemplo, o seguinte código sempre exibirá um erro de validação do lado do cliente, mesmo quando a data estiver no intervalo especificado:</span><span class="sxs-lookup"><span data-stu-id="8e102-184">For example, the following code will always display a client-side validation error, even when the date is in the specified range:</span></span>

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

<span data-ttu-id="8e102-185">Geralmente, não é uma boa prática compilar datas rígidas nos modelos e, portanto, o uso do atributo `Range` e de `DateTime` não é recomendado.</span><span class="sxs-lookup"><span data-stu-id="8e102-185">It's generally not a good practice to compile hard dates in your models, so using the `Range` attribute and `DateTime` is discouraged.</span></span>

<span data-ttu-id="8e102-186">O seguinte código mostra como combinar atributos em uma linha:</span><span class="sxs-lookup"><span data-stu-id="8e102-186">The following code shows combining attributes on one line:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDAmult.cs?name=snippet1)]

<span data-ttu-id="8e102-187">[Comece com o Razor Pages e o EF Core](xref:data/ef-rp/intro) mostra operações mais avançadas do EF Core com o Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="8e102-187">[Get started with Razor Pages and EF Core](xref:data/ef-rp/intro) shows advanced EF Core operations with Razor Pages.</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="8e102-188">Aplicar migrações</span><span class="sxs-lookup"><span data-stu-id="8e102-188">Apply migrations</span></span>

<span data-ttu-id="8e102-189">As DataAnnotations aplicadas à classe alteram o esquema.</span><span class="sxs-lookup"><span data-stu-id="8e102-189">The DataAnnotations applied to the class change the schema.</span></span> <span data-ttu-id="8e102-190">Por exemplo, as DataAnnotations aplicadas ao campo `Title`:</span><span class="sxs-lookup"><span data-stu-id="8e102-190">For example, the DataAnnotations applied to the `Title` field:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet11)]

* <span data-ttu-id="8e102-191">Limitam os caracteres a 60.</span><span class="sxs-lookup"><span data-stu-id="8e102-191">Limits the characters to 60.</span></span>
* <span data-ttu-id="8e102-192">Não permitem um valor `null`.</span><span class="sxs-lookup"><span data-stu-id="8e102-192">Doesn't allow a `null` value.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="8e102-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8e102-193">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8e102-194">Atualmente a tabela `Movie` tem o seguinte esquema:</span><span class="sxs-lookup"><span data-stu-id="8e102-194">The `Movie` table currently has the following schema:</span></span>

``` sql
CREATE TABLE [dbo].[Movie] (
    [ID]          INT             IDENTITY (1, 1) NOT NULL,
    [Title]       NVARCHAR (MAX)  NULL,
    [ReleaseDate] DATETIME2 (7)   NOT NULL,
    [Genre]       NVARCHAR (MAX)  NULL,
    [Price]       DECIMAL (18, 2) NOT NULL,
    [Rating]      NVARCHAR (MAX)  NULL,
    CONSTRAINT [PK_Movie] PRIMARY KEY CLUSTERED ([ID] ASC)
);
```

<span data-ttu-id="8e102-195">As alterações do esquema anterior não fazem com que o EF lance uma exceção.</span><span class="sxs-lookup"><span data-stu-id="8e102-195">The preceding schema changes don't cause EF to throw an exception.</span></span> <span data-ttu-id="8e102-196">No entanto, crie uma migração de forma que o esquema seja consistente com o modelo.</span><span class="sxs-lookup"><span data-stu-id="8e102-196">However, create a migration so the schema is consistent with the model.</span></span>

<span data-ttu-id="8e102-197">No menu **Ferramentas**, selecione **Gerenciador de Pacotes NuGet > Console do Gerenciador de Pacotes**.</span><span class="sxs-lookup"><span data-stu-id="8e102-197">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="8e102-198">No PMC, insira os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="8e102-198">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration New_DataAnnotations
Update-Database
```

<span data-ttu-id="8e102-199">`Update-Database` executa os métodos `Up` da classe `New_DataAnnotations`.</span><span class="sxs-lookup"><span data-stu-id="8e102-199">`Update-Database` runs the `Up` methods of the `New_DataAnnotations` class.</span></span> <span data-ttu-id="8e102-200">Examine o método `Up`:</span><span class="sxs-lookup"><span data-stu-id="8e102-200">Examine the `Up` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Migrations/20190724163003_New_DataAnnotations.cs?name=snippet)]

<span data-ttu-id="8e102-201">A tabela `Movie` atualizada tem o seguinte esquema:</span><span class="sxs-lookup"><span data-stu-id="8e102-201">The updated `Movie` table has the following schema:</span></span>

``` sql
CREATE TABLE [dbo].[Movie] (
    [ID]          INT             IDENTITY (1, 1) NOT NULL,
    [Title]       NVARCHAR (60)   NOT NULL,
    [ReleaseDate] DATETIME2 (7)   NOT NULL,
    [Genre]       NVARCHAR (30)   NOT NULL,
    [Price]       DECIMAL (18, 2) NOT NULL,
    [Rating]      NVARCHAR (5)    NOT NULL,
    CONSTRAINT [PK_Movie] PRIMARY KEY CLUSTERED ([ID] ASC)
);
```

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="8e102-202">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="8e102-202">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="8e102-203">As migrações não são necessárias para o SQLite.</span><span class="sxs-lookup"><span data-stu-id="8e102-203">Migrations are not required for SQLite.</span></span>

---

### <a name="publish-to-azure"></a><span data-ttu-id="8e102-204">Publicar no Azure</span><span class="sxs-lookup"><span data-stu-id="8e102-204">Publish to Azure</span></span>

<span data-ttu-id="8e102-205">Para obter informações sobre como implantar no Azure, consulte [Tutorial: Compilar um aplicativo ASP.NET Core no Azure com o Banco de Dados SQL](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb).</span><span class="sxs-lookup"><span data-stu-id="8e102-205">For information on deploying to Azure, see [Tutorial: Build an ASP.NET Core app in Azure with SQL Database](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb).</span></span>

<span data-ttu-id="8e102-206">Obrigado por concluir esta introdução às Páginas Razor.</span><span class="sxs-lookup"><span data-stu-id="8e102-206">Thanks for completing this introduction to Razor Pages.</span></span> <span data-ttu-id="8e102-207">A [Introdução ao Razor Pages e ao EF Core](xref:data/ef-rp/intro) é um excelente acompanhamento para este tutorial.</span><span class="sxs-lookup"><span data-stu-id="8e102-207">[Get started with Razor Pages and EF Core](xref:data/ef-rp/intro) is an excellent follow up to this tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8e102-208">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="8e102-208">Additional resources</span></span>

* <xref:mvc/views/working-with-forms>
* <xref:fundamentals/localization>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/authoring>
* [<span data-ttu-id="8e102-209">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="8e102-209">YouTube version of this tutorial</span></span>](https://youtu.be/b63m66eu7us)

> [!div class="step-by-step"]
> [<span data-ttu-id="8e102-210">Anterior: adicionando um novo campo</span><span class="sxs-lookup"><span data-stu-id="8e102-210">Previous: Adding a new field</span></span>](xref:tutorials/razor-pages/new-field)
