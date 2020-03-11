---
title: Adicionar validação a uma Página Razor do ASP.NET Core
author: rick-anderson
description: Saiba como adicionar validação a uma Página Razor no ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 7/23/2019
uid: tutorials/razor-pages/validation
ms.openlocfilehash: f283234ed8a32dc9b7904bc6fee1cc9c04741029
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78666015"
---
# <a name="add-validation-to-an-aspnet-core-razor-page"></a><span data-ttu-id="14ab2-103">Adicionar validação a uma Página Razor do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="14ab2-103">Add validation to an ASP.NET Core Razor Page</span></span>

<span data-ttu-id="14ab2-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="14ab2-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="14ab2-105">Nesta seção, a lógica de validação é adicionada para o modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="14ab2-105">In this section, validation logic is added to the `Movie` model.</span></span> <span data-ttu-id="14ab2-106">As regras de validação são impostas sempre que um usuário cria ou edita um filme.</span><span class="sxs-lookup"><span data-stu-id="14ab2-106">The validation rules are enforced any time a user creates or edits a movie.</span></span>

## <a name="validation"></a><span data-ttu-id="14ab2-107">Validação</span><span class="sxs-lookup"><span data-stu-id="14ab2-107">Validation</span></span>

<span data-ttu-id="14ab2-108">Um princípio-chave do desenvolvimento de software é chamado [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) (“**D**on't **R**epeat **Y**ourself”).</span><span class="sxs-lookup"><span data-stu-id="14ab2-108">A key tenet of software development is called [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("**D**on't **R**epeat **Y**ourself").</span></span> <span data-ttu-id="14ab2-109">As Páginas Razor incentivam o desenvolvimento quando a funcionalidade é especificada uma vez e ela é refletida em todo o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="14ab2-109">Razor Pages encourages development where functionality is specified once, and it's reflected throughout the app.</span></span> <span data-ttu-id="14ab2-110">O DRY pode ajudar a:</span><span class="sxs-lookup"><span data-stu-id="14ab2-110">DRY can help:</span></span>

* <span data-ttu-id="14ab2-111">Reduzir a quantidade de código em um aplicativo.</span><span class="sxs-lookup"><span data-stu-id="14ab2-111">Reduce the amount of code in an app.</span></span>
* <span data-ttu-id="14ab2-112">Fazer com que o código seja menos propenso a erros e mais fácil de ser testado e mantido.</span><span class="sxs-lookup"><span data-stu-id="14ab2-112">Make the code less error prone, and easier to test and maintain.</span></span>

<span data-ttu-id="14ab2-113">O suporte de validação fornecido pelas Páginas Razor e pelo Entity Framework é um bom exemplo do princípio DRY.</span><span class="sxs-lookup"><span data-stu-id="14ab2-113">The validation support provided by Razor Pages and Entity Framework is a good example of the DRY principle.</span></span> <span data-ttu-id="14ab2-114">As regras de validação são especificadas de forma declarativa em um único lugar (na classe de modelo) e as regras são impostas em qualquer lugar no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="14ab2-114">Validation rules are declaratively specified in one place (in the model class), and the rules are enforced everywhere in the app.</span></span>

## <a name="add-validation-rules-to-the-movie-model"></a><span data-ttu-id="14ab2-115">Adicionar regras de validação ao modelo de filme</span><span class="sxs-lookup"><span data-stu-id="14ab2-115">Add validation rules to the movie model</span></span>

<span data-ttu-id="14ab2-116">O namespace DataAnnotations fornece um conjunto de atributos de validação internos aplicados de forma declarativa a uma classe ou propriedade.</span><span class="sxs-lookup"><span data-stu-id="14ab2-116">The DataAnnotations namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="14ab2-117">DataAnnotations também contém atributos de formatação como `DataType`, que ajudam com a formatação e não fornecem validação.</span><span class="sxs-lookup"><span data-stu-id="14ab2-117">DataAnnotations also contains formatting attributes like `DataType` that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="14ab2-118">Atualize a classe `Movie` para aproveitar os atributos de validação `Required`, `StringLength`, `RegularExpression` e `Range` internos.</span><span class="sxs-lookup"><span data-stu-id="14ab2-118">Update the `Movie` class to take advantage of the built-in `Required`, `StringLength`, `RegularExpression`, and `Range` validation attributes.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="14ab2-119">Os atributos de validação especificam o comportamento que você deseja impor nas propriedades de modelo às quais eles são aplicados:</span><span class="sxs-lookup"><span data-stu-id="14ab2-119">The validation attributes specify behavior that you want to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="14ab2-120">Os atributos `Required` e `MinimumLength` indicam que uma propriedade deve ter um valor; porém, nada impede que um usuário insira um espaço em branco para atender a essa validação.</span><span class="sxs-lookup"><span data-stu-id="14ab2-120">The `Required` and `MinimumLength` attributes indicate that a property must have a value; but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="14ab2-121">O atributo `RegularExpression` é usado para limitar quais caracteres podem ser inseridos.</span><span class="sxs-lookup"><span data-stu-id="14ab2-121">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="14ab2-122">No código anterior, "Gênero":</span><span class="sxs-lookup"><span data-stu-id="14ab2-122">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="14ab2-123">Deve usar apenas letras.</span><span class="sxs-lookup"><span data-stu-id="14ab2-123">Must only use letters.</span></span>
  * <span data-ttu-id="14ab2-124">A primeira letra deve ser maiúscula.</span><span class="sxs-lookup"><span data-stu-id="14ab2-124">The first letter is required to be uppercase.</span></span> <span data-ttu-id="14ab2-125">Espaços em branco, números e caracteres especiais não são permitidos.</span><span class="sxs-lookup"><span data-stu-id="14ab2-125">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="14ab2-126">A "Classificação" `RegularExpression`:</span><span class="sxs-lookup"><span data-stu-id="14ab2-126">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="14ab2-127">Exige que o primeiro caractere seja uma letra maiúscula.</span><span class="sxs-lookup"><span data-stu-id="14ab2-127">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="14ab2-128">Permite caracteres especiais e números nos espaços subsequentes.</span><span class="sxs-lookup"><span data-stu-id="14ab2-128">Allows special characters and numbers in  subsequent spaces.</span></span> <span data-ttu-id="14ab2-129">"PG-13" é válido para uma classificação, mas é um erro em "Gênero".</span><span class="sxs-lookup"><span data-stu-id="14ab2-129">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="14ab2-130">O atributo `Range` restringe um valor a um intervalo especificado.</span><span class="sxs-lookup"><span data-stu-id="14ab2-130">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="14ab2-131">O atributo `StringLength` permite definir o tamanho máximo de uma propriedade de cadeia de caracteres e, opcionalmente, seu tamanho mínimo.</span><span class="sxs-lookup"><span data-stu-id="14ab2-131">The `StringLength` attribute lets you set the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="14ab2-132">Os tipos de valor (como `decimal`, `int`, `float`, `DateTime`) são inerentemente necessários e não precisam do atributo `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="14ab2-132">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="14ab2-133">Ter as regras de validação automaticamente impostas pelo ASP.NET Core ajuda a tornar seu aplicativo mais robusto.</span><span class="sxs-lookup"><span data-stu-id="14ab2-133">Having validation rules automatically enforced by ASP.NET Core helps make your app more robust.</span></span> <span data-ttu-id="14ab2-134">Também garante que você não se esqueça de validar algo e inadvertidamente permita dados incorretos no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="14ab2-134">It also ensures that you can't forget to validate something and inadvertently let bad data into the database.</span></span>

### <a name="validation-error-ui-in-razor-pages"></a><span data-ttu-id="14ab2-135">Interface do usuário do erro de validação nas Páginas Razor</span><span class="sxs-lookup"><span data-stu-id="14ab2-135">Validation Error UI in Razor Pages</span></span>

<span data-ttu-id="14ab2-136">Execute o aplicativo e navegue para Pages/Movies.</span><span class="sxs-lookup"><span data-stu-id="14ab2-136">Run the app and navigate to Pages/Movies.</span></span>

<span data-ttu-id="14ab2-137">Selecione o link **Criar Novo**.</span><span class="sxs-lookup"><span data-stu-id="14ab2-137">Select the **Create New** link.</span></span> <span data-ttu-id="14ab2-138">Preencha o formulário com alguns valores inválidos.</span><span class="sxs-lookup"><span data-stu-id="14ab2-138">Complete the form with some invalid values.</span></span> <span data-ttu-id="14ab2-139">Quando a validação do lado do cliente do jQuery detecta o erro, ela exibe uma mensagem de erro.</span><span class="sxs-lookup"><span data-stu-id="14ab2-139">When jQuery client-side validation detects the error, it displays an error message.</span></span>

![Formulário da exibição de filmes com vários erros de validação do lado do cliente do jQuery](validation/_static/val.png)

[!INCLUDE[](~/includes/localization/currency.md)]

<span data-ttu-id="14ab2-141">Observe como o formulário renderizou automaticamente uma mensagem de erro de validação em cada campo que contém um valor inválido.</span><span class="sxs-lookup"><span data-stu-id="14ab2-141">Notice how the form has automatically rendered a validation error message in each field containing an invalid value.</span></span> <span data-ttu-id="14ab2-142">Os erros são impostos no lado do cliente (usando o JavaScript e o jQuery) e no lado do servidor (quando um usuário tem o JavaScript desabilitado).</span><span class="sxs-lookup"><span data-stu-id="14ab2-142">The errors are enforced both client-side (using JavaScript and jQuery) and server-side (when a user has JavaScript disabled).</span></span>

<span data-ttu-id="14ab2-143">Uma vantagem significativa é que **nenhuma** alteração de código foi necessária nas páginas Criar ou Editar.</span><span class="sxs-lookup"><span data-stu-id="14ab2-143">A significant benefit is that **no** code changes were necessary in the Create  or Edit pages.</span></span> <span data-ttu-id="14ab2-144">Depois que DataAnnotations foi aplicado ao modelo, a interface do usuário de validação foi habilitada.</span><span class="sxs-lookup"><span data-stu-id="14ab2-144">Once DataAnnotations were applied to the model, the validation UI was enabled.</span></span> <span data-ttu-id="14ab2-145">As Páginas Razor criadas neste tutorial selecionaram automaticamente as regras de validação (usando os atributos de validação nas propriedades da classe do modelo `Movie`).</span><span class="sxs-lookup"><span data-stu-id="14ab2-145">The Razor Pages created in this tutorial automatically picked up the validation rules (using validation attributes on the properties of the `Movie` model class).</span></span> <span data-ttu-id="14ab2-146">Validação do teste usando a página Editar: a mesma validação é aplicada.</span><span class="sxs-lookup"><span data-stu-id="14ab2-146">Test validation using the Edit page, the same validation is applied.</span></span>

<span data-ttu-id="14ab2-147">Os dados de formulário não serão postados no servidor enquanto houver erros de validação do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="14ab2-147">The form data isn't posted to the server until there are no client-side validation errors.</span></span> <span data-ttu-id="14ab2-148">Verifique se os dados de formulário não são postados por uma ou mais das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="14ab2-148">Verify form data isn't posted by one or more of the following approaches:</span></span>

* <span data-ttu-id="14ab2-149">Coloque um ponto de interrupção no método `OnPostAsync`.</span><span class="sxs-lookup"><span data-stu-id="14ab2-149">Put a break point in the `OnPostAsync` method.</span></span> <span data-ttu-id="14ab2-150">Envie o formulário (selecione **Criar** ou **Salvar**).</span><span class="sxs-lookup"><span data-stu-id="14ab2-150">Submit the form (select **Create** or **Save**).</span></span> <span data-ttu-id="14ab2-151">O ponto de interrupção nunca é atingido.</span><span class="sxs-lookup"><span data-stu-id="14ab2-151">The break point is never hit.</span></span>
* <span data-ttu-id="14ab2-152">Use a [ferramenta Fiddler](https://www.telerik.com/fiddler).</span><span class="sxs-lookup"><span data-stu-id="14ab2-152">Use the [Fiddler tool](https://www.telerik.com/fiddler).</span></span>
* <span data-ttu-id="14ab2-153">Use as ferramentas do desenvolvedor do navegador para monitorar o tráfego de rede.</span><span class="sxs-lookup"><span data-stu-id="14ab2-153">Use the browser developer tools to monitor network traffic.</span></span>

### <a name="server-side-validation"></a><span data-ttu-id="14ab2-154">Validação do servidor</span><span class="sxs-lookup"><span data-stu-id="14ab2-154">Server-side validation</span></span>

<span data-ttu-id="14ab2-155">Quando o JavaScript está desabilitado no navegador, o envio do formulário com erros será postado no servidor.</span><span class="sxs-lookup"><span data-stu-id="14ab2-155">When JavaScript is disabled in the browser, submitting the form with errors will post to the server.</span></span>

<span data-ttu-id="14ab2-156">(Opcional) Teste a validação do servidor:</span><span class="sxs-lookup"><span data-stu-id="14ab2-156">Optional, test server-side validation:</span></span>

* <span data-ttu-id="14ab2-157">Desabilite o JavaScript no navegador.</span><span class="sxs-lookup"><span data-stu-id="14ab2-157">Disable JavaScript in the browser.</span></span> <span data-ttu-id="14ab2-158">É possível desabilitar o JavaScript usando as ferramentas de desenvolvedor do navegador.</span><span class="sxs-lookup"><span data-stu-id="14ab2-158">You can disable JavaScript using browser's developer tools.</span></span> <span data-ttu-id="14ab2-159">Se você não conseguir desabilitar o JavaScript no navegador, tente outro navegador.</span><span class="sxs-lookup"><span data-stu-id="14ab2-159">If you can't disable JavaScript in the browser, try another browser.</span></span>
* <span data-ttu-id="14ab2-160">Defina um ponto de interrupção no método `OnPostAsync` da página Criar ou Editar.</span><span class="sxs-lookup"><span data-stu-id="14ab2-160">Set a break point in the `OnPostAsync` method of the Create or Edit page.</span></span>
* <span data-ttu-id="14ab2-161">Envie um formulário com dados inválidos.</span><span class="sxs-lookup"><span data-stu-id="14ab2-161">Submit a form with invalid data.</span></span>
* <span data-ttu-id="14ab2-162">Verifique se o estado do modelo é inválido:</span><span class="sxs-lookup"><span data-stu-id="14ab2-162">Verify the model state is invalid:</span></span>

  ```csharp
   if (!ModelState.IsValid)
   {
      return Page();
   }
  ```
  
<span data-ttu-id="14ab2-163">Como alternativa, você pode [desabilitar a validação do lado do cliente no servidor](xref:mvc/models/validation#disable-client-side-validation).</span><span class="sxs-lookup"><span data-stu-id="14ab2-163">Alternatively, you can [Disable client-side validation on the server](xref:mvc/models/validation#disable-client-side-validation).</span></span>

<span data-ttu-id="14ab2-164">O código a seguir mostra uma parte da página *Create.cshtml* gerada anteriormente com scaffold no tutorial.</span><span class="sxs-lookup"><span data-stu-id="14ab2-164">The following code shows a portion of the *Create.cshtml* page scaffolded earlier in the tutorial.</span></span> <span data-ttu-id="14ab2-165">Ele é usado pelas páginas Criar e Editar para exibir o formulário inicial e exibir o formulário novamente, em caso de erro.</span><span class="sxs-lookup"><span data-stu-id="14ab2-165">It's used by the Create and Edit pages to display the initial form and to redisplay the form in the event of an error.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=14-20)]

<span data-ttu-id="14ab2-166">O [Auxiliar de Marcação de Entrada](xref:mvc/views/working-with-forms) usa os atributos de [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) e produz os atributos HTML necessários para a Validação do jQuery no lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="14ab2-166">The [Input Tag Helper](xref:mvc/views/working-with-forms) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span> <span data-ttu-id="14ab2-167">O [Auxiliar de Marcação de Validação](xref:mvc/views/working-with-forms#the-validation-tag-helpers) exibe erros de validação.</span><span class="sxs-lookup"><span data-stu-id="14ab2-167">The [Validation Tag Helper](xref:mvc/views/working-with-forms#the-validation-tag-helpers) displays validation errors.</span></span> <span data-ttu-id="14ab2-168">Consulte [Validação](xref:mvc/models/validation) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="14ab2-168">See [Validation](xref:mvc/models/validation) for more information.</span></span>

<span data-ttu-id="14ab2-169">As páginas Criar e Editar não têm nenhuma regra de validação.</span><span class="sxs-lookup"><span data-stu-id="14ab2-169">The Create and Edit pages have no validation rules in them.</span></span> <span data-ttu-id="14ab2-170">As regras de validação e as cadeias de caracteres de erro são especificadas somente na classe `Movie`.</span><span class="sxs-lookup"><span data-stu-id="14ab2-170">The validation rules and the error strings are specified only in the `Movie` class.</span></span> <span data-ttu-id="14ab2-171">Essas regras de validação são aplicadas automaticamente às Páginas Razor que editam o modelo `Movie`.</span><span class="sxs-lookup"><span data-stu-id="14ab2-171">These validation rules are automatically applied to Razor Pages that edit the `Movie` model.</span></span>

<span data-ttu-id="14ab2-172">Quando a lógica de validação precisa ser alterada, ela é feita apenas no modelo.</span><span class="sxs-lookup"><span data-stu-id="14ab2-172">When validation logic needs to change, it's done only in the model.</span></span> <span data-ttu-id="14ab2-173">A validação é aplicada de forma consistente em todo o aplicativo (a lógica de validação é definida em um único lugar).</span><span class="sxs-lookup"><span data-stu-id="14ab2-173">Validation is applied consistently throughout the application (validation logic is defined in one place).</span></span> <span data-ttu-id="14ab2-174">A validação em um único lugar ajuda a manter o código limpo e facilita sua manutenção e atualização.</span><span class="sxs-lookup"><span data-stu-id="14ab2-174">Validation in one place helps keep the code clean, and makes it easier to maintain and update.</span></span>

## <a name="using-datatype-attributes"></a><span data-ttu-id="14ab2-175">Usando atributos DataType</span><span class="sxs-lookup"><span data-stu-id="14ab2-175">Using DataType Attributes</span></span>

<span data-ttu-id="14ab2-176">Examine a classe `Movie`.</span><span class="sxs-lookup"><span data-stu-id="14ab2-176">Examine the `Movie` class.</span></span> <span data-ttu-id="14ab2-177">O namespace `System.ComponentModel.DataAnnotations` fornece atributos de formatação, além do conjunto interno de atributos de validação.</span><span class="sxs-lookup"><span data-stu-id="14ab2-177">The `System.ComponentModel.DataAnnotations` namespace provides formatting attributes in addition to the built-in set of validation attributes.</span></span> <span data-ttu-id="14ab2-178">O atributo `DataType` é aplicado às propriedades `ReleaseDate` e `Price`.</span><span class="sxs-lookup"><span data-stu-id="14ab2-178">The `DataType` attribute is applied to the `ReleaseDate` and `Price` properties.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

<span data-ttu-id="14ab2-179">Os atributos `DataType` fornecem apenas dicas para que o mecanismo de exibição formate os dados (e fornece atributos como `<a>` para as URLs e `<a href="mailto:EmailAddress.com">` para o email).</span><span class="sxs-lookup"><span data-stu-id="14ab2-179">The `DataType` attributes only provide hints for the view engine to format the data (and supplies attributes such as `<a>` for URL's and `<a href="mailto:EmailAddress.com">` for email).</span></span> <span data-ttu-id="14ab2-180">Use o atributo `RegularExpression` para validar o formato dos dados.</span><span class="sxs-lookup"><span data-stu-id="14ab2-180">Use the `RegularExpression` attribute to validate the format of the data.</span></span> <span data-ttu-id="14ab2-181">O atributo `DataType` é usado para especificar um tipo de dados mais específico do que o tipo intrínseco de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="14ab2-181">The `DataType` attribute is used to specify a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="14ab2-182">Os atributos `DataType` não são atributos de validação.</span><span class="sxs-lookup"><span data-stu-id="14ab2-182">`DataType` attributes are not validation attributes.</span></span> <span data-ttu-id="14ab2-183">No aplicativo de exemplo, apenas a data é exibida, sem a hora.</span><span class="sxs-lookup"><span data-stu-id="14ab2-183">In the sample application, only the date is displayed, without time.</span></span>

<span data-ttu-id="14ab2-184">A Enumeração `DataType` fornece muitos tipos de dados, como Date, Time, PhoneNumber, Currency, EmailAddress e muito mais.</span><span class="sxs-lookup"><span data-stu-id="14ab2-184">The `DataType` Enumeration provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, and more.</span></span> <span data-ttu-id="14ab2-185">O atributo `DataType` também pode permitir que o aplicativo forneça automaticamente recursos específicos a um tipo.</span><span class="sxs-lookup"><span data-stu-id="14ab2-185">The `DataType` attribute can also enable the application to automatically provide type-specific features.</span></span> <span data-ttu-id="14ab2-186">Por exemplo, um link `mailto:` pode ser criado para `DataType.EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="14ab2-186">For example, a `mailto:` link can be created for `DataType.EmailAddress`.</span></span> <span data-ttu-id="14ab2-187">Um seletor de data pode ser fornecido para `DataType.Date` em navegadores que dão suporte a HTML5.</span><span class="sxs-lookup"><span data-stu-id="14ab2-187">A date selector can be provided for `DataType.Date` in browsers that support HTML5.</span></span> <span data-ttu-id="14ab2-188">Os atributos `DataType` emitem atributos `data-` HTML 5 (pronunciados “data dash”) que são consumidos pelos navegadores HTML 5.</span><span class="sxs-lookup"><span data-stu-id="14ab2-188">The `DataType` attributes emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers consume.</span></span> <span data-ttu-id="14ab2-189">Os atributos `DataType`**não** fornecem nenhuma validação.</span><span class="sxs-lookup"><span data-stu-id="14ab2-189">The `DataType` attributes do **not** provide any validation.</span></span>

<span data-ttu-id="14ab2-190">`DataType.Date` não especifica o formato da data exibida.</span><span class="sxs-lookup"><span data-stu-id="14ab2-190">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="14ab2-191">Por padrão, o campo de dados é exibido de acordo com os formatos padrão com base nas `CultureInfo` do servidor.</span><span class="sxs-lookup"><span data-stu-id="14ab2-191">By default, the data field is displayed according to the default formats based on the server's `CultureInfo`.</span></span>

<span data-ttu-id="14ab2-192">A anotação de dados `[Column(TypeName = "decimal(18, 2)")]` é necessária para que o Entity Framework Core possa mapear corretamente o `Price` para a moeda no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="14ab2-192">The `[Column(TypeName = "decimal(18, 2)")]` data annotation is required so Entity Framework Core can correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="14ab2-193">Para obter mais informações, veja [Tipos de Dados](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="14ab2-193">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="14ab2-194">O atributo `DisplayFormat` é usado para especificar explicitamente o formato de data:</span><span class="sxs-lookup"><span data-stu-id="14ab2-194">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

<span data-ttu-id="14ab2-195">A configuração `ApplyFormatInEditMode` especifica que a formatação deve ser aplicada quando o valor é exibido para edição.</span><span class="sxs-lookup"><span data-stu-id="14ab2-195">The `ApplyFormatInEditMode` setting specifies that the formatting should be applied when the value is displayed for editing.</span></span> <span data-ttu-id="14ab2-196">Não é recomendável ter esse comportamento em alguns campos.</span><span class="sxs-lookup"><span data-stu-id="14ab2-196">You might not want that behavior for some fields.</span></span> <span data-ttu-id="14ab2-197">Por exemplo, em valores de moeda, você provavelmente não deseja que o símbolo de moeda seja exibido na interface do usuário de edição.</span><span class="sxs-lookup"><span data-stu-id="14ab2-197">For example, in currency values, you probably don't want the currency symbol in the edit UI.</span></span>

<span data-ttu-id="14ab2-198">O atributo `DisplayFormat` pode ser usado por si só, mas geralmente é uma boa ideia usar o atributo `DataType`.</span><span class="sxs-lookup"><span data-stu-id="14ab2-198">The `DisplayFormat` attribute can be used by itself, but it's generally a good idea to use the `DataType` attribute.</span></span> <span data-ttu-id="14ab2-199">O atributo `DataType` transmite a semântica dos dados, ao invés de apresentar como renderizá-lo em uma tela e oferece os seguintes benefícios que você não obtém com DisplayFormat:</span><span class="sxs-lookup"><span data-stu-id="14ab2-199">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen, and provides the following benefits that you don't get with DisplayFormat:</span></span>

* <span data-ttu-id="14ab2-200">O navegador pode habilitar os recursos do HTML5 (por exemplo, mostrar um controle de calendário, o símbolo de moeda apropriado à localidade, links de email, etc.)</span><span class="sxs-lookup"><span data-stu-id="14ab2-200">The browser can enable HTML5 features (for example to show a calendar control, the locale-appropriate currency symbol, email links, etc.)</span></span>
* <span data-ttu-id="14ab2-201">Por padrão, o navegador renderizará os dados usando o formato correto de acordo com a localidade.</span><span class="sxs-lookup"><span data-stu-id="14ab2-201">By default, the browser will render data using the correct format based on your locale.</span></span>
* <span data-ttu-id="14ab2-202">O atributo `DataType` pode permitir que a estrutura ASP.NET Core escolha o modelo de campo correto para renderizar os dados.</span><span class="sxs-lookup"><span data-stu-id="14ab2-202">The `DataType` attribute can enable the ASP.NET Core framework to choose the right field template to render the data.</span></span> <span data-ttu-id="14ab2-203">O `DisplayFormat`, se usado por si só, usa o modelo de cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="14ab2-203">The `DisplayFormat` if used by itself uses the string template.</span></span>

<span data-ttu-id="14ab2-204">Observação: a validação do jQuery não funciona com os atributos `Range` e `DateTime`.</span><span class="sxs-lookup"><span data-stu-id="14ab2-204">Note: jQuery validation doesn't work with the `Range` attribute and `DateTime`.</span></span> <span data-ttu-id="14ab2-205">Por exemplo, o seguinte código sempre exibirá um erro de validação do lado do cliente, mesmo quando a data estiver no intervalo especificado:</span><span class="sxs-lookup"><span data-stu-id="14ab2-205">For example, the following code will always display a client-side validation error, even when the date is in the specified range:</span></span>

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

<span data-ttu-id="14ab2-206">Geralmente, não é uma boa prática compilar datas rígidas nos modelos e, portanto, o uso do atributo `Range` e de `DateTime` não é recomendado.</span><span class="sxs-lookup"><span data-stu-id="14ab2-206">It's generally not a good practice to compile hard dates in your models, so using the `Range` attribute and `DateTime` is discouraged.</span></span>

<span data-ttu-id="14ab2-207">O seguinte código mostra como combinar atributos em uma linha:</span><span class="sxs-lookup"><span data-stu-id="14ab2-207">The following code shows combining attributes on one line:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDAmult.cs?name=snippet1)]

<span data-ttu-id="14ab2-208">[Comece com o Razor Pages e o EF Core](xref:data/ef-rp/intro) mostra operações mais avançadas do EF Core com o Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="14ab2-208">[Get started with Razor Pages and EF Core](xref:data/ef-rp/intro) shows advanced EF Core operations with Razor Pages.</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="14ab2-209">Aplicar migrações</span><span class="sxs-lookup"><span data-stu-id="14ab2-209">Apply migrations</span></span>

<span data-ttu-id="14ab2-210">As DataAnnotations aplicadas à classe alteram o esquema.</span><span class="sxs-lookup"><span data-stu-id="14ab2-210">The DataAnnotations applied to the class change the schema.</span></span> <span data-ttu-id="14ab2-211">Por exemplo, as DataAnnotations aplicadas ao campo `Title`:</span><span class="sxs-lookup"><span data-stu-id="14ab2-211">For example, the DataAnnotations applied to the `Title` field:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet11)]

* <span data-ttu-id="14ab2-212">Limitam os caracteres a 60.</span><span class="sxs-lookup"><span data-stu-id="14ab2-212">Limits the characters to 60.</span></span>
* <span data-ttu-id="14ab2-213">Não permitem um valor `null`.</span><span class="sxs-lookup"><span data-stu-id="14ab2-213">Doesn't allow a `null` value.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="14ab2-214">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="14ab2-214">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="14ab2-215">Atualmente a tabela `Movie` tem o seguinte esquema:</span><span class="sxs-lookup"><span data-stu-id="14ab2-215">The `Movie` table currently has the following schema:</span></span>

```sql
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

<span data-ttu-id="14ab2-216">As alterações do esquema anterior não fazem com que o EF lance uma exceção.</span><span class="sxs-lookup"><span data-stu-id="14ab2-216">The preceding schema changes don't cause EF to throw an exception.</span></span> <span data-ttu-id="14ab2-217">No entanto, crie uma migração de forma que o esquema seja consistente com o modelo.</span><span class="sxs-lookup"><span data-stu-id="14ab2-217">However, create a migration so the schema is consistent with the model.</span></span>

<span data-ttu-id="14ab2-218">No menu **Ferramentas**, selecione **Gerenciador de Pacotes NuGet > Console do Gerenciador de Pacotes**.</span><span class="sxs-lookup"><span data-stu-id="14ab2-218">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="14ab2-219">No PMC, digite os seguintes comandos:</span><span class="sxs-lookup"><span data-stu-id="14ab2-219">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration New_DataAnnotations
Update-Database
```

<span data-ttu-id="14ab2-220">`Update-Database` executa os métodos `Up` da classe `New_DataAnnotations`.</span><span class="sxs-lookup"><span data-stu-id="14ab2-220">`Update-Database` runs the `Up` methods of the `New_DataAnnotations` class.</span></span> <span data-ttu-id="14ab2-221">Examine o método `Up`:</span><span class="sxs-lookup"><span data-stu-id="14ab2-221">Examine the `Up` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Migrations/20190724163003_New_DataAnnotations.cs?name=snippet)]

<span data-ttu-id="14ab2-222">A tabela `Movie` atualizada tem o seguinte esquema:</span><span class="sxs-lookup"><span data-stu-id="14ab2-222">The updated `Movie` table has the following schema:</span></span>

```sql
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

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="14ab2-223">Visual Studio Code/Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="14ab2-223">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="14ab2-224">As migrações não são necessárias para o SQLite.</span><span class="sxs-lookup"><span data-stu-id="14ab2-224">Migrations are not required for SQLite.</span></span>

---

### <a name="publish-to-azure"></a><span data-ttu-id="14ab2-225">Publicar no Azure</span><span class="sxs-lookup"><span data-stu-id="14ab2-225">Publish to Azure</span></span>

<span data-ttu-id="14ab2-226">Para obter informações sobre como implantar no Azure, consulte [tutorial: criar um aplicativo ASP.NET Core no Azure com o banco de dados SQL](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb).</span><span class="sxs-lookup"><span data-stu-id="14ab2-226">For information on deploying to Azure, see [Tutorial: Build an ASP.NET Core app in Azure with SQL Database](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb).</span></span>

<span data-ttu-id="14ab2-227">Obrigado por concluir esta introdução às Páginas Razor.</span><span class="sxs-lookup"><span data-stu-id="14ab2-227">Thanks for completing this introduction to Razor Pages.</span></span> <span data-ttu-id="14ab2-228">A [Introdução ao Razor Pages e ao EF Core](xref:data/ef-rp/intro) é um excelente acompanhamento para este tutorial.</span><span class="sxs-lookup"><span data-stu-id="14ab2-228">[Get started with Razor Pages and EF Core](xref:data/ef-rp/intro) is an excellent follow up to this tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="14ab2-229">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="14ab2-229">Additional resources</span></span>

* <xref:mvc/views/working-with-forms>
* <xref:fundamentals/localization>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/authoring>
* [<span data-ttu-id="14ab2-230">Versão do YouTube deste tutorial</span><span class="sxs-lookup"><span data-stu-id="14ab2-230">YouTube version of this tutorial</span></span>](https://youtu.be/b63m66eu7us)

> [!div class="step-by-step"]
> [<span data-ttu-id="14ab2-231">Anterior: Adicionando um novo campo</span><span class="sxs-lookup"><span data-stu-id="14ab2-231">Previous: Adding a new field</span></span>](xref:tutorials/razor-pages/new-field)
