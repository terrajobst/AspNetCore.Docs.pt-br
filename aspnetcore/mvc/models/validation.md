---
title: Validação de modelo no ASP.NET Core MVC
author: rick-anderson
description: Saiba mais sobre a validação de modelo no ASP.NET Core MVC e Razor Pages.
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2019
monikerRange: '>= aspnetcore-2.1'
uid: mvc/models/validation
ms.openlocfilehash: eb18d3a701a4d1937ac6eb9f61916f348b95882a
ms.sourcegitcommit: 8835b6777682da6fb3becf9f9121c03f89dc7614
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69975254"
---
# <a name="model-validation-in-aspnet-core-mvc-and-razor-pages"></a><span data-ttu-id="76d3d-103">Validação de modelo no ASP.NET Core MVC e Razor Pages</span><span class="sxs-lookup"><span data-stu-id="76d3d-103">Model validation in ASP.NET Core MVC and Razor Pages</span></span>

<span data-ttu-id="76d3d-104">Este artigo explica como validar a entrada do usuário em um aplicativo em ASP.NET Core MVC ou Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="76d3d-104">This article explains how to validate user input in an ASP.NET Core MVC or Razor Pages app.</span></span>

<span data-ttu-id="76d3d-105">[Exibir ou baixar um código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="76d3d-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="model-state"></a><span data-ttu-id="76d3d-106">Estado do modelo</span><span class="sxs-lookup"><span data-stu-id="76d3d-106">Model state</span></span>

<span data-ttu-id="76d3d-107">O estado do modelo representa erros que vêm de dois subsistemas: model binding e validação de modelo.</span><span class="sxs-lookup"><span data-stu-id="76d3d-107">Model state represents errors that come from two subsystems: model binding and model validation.</span></span> <span data-ttu-id="76d3d-108">Erros que se originam de [model binding](model-binding.md) geralmente são erros de conversão de dados (por exemplo, um "x" é inserido em um campo que espera um inteiro).</span><span class="sxs-lookup"><span data-stu-id="76d3d-108">Errors that originate from [model binding](model-binding.md) are generally data conversion errors (for example, an "x" is entered in a field that expects an integer).</span></span> <span data-ttu-id="76d3d-109">A validação do modelo ocorre após o model binding e relata os erros em que os dados não estão em conformidade com as regras de negócio (por exemplo, um 0 é inserido em um campo que espera uma classificação entre 1 e 5).</span><span class="sxs-lookup"><span data-stu-id="76d3d-109">Model validation occurs after model binding and reports errors where the data doesn't conform to business rules (for example, a 0 is entered in a field that expects a rating between 1 and 5).</span></span>

<span data-ttu-id="76d3d-110">O model binding e a validação ocorrem antes da execução de uma ação do controlador ou de um método de manipulador do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="76d3d-110">Both model binding and validation occur before the execution of a controller action or a Razor Pages handler method.</span></span> <span data-ttu-id="76d3d-111">Nos aplicativos Web, é responsabilidade do aplicativo inspecionar `ModelState.IsValid` e reagir adequadamente.</span><span class="sxs-lookup"><span data-stu-id="76d3d-111">For web apps, it's the app's responsibility to inspect `ModelState.IsValid` and react appropriately.</span></span> <span data-ttu-id="76d3d-112">Geralmente, os aplicativos Web reexibem a página com uma mensagem de erro:</span><span class="sxs-lookup"><span data-stu-id="76d3d-112">Web apps typically redisplay the page with an error message:</span></span>

[!code-csharp[](validation/sample_snapshot/Create.cshtml.cs?name=snippet&highlight=3-6)]

<span data-ttu-id="76d3d-113">Os controladores de API Web não precisarão verificar `ModelState.IsValid` se eles tiverem o atributo `[ApiController]`.</span><span class="sxs-lookup"><span data-stu-id="76d3d-113">Web API controllers don't have to check `ModelState.IsValid` if they have the `[ApiController]` attribute.</span></span> <span data-ttu-id="76d3d-114">Nesse caso, uma resposta automática HTTP 400 contendo detalhes do problema será retornada quando o estado do modelo for inválido.</span><span class="sxs-lookup"><span data-stu-id="76d3d-114">In that case, an automatic HTTP 400 response containing issue details is returned when model state is invalid.</span></span> <span data-ttu-id="76d3d-115">Para obter mais informações, veja [Respostas automáticas HTTP 400](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="76d3d-115">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="rerun-validation"></a><span data-ttu-id="76d3d-116">Executar validação novamente</span><span class="sxs-lookup"><span data-stu-id="76d3d-116">Rerun validation</span></span>

<span data-ttu-id="76d3d-117">A validação é automática, mas talvez seja necessário repeti-la manualmente.</span><span class="sxs-lookup"><span data-stu-id="76d3d-117">Validation is automatic, but you might want to repeat it manually.</span></span> <span data-ttu-id="76d3d-118">Por exemplo, você pode calcular um valor para uma propriedade e executar novamente a validação após a configuração da propriedade com o valor computado.</span><span class="sxs-lookup"><span data-stu-id="76d3d-118">For example, you might compute a value for a property and want to rerun validation after setting the property to the computed value.</span></span> <span data-ttu-id="76d3d-119">Para reexecutar a validação, chame o método `TryValidateModel`, conforme mostrado aqui:</span><span class="sxs-lookup"><span data-stu-id="76d3d-119">To rerun validation, call the `TryValidateModel` method, as shown here:</span></span>

[!code-csharp[](validation/sample/Controllers/MoviesController.cs?name=snippet_TryValidateModel&highlight=11)]

## <a name="validation-attributes"></a><span data-ttu-id="76d3d-120">Atributos de validação</span><span class="sxs-lookup"><span data-stu-id="76d3d-120">Validation attributes</span></span>

<span data-ttu-id="76d3d-121">Os atributos de validação permitem que você especifique regras de validação para propriedades do modelo.</span><span class="sxs-lookup"><span data-stu-id="76d3d-121">Validation attributes let you specify validation rules for model properties.</span></span> <span data-ttu-id="76d3d-122">O exemplo a seguir do [aplicativo de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) mostra uma classe de modelo que é anotada com atributos de validação.</span><span class="sxs-lookup"><span data-stu-id="76d3d-122">The following example from [the sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) shows a model class that is annotated with validation attributes.</span></span> <span data-ttu-id="76d3d-123">O atributo `[ClassicMovie]` é um atributo de validação personalizado e os outros são atributos internos.</span><span class="sxs-lookup"><span data-stu-id="76d3d-123">The `[ClassicMovie]` attribute is a custom validation attribute and the others are built-in.</span></span> <span data-ttu-id="76d3d-124">(O `[ClassicMovie2]` não está exibido e mostra uma maneira alternativa de implementar um atributo personalizado.)</span><span class="sxs-lookup"><span data-stu-id="76d3d-124">(Not shown is `[ClassicMovie2]`, which shows an alternative way to implement a custom attribute.)</span></span>

[!code-csharp[](validation/sample/Models/Movie.cs?name=snippet_ModelClass)]

## <a name="built-in-attributes"></a><span data-ttu-id="76d3d-125">Atributos internos</span><span class="sxs-lookup"><span data-stu-id="76d3d-125">Built-in attributes</span></span>

<span data-ttu-id="76d3d-126">Aqui estão alguns dos atributos de validação internos:</span><span class="sxs-lookup"><span data-stu-id="76d3d-126">Here are some of the built-in validation attributes:</span></span>

* <span data-ttu-id="76d3d-127">`[CreditCard]`: valida se a propriedade tem um formato de cartão de crédito.</span><span class="sxs-lookup"><span data-stu-id="76d3d-127">`[CreditCard]`: Validates that the property has a credit card format.</span></span>
* <span data-ttu-id="76d3d-128">`[Compare]`: valida se duas propriedades em um modelo são correspondentes.</span><span class="sxs-lookup"><span data-stu-id="76d3d-128">`[Compare]`: Validates that two properties in a model match.</span></span>
* <span data-ttu-id="76d3d-129">`[EmailAddress]`: valida se a propriedade tem um formato de email.</span><span class="sxs-lookup"><span data-stu-id="76d3d-129">`[EmailAddress]`: Validates that the property has an email format.</span></span>
* <span data-ttu-id="76d3d-130">`[Phone]`: valida se a propriedade tem um formato de número de telefone.</span><span class="sxs-lookup"><span data-stu-id="76d3d-130">`[Phone]`: Validates that the property has a telephone number format.</span></span>
* <span data-ttu-id="76d3d-131">`[Range]`: valida se o valor da propriedade está dentro de um intervalo especificado.</span><span class="sxs-lookup"><span data-stu-id="76d3d-131">`[Range]`: Validates that the property value falls within a specified range.</span></span>
* <span data-ttu-id="76d3d-132">`[RegularExpression]`: valida se o valor da propriedade corresponde à expressão regular especificada.</span><span class="sxs-lookup"><span data-stu-id="76d3d-132">`[RegularExpression]`: Validates that the property value matches a specified regular expression.</span></span>
* <span data-ttu-id="76d3d-133">`[Required]`: valida se o campo não é nulo.</span><span class="sxs-lookup"><span data-stu-id="76d3d-133">`[Required]`: Validates that the field is not null.</span></span> <span data-ttu-id="76d3d-134">Consulte [atributo [Required]](#required-attribute) para obter detalhes sobre o comportamento desse atributo.</span><span class="sxs-lookup"><span data-stu-id="76d3d-134">See [[Required] attribute](#required-attribute) for details about this attribute's behavior.</span></span>
* <span data-ttu-id="76d3d-135">`[StringLength]`: valida se um valor da propriedade de cadeia de caracteres não excede um limite de comprimento especificado.</span><span class="sxs-lookup"><span data-stu-id="76d3d-135">`[StringLength]`: Validates that a string property value doesn't exceed a specified length limit.</span></span>
* <span data-ttu-id="76d3d-136">`[Url]`: valida se a propriedade tem um formato de URL.</span><span class="sxs-lookup"><span data-stu-id="76d3d-136">`[Url]`: Validates that the property has a URL format.</span></span>
* <span data-ttu-id="76d3d-137">`[Remote]`: valida a entrada no cliente chamando um método de ação no servidor.</span><span class="sxs-lookup"><span data-stu-id="76d3d-137">`[Remote]`: Validates input on the client by calling an action method on the server.</span></span> <span data-ttu-id="76d3d-138">Consulte [atributo [Remote]](#remote-attribute) para obter detalhes sobre o comportamento desse atributo.</span><span class="sxs-lookup"><span data-stu-id="76d3d-138">See [[Remote] attribute](#remote-attribute) for details about this attribute's behavior.</span></span>

<span data-ttu-id="76d3d-139">Uma lista completa de atributos de validação pode ser encontrada no namespace [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations).</span><span class="sxs-lookup"><span data-stu-id="76d3d-139">A complete list of validation attributes can be found in the [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) namespace.</span></span>

### <a name="error-messages"></a><span data-ttu-id="76d3d-140">Mensagens de erro</span><span class="sxs-lookup"><span data-stu-id="76d3d-140">Error messages</span></span>

<span data-ttu-id="76d3d-141">Os atributos de validação permitem que você especifique a mensagem de erro a ser exibido para uma entrada inválida.</span><span class="sxs-lookup"><span data-stu-id="76d3d-141">Validation attributes let you specify the error message to be displayed for invalid input.</span></span> <span data-ttu-id="76d3d-142">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="76d3d-142">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

<span data-ttu-id="76d3d-143">Internamente, a chamada de atributos `String.Format` com um espaço reservado para o nome do campo e, às vezes, espaços reservados adicionais.</span><span class="sxs-lookup"><span data-stu-id="76d3d-143">Internally, the attributes call `String.Format` with a placeholder for the field name and sometimes additional placeholders.</span></span> <span data-ttu-id="76d3d-144">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="76d3d-144">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

<span data-ttu-id="76d3d-145">Quando aplicada a uma propriedade `Name`, a mensagem de erro criada pelo código anterior seria "Comprimento do nome ter entre 6 e 8.".</span><span class="sxs-lookup"><span data-stu-id="76d3d-145">When applied to a `Name` property, the error message created by the preceding code would be "Name length must be between 6 and 8.".</span></span>

<span data-ttu-id="76d3d-146">Para descobrir quais parâmetros são passados para `String.Format` no caso de uma mensagem de erro de um atributo específico, consulte o [código-fonte de DataAnnotations](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span><span class="sxs-lookup"><span data-stu-id="76d3d-146">To find out which parameters are passed to `String.Format` for a particular attribute's error message, see the [DataAnnotations source code](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span></span>

## <a name="required-attribute"></a><span data-ttu-id="76d3d-147">Atributo [Required]</span><span class="sxs-lookup"><span data-stu-id="76d3d-147">[Required] attribute</span></span>

<span data-ttu-id="76d3d-148">Por padrão, o sistema de validação trata parâmetros não anuláveis ou propriedades como se tivessem um atributo `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="76d3d-148">By default, the validation system treats non-nullable parameters or properties as if they had a `[Required]` attribute.</span></span> <span data-ttu-id="76d3d-149">[Tipos de valor](/dotnet/csharp/language-reference/keywords/value-types) como `decimal` e `int` são não anuláveis.</span><span class="sxs-lookup"><span data-stu-id="76d3d-149">[Value types](/dotnet/csharp/language-reference/keywords/value-types) such as `decimal` and `int` are non-nullable.</span></span>

### <a name="required-validation-on-the-server"></a><span data-ttu-id="76d3d-150">Validação de [Required] no servidor</span><span class="sxs-lookup"><span data-stu-id="76d3d-150">[Required] validation on the server</span></span>

<span data-ttu-id="76d3d-151">No servidor, um valor obrigatório será considerado ausente se a propriedade for nula.</span><span class="sxs-lookup"><span data-stu-id="76d3d-151">On the server, a required value is considered missing if the property is null.</span></span> <span data-ttu-id="76d3d-152">Um campo não anulável é sempre válido e a mensagem de erro do atributo [Required] nunca é exibida.</span><span class="sxs-lookup"><span data-stu-id="76d3d-152">A non-nullable field is always valid, and the [Required] attribute's error message is never displayed.</span></span>

<span data-ttu-id="76d3d-153">No entanto, o model binding para uma propriedade que não permite valor nulo pode falhar, resultando em uma mensagem de erro como `The value '' is invalid`.</span><span class="sxs-lookup"><span data-stu-id="76d3d-153">However, model binding for a non-nullable property may fail, resulting in an error message such as `The value '' is invalid`.</span></span> <span data-ttu-id="76d3d-154">Para especificar uma mensagem de erro personalizada para a validação de tipos não anuláveis do lado do servidor, você tem as seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="76d3d-154">To specify a custom error message for server-side validation of non-nullable types, you have the following options:</span></span>

* <span data-ttu-id="76d3d-155">Tornar o campo anulável (por exemplo, `decimal?` em vez de `decimal`).</span><span class="sxs-lookup"><span data-stu-id="76d3d-155">Make the field nullable (for example, `decimal?` instead of `decimal`).</span></span> <span data-ttu-id="76d3d-156">Tipos de valor [Nullable\<T>](/dotnet/csharp/programming-guide/nullable-types/) são tratados como tipos que permitem valor nulo padrão.</span><span class="sxs-lookup"><span data-stu-id="76d3d-156">[Nullable\<T>](/dotnet/csharp/programming-guide/nullable-types/) value types are treated like standard nullable types.</span></span>
* <span data-ttu-id="76d3d-157">Especifique a mensagem de erro padrão a ser usada pelo model binding, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="76d3d-157">Specify the default error message to be used by model binding, as shown in the following example:</span></span>

  [!code-csharp[](validation/sample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=4-5)]

  <span data-ttu-id="76d3d-158">Para obter mais informações sobre erros de model binding para os quais você pode definir mensagens padrão, consulte <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span><span class="sxs-lookup"><span data-stu-id="76d3d-158">For more information about model binding errors that you can set default messages for, see <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span></span>

### <a name="required-validation-on-the-client"></a><span data-ttu-id="76d3d-159">Validação de [Required] no cliente</span><span class="sxs-lookup"><span data-stu-id="76d3d-159">[Required] validation on the client</span></span>

<span data-ttu-id="76d3d-160">Cadeias de caracteres e tipos que não permitem valor nulo são tratados de maneira diferente no cliente em comparação com servidor.</span><span class="sxs-lookup"><span data-stu-id="76d3d-160">Non-nullable types and strings are handled differently on the client compared to the server.</span></span> <span data-ttu-id="76d3d-161">No cliente:</span><span class="sxs-lookup"><span data-stu-id="76d3d-161">On the client:</span></span>

* <span data-ttu-id="76d3d-162">Um valor será considerado presente apenas se a entrada for inserida para ele.</span><span class="sxs-lookup"><span data-stu-id="76d3d-162">A value is considered present only if input is entered for it.</span></span> <span data-ttu-id="76d3d-163">Portanto, a validação do lado do cliente lida com tipos que não permitem valor nulo da mesma maneira que com tipos que permitem valor nulo.</span><span class="sxs-lookup"><span data-stu-id="76d3d-163">Therefore, client-side validation handles non-nullable types the same as nullable types.</span></span>
* <span data-ttu-id="76d3d-164">O espaço em branco em um campo de cadeia de caracteres é considerado uma entrada válida pelo método [required](https://jqueryvalidation.org/required-method/) do jQuery Validation.</span><span class="sxs-lookup"><span data-stu-id="76d3d-164">Whitespace in a string field is considered valid input by the jQuery Validation [required](https://jqueryvalidation.org/required-method/) method.</span></span> <span data-ttu-id="76d3d-165">A validação do lado do servidor considerará um campo de cadeia de caracteres necessário inválido somente se o espaço em branco for inserido.</span><span class="sxs-lookup"><span data-stu-id="76d3d-165">Server-side validation considers a required string field invalid if only whitespace is entered.</span></span>

<span data-ttu-id="76d3d-166">Conforme observado anteriormente, os tipos que não permitem valor nulo são tratados como se tivessem um atributo `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="76d3d-166">As noted earlier, non-nullable types are treated as though they had a `[Required]` attribute.</span></span> <span data-ttu-id="76d3d-167">Isso significa que você obtém a validação do lado do cliente mesmo que não aplique o atributo `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="76d3d-167">That means you get client-side validation even if you don't apply the `[Required]` attribute.</span></span> <span data-ttu-id="76d3d-168">Mas se não for usar o atributo, você obterá uma mensagem de erro padrão.</span><span class="sxs-lookup"><span data-stu-id="76d3d-168">But if you don't use the attribute, you get a default error message.</span></span> <span data-ttu-id="76d3d-169">Para especificar uma mensagem de erro personalizada, use o atributo.</span><span class="sxs-lookup"><span data-stu-id="76d3d-169">To specify a custom error message, use the attribute.</span></span>

## <a name="remote-attribute"></a><span data-ttu-id="76d3d-170">Atributo [Remote]</span><span class="sxs-lookup"><span data-stu-id="76d3d-170">[Remote] attribute</span></span>

<span data-ttu-id="76d3d-171">O atributo `[Remote]` implementa a validação do lado do cliente, exigindo a chamada de um método no servidor para determinar se a entrada do campo é válida.</span><span class="sxs-lookup"><span data-stu-id="76d3d-171">The `[Remote]` attribute implements client-side validation that requires calling a method on the server to determine whether field input is valid.</span></span> <span data-ttu-id="76d3d-172">Por exemplo, o aplicativo pode precisar verificar se um nome de usuário já está em uso.</span><span class="sxs-lookup"><span data-stu-id="76d3d-172">For example, the app may need to verify whether a user name is already in use.</span></span>

<span data-ttu-id="76d3d-173">Para implementar a validação remota:</span><span class="sxs-lookup"><span data-stu-id="76d3d-173">To implement remote validation:</span></span>

1. <span data-ttu-id="76d3d-174">Crie um método de ação para JavaScript para chamar.</span><span class="sxs-lookup"><span data-stu-id="76d3d-174">Create an action method for JavaScript to call.</span></span>  <span data-ttu-id="76d3d-175">O método [remoto](https://jqueryvalidation.org/remote-method/) do jQuery Validate espera uma resposta JSON:</span><span class="sxs-lookup"><span data-stu-id="76d3d-175">The jQuery Validate [remote](https://jqueryvalidation.org/remote-method/) method expects a JSON response:</span></span>

   * <span data-ttu-id="76d3d-176">`"true"` significa que os dados de entrada são válidos.</span><span class="sxs-lookup"><span data-stu-id="76d3d-176">`"true"` means the input data is valid.</span></span>
   * <span data-ttu-id="76d3d-177">`"false"`, `undefined` ou `null` significa que a entrada é inválida.</span><span class="sxs-lookup"><span data-stu-id="76d3d-177">`"false"`, `undefined`, or `null` means the input is invalid.</span></span>  <span data-ttu-id="76d3d-178">Exiba a mensagem de erro padrão.</span><span class="sxs-lookup"><span data-stu-id="76d3d-178">Display the default error message.</span></span>
   * <span data-ttu-id="76d3d-179">Qualquer outra cadeia de caracteres significa que a entrada é inválida.</span><span class="sxs-lookup"><span data-stu-id="76d3d-179">Any other string means the input is invalid.</span></span> <span data-ttu-id="76d3d-180">Exiba a cadeia de caracteres como uma mensagem de erro personalizada.</span><span class="sxs-lookup"><span data-stu-id="76d3d-180">Display the string as a custom error message.</span></span>

   <span data-ttu-id="76d3d-181">Aqui está um exemplo de um método de ação que retorna uma mensagem de erro personalizada:</span><span class="sxs-lookup"><span data-stu-id="76d3d-181">Here's an example of an action method that returns a custom error message:</span></span>

   [!code-csharp[](validation/sample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. <span data-ttu-id="76d3d-182">Na classe de modelo, anote a propriedade com um atributo `[Remote]` que aponta para o método de ação de validação, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="76d3d-182">In the model class, annotate the property with a `[Remote]` attribute that points to the validation action method, as shown in the following example:</span></span>

   [!code-csharp[](validation/sample/Models/User.cs?name=snippet_UserEmailProperty)]
 
   <span data-ttu-id="76d3d-183">O atributo `[Remote]` está no namespace `Microsoft.AspNetCore.Mvc`.</span><span class="sxs-lookup"><span data-stu-id="76d3d-183">The `[Remote]` attribute is in the `Microsoft.AspNetCore.Mvc` namespace.</span></span> <span data-ttu-id="76d3d-184">Instale o pacote NuGet [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) se você não estiver usando o metapacote `Microsoft.AspNetCore.App` ou `Microsoft.AspNetCore.All`.</span><span class="sxs-lookup"><span data-stu-id="76d3d-184">Install the [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) NuGet package if you're not using the `Microsoft.AspNetCore.App` or `Microsoft.AspNetCore.All` metapackage.</span></span>
   
### <a name="additional-fields"></a><span data-ttu-id="76d3d-185">Campos adicionais</span><span class="sxs-lookup"><span data-stu-id="76d3d-185">Additional fields</span></span>

<span data-ttu-id="76d3d-186">A propriedade `AdditionalFields` do atributo `[Remote]` permite validar combinações de campos em relação aos dados no servidor.</span><span class="sxs-lookup"><span data-stu-id="76d3d-186">The `AdditionalFields` property of the `[Remote]` attribute lets you validate combinations of fields against data on the server.</span></span> <span data-ttu-id="76d3d-187">Por exemplo, se o modelo `User` tinha as propriedades `FirstName` e `LastName`, pode ser interessante verificar se nenhum usuário existente já tem esse par de nomes.</span><span class="sxs-lookup"><span data-stu-id="76d3d-187">For example, if the `User` model had `FirstName` and `LastName` properties, you might want to verify that no existing users already have that pair of names.</span></span> <span data-ttu-id="76d3d-188">O exemplo a seguir mostra como usar `AdditionalFields`:</span><span class="sxs-lookup"><span data-stu-id="76d3d-188">The following example shows how to use `AdditionalFields`:</span></span>

[!code-csharp[](validation/sample/Models/User.cs?name=snippet_UserNameProperties)]

<span data-ttu-id="76d3d-189">`AdditionalFields` pode ser definido de forma explícita com as cadeias de caracteres `"FirstName"` e `"LastName"`, mas o uso do operador [`nameof`](/dotnet/csharp/language-reference/keywords/nameof), simplifica a refatoração posterior.</span><span class="sxs-lookup"><span data-stu-id="76d3d-189">`AdditionalFields` could be set explicitly to the strings `"FirstName"` and `"LastName"`, but using the [`nameof`](/dotnet/csharp/language-reference/keywords/nameof) operator simplifies later refactoring.</span></span> <span data-ttu-id="76d3d-190">O método de ação para essa validação deve aceitar os argumentos de primeiro nome e de sobrenome:</span><span class="sxs-lookup"><span data-stu-id="76d3d-190">The action method for this validation must accept both first name and last name arguments:</span></span>

[!code-csharp[](validation/sample/Controllers/UsersController.cs?name=snippet_VerifyName)]

<span data-ttu-id="76d3d-191">Quando o usuário insere o primeiro nome ou o sobrenome, o JavaScript faz uma chamada remota para ver se esse par de nomes foi usado.</span><span class="sxs-lookup"><span data-stu-id="76d3d-191">When the user enters a first or last name, JavaScript makes a remote call to see if that pair of names has been taken.</span></span>

<span data-ttu-id="76d3d-192">Para validar dois ou mais campos adicionais, forneça-os como uma lista delimitada por vírgulas.</span><span class="sxs-lookup"><span data-stu-id="76d3d-192">To validate two or more additional fields, provide them as a comma-delimited list.</span></span> <span data-ttu-id="76d3d-193">Por exemplo, para adicionar uma propriedade `MiddleName` ao modelo, defina o atributo `[Remote]`, conforme mostrado no seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="76d3d-193">For example, to add a `MiddleName` property to the model, set the `[Remote]` attribute as shown in the following example:</span></span>

```cs
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

<span data-ttu-id="76d3d-194">`AdditionalFields`, como todos os argumentos de atributo, deve ser uma expressão de constante.</span><span class="sxs-lookup"><span data-stu-id="76d3d-194">`AdditionalFields`, like all attribute arguments, must be a constant expression.</span></span> <span data-ttu-id="76d3d-195">Portanto, não use uma [cadeia de caracteres interpolada](/dotnet/csharp/language-reference/keywords/interpolated-strings) nem chame <xref:System.String.Join*> para inicializar `AdditionalFields`.</span><span class="sxs-lookup"><span data-stu-id="76d3d-195">Therefore, don't use an [interpolated string](/dotnet/csharp/language-reference/keywords/interpolated-strings) or call <xref:System.String.Join*> to initialize `AdditionalFields`.</span></span>

## <a name="alternatives-to-built-in-attributes"></a><span data-ttu-id="76d3d-196">Alternativas aos atributos internos</span><span class="sxs-lookup"><span data-stu-id="76d3d-196">Alternatives to built-in attributes</span></span>

<span data-ttu-id="76d3d-197">Se precisar de validação não fornecida por atributos internos, você poderá:</span><span class="sxs-lookup"><span data-stu-id="76d3d-197">If you need validation not provided by built-in attributes, you can:</span></span>

* <span data-ttu-id="76d3d-198">[Criar atributos personalizados](#custom-attributes).</span><span class="sxs-lookup"><span data-stu-id="76d3d-198">[Create custom attributes](#custom-attributes).</span></span>
* <span data-ttu-id="76d3d-199">[Implementar IValidatableObject](#ivalidatableobject).</span><span class="sxs-lookup"><span data-stu-id="76d3d-199">[Implement IValidatableObject](#ivalidatableobject).</span></span>

## <a name="custom-attributes"></a><span data-ttu-id="76d3d-200">Atributos personalizados</span><span class="sxs-lookup"><span data-stu-id="76d3d-200">Custom attributes</span></span>

<span data-ttu-id="76d3d-201">Para cenários não compatíveis com os atributos de validação internos, você pode criar atributos de validação personalizados.</span><span class="sxs-lookup"><span data-stu-id="76d3d-201">For scenarios that the built-in validation attributes don't handle, you can create custom validation attributes.</span></span> <span data-ttu-id="76d3d-202">Crie uma classe que herda de <xref:System.ComponentModel.DataAnnotations.ValidationAttribute> e substitua o método <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*>.</span><span class="sxs-lookup"><span data-stu-id="76d3d-202">Create a class that inherits from <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>, and override the <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> method.</span></span>

<span data-ttu-id="76d3d-203">O método `IsValid` aceita um objeto chamado *valor*, que é a entrada a ser validada.</span><span class="sxs-lookup"><span data-stu-id="76d3d-203">The `IsValid` method accepts an object named *value*, which is the input to be validated.</span></span> <span data-ttu-id="76d3d-204">Uma sobrecarga também aceita um objeto `ValidationContext`, que fornece informações adicionais, como a instância do modelo criada pelo model binding.</span><span class="sxs-lookup"><span data-stu-id="76d3d-204">An overload also accepts a `ValidationContext` object, which provides additional information, such as the model instance created by model binding.</span></span>

<span data-ttu-id="76d3d-205">O exemplo a seguir valida se a data de lançamento de um filme no gênero *Clássico* não é posterior a um ano especificado.</span><span class="sxs-lookup"><span data-stu-id="76d3d-205">The following example validates that the release date for a movie in the *Classic* genre isn't later than a specified year.</span></span> <span data-ttu-id="76d3d-206">O atributo `[ClassicMovie2]` verificará o gênero primeiro e continuará apenas se for *Clássico*.</span><span class="sxs-lookup"><span data-stu-id="76d3d-206">The `[ClassicMovie2]` attribute checks the genre first and continues only if it's *Classic*.</span></span> <span data-ttu-id="76d3d-207">Para filmes identificados como clássicos, ele verifica a data de lançamento para garantir que não seja posterior ao limite passado ao construtor de atributo).</span><span class="sxs-lookup"><span data-stu-id="76d3d-207">For movies identified as classics, it checks the release date to make sure it's not later than the limit passed to the attribute constructor.)</span></span>

[!code-csharp[](validation/sample/Attributes/ClassicMovieAttribute.cs?name=snippet_ClassicMovieAttribute)]

<span data-ttu-id="76d3d-208">A variável `movie` no exemplo anterior representa um objeto `Movie` que contém os dados do envio do formulário.</span><span class="sxs-lookup"><span data-stu-id="76d3d-208">The `movie` variable in the preceding example represents a `Movie` object that contains the data from the form submission.</span></span> <span data-ttu-id="76d3d-209">O método `IsValid` verifica a data e o gênero.</span><span class="sxs-lookup"><span data-stu-id="76d3d-209">The `IsValid` method checks the date and genre.</span></span> <span data-ttu-id="76d3d-210">Após a validação bem-sucedida, o `IsValid` retorna um código `ValidationResult.Success`.</span><span class="sxs-lookup"><span data-stu-id="76d3d-210">Upon successful validation, `IsValid` returns a `ValidationResult.Success` code.</span></span> <span data-ttu-id="76d3d-211">Quando a validação falha, um `ValidationResult` com uma mensagem erro será retornado.</span><span class="sxs-lookup"><span data-stu-id="76d3d-211">When validation fails, a `ValidationResult` with an error message is returned.</span></span>

## <a name="ivalidatableobject"></a><span data-ttu-id="76d3d-212">IValidatableObject</span><span class="sxs-lookup"><span data-stu-id="76d3d-212">IValidatableObject</span></span>

<span data-ttu-id="76d3d-213">O exemplo anterior funciona apenas com tipos `Movie`.</span><span class="sxs-lookup"><span data-stu-id="76d3d-213">The preceding example works only with `Movie` types.</span></span> <span data-ttu-id="76d3d-214">Outra opção para validação de nível de classe é implementar `IValidatableObject` na classe de modelo, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="76d3d-214">Another option for class-level validation is to implement `IValidatableObject` in the model class, as shown in the following example:</span></span>

[!code-csharp[](validation/sample/Models/MovieIValidatable.cs?name=snippet&highlight=1,26-34)]

## <a name="top-level-node-validation"></a><span data-ttu-id="76d3d-215">Validação de nó de nível superior</span><span class="sxs-lookup"><span data-stu-id="76d3d-215">Top-level node validation</span></span>

<span data-ttu-id="76d3d-216">Os nós de nível superior incluem:</span><span class="sxs-lookup"><span data-stu-id="76d3d-216">Top-level nodes include:</span></span>

* <span data-ttu-id="76d3d-217">Parâmetros de ação</span><span class="sxs-lookup"><span data-stu-id="76d3d-217">Action parameters</span></span>
* <span data-ttu-id="76d3d-218">Propriedades do controlador</span><span class="sxs-lookup"><span data-stu-id="76d3d-218">Controller properties</span></span>
* <span data-ttu-id="76d3d-219">Parâmetros do manipulador de página</span><span class="sxs-lookup"><span data-stu-id="76d3d-219">Page handler parameters</span></span>
* <span data-ttu-id="76d3d-220">Propriedades do modelo de página</span><span class="sxs-lookup"><span data-stu-id="76d3d-220">Page model properties</span></span>

<span data-ttu-id="76d3d-221">Os nós de nível superior associados ao modelo são validados além de validar as propriedades do modelo.</span><span class="sxs-lookup"><span data-stu-id="76d3d-221">Model-bound top-level nodes are validated in addition to validating model properties.</span></span> <span data-ttu-id="76d3d-222">No exemplo a seguir do aplicativo de exemplo, o método `VerifyPhone` usa o <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> para validar o parâmetro de ação `phone`:</span><span class="sxs-lookup"><span data-stu-id="76d3d-222">In the following example from the sample app, the `VerifyPhone` method uses the <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> to validate the `phone` action parameter:</span></span>

[!code-csharp[](validation/sample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

<span data-ttu-id="76d3d-223">Os nós de nível superior podem usar <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> com atributos de validação.</span><span class="sxs-lookup"><span data-stu-id="76d3d-223">Top-level nodes can use <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> with validation attributes.</span></span> <span data-ttu-id="76d3d-224">No exemplo a seguir do aplicativo de exemplo, o método `CheckAge` especifica que o parâmetro `age` deve ser associado da cadeia de caracteres de consulta quando o formulário é enviado:</span><span class="sxs-lookup"><span data-stu-id="76d3d-224">In the following example from the sample app, the `CheckAge` method specifies that the `age` parameter must be bound from the query string when the form is submitted:</span></span>

[!code-csharp[](validation/sample/Controllers/UsersController.cs?name=snippet_CheckAge)]

<span data-ttu-id="76d3d-225">Na página Verificar Idade (*CheckAge.cshtml*), há dois formulários.</span><span class="sxs-lookup"><span data-stu-id="76d3d-225">In the Check Age page (*CheckAge.cshtml*), there are two forms.</span></span> <span data-ttu-id="76d3d-226">O primeiro formulário envia um valor `Age` de `99` como uma cadeia de caracteres de consulta: `https://localhost:5001/Users/CheckAge?Age=99`.</span><span class="sxs-lookup"><span data-stu-id="76d3d-226">The first form submits an `Age` value of `99` as a query string: `https://localhost:5001/Users/CheckAge?Age=99`.</span></span>

<span data-ttu-id="76d3d-227">Quando um parâmetro `age` formatado corretamente da cadeia de caracteres de consulta é enviado, o formulário é validado.</span><span class="sxs-lookup"><span data-stu-id="76d3d-227">When a properly formatted `age` parameter from the query string is submitted, the form validates.</span></span>

<span data-ttu-id="76d3d-228">O segundo formulário na página Verificar Idade envia o valor `Age` no corpo da solicitação e a validação falha.</span><span class="sxs-lookup"><span data-stu-id="76d3d-228">The second form on the Check Age page submits the `Age` value in the body of the request, and validation fails.</span></span> <span data-ttu-id="76d3d-229">A associação falha porque o parâmetro `age` deve vir de uma cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="76d3d-229">Binding fails because the `age` parameter must come from a query string.</span></span>

<span data-ttu-id="76d3d-230">Ao executar com `CompatibilityVersion.Version_2_1` ou posterior, a validação do nó de nível superior é habilitada por padrão.</span><span class="sxs-lookup"><span data-stu-id="76d3d-230">When running with `CompatibilityVersion.Version_2_1` or later, top-level node validation is enabled by default.</span></span> <span data-ttu-id="76d3d-231">Caso contrário, a validação do nó de nível superior ficará desabilitada.</span><span class="sxs-lookup"><span data-stu-id="76d3d-231">Otherwise, top-level node validation is disabled.</span></span> <span data-ttu-id="76d3d-232">A opção padrão pode ser substituída pela configuração da propriedade <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> em (`Startup.ConfigureServices`), conforme mostrado aqui:</span><span class="sxs-lookup"><span data-stu-id="76d3d-232">The default option can be overridden by setting the <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> property in (`Startup.ConfigureServices`), as shown here:</span></span>

[!code-csharp[](validation/sample_snapshot/Startup.cs?name=snippet_AddMvc&highlight=4)]

## <a name="maximum-errors"></a><span data-ttu-id="76d3d-233">Máximo de erros</span><span class="sxs-lookup"><span data-stu-id="76d3d-233">Maximum errors</span></span>

<span data-ttu-id="76d3d-234">A validação para quando o número máximo de erros é atingido (200 por padrão).</span><span class="sxs-lookup"><span data-stu-id="76d3d-234">Validation stops when the maximum number of errors is reached (200 by default).</span></span> <span data-ttu-id="76d3d-235">Você pode configurar esse número com o seguinte código no `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="76d3d-235">You can configure this number with the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](validation/sample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=3)]

## <a name="maximum-recursion"></a><span data-ttu-id="76d3d-236">Recursão máxima</span><span class="sxs-lookup"><span data-stu-id="76d3d-236">Maximum recursion</span></span>

<span data-ttu-id="76d3d-237"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> percorre o grafo de objeto do modelo que está sendo validado.</span><span class="sxs-lookup"><span data-stu-id="76d3d-237"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> traverses the object graph of the model being validated.</span></span> <span data-ttu-id="76d3d-238">Para modelos que são muito profundos ou que são infinitamente recursivos, a validação pode resultar em estouro de pilha.</span><span class="sxs-lookup"><span data-stu-id="76d3d-238">For models that are very deep or are infinitely recursive, validation may result in stack overflow.</span></span> <span data-ttu-id="76d3d-239">O [MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) fornece uma maneira de interromper a validação antes que a recursão visitante exceda uma profundidade configurada.</span><span class="sxs-lookup"><span data-stu-id="76d3d-239">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) provides a way to stop validation early if the visitor recursion exceeds a configured depth.</span></span> <span data-ttu-id="76d3d-240">O valor padrão de `MvcOptions.MaxValidationDepth` é 200 durante a execução com `CompatibilityVersion.Version_2_2` ou posterior.</span><span class="sxs-lookup"><span data-stu-id="76d3d-240">The default value of `MvcOptions.MaxValidationDepth` is 200 when running with `CompatibilityVersion.Version_2_2` or later.</span></span> <span data-ttu-id="76d3d-241">Para versões anteriores, o valor é nulo, o que significa que não há nenhuma restrição de profundidade.</span><span class="sxs-lookup"><span data-stu-id="76d3d-241">For earlier versions, the value is null, which means no depth constraint.</span></span>

## <a name="automatic-short-circuit"></a><span data-ttu-id="76d3d-242">Curto-circuito automático</span><span class="sxs-lookup"><span data-stu-id="76d3d-242">Automatic short-circuit</span></span>

<span data-ttu-id="76d3d-243">A validação sofrerá curto-circuito (será ignorada) automaticamente se o grafo do modelo não exigir validação.</span><span class="sxs-lookup"><span data-stu-id="76d3d-243">Validation is automatically short-circuited (skipped) if the model graph doesn't require validation.</span></span> <span data-ttu-id="76d3d-244">Objetos em que o tempo de execução ignora a validação para inclusão de coleções de primitivos (como `byte[]`, `string[]` e `Dictionary<string, string>`) e grafos de objetos complexos que não têm um validador.</span><span class="sxs-lookup"><span data-stu-id="76d3d-244">Objects that the runtime skips validation for include collections of primitives (such as `byte[]`, `string[]`, `Dictionary<string, string>`) and complex object graphs that don't have any validators.</span></span>

## <a name="disable-validation"></a><span data-ttu-id="76d3d-245">Desabilitar validação</span><span class="sxs-lookup"><span data-stu-id="76d3d-245">Disable validation</span></span>

<span data-ttu-id="76d3d-246">Para desabilitar a validação:</span><span class="sxs-lookup"><span data-stu-id="76d3d-246">To disable validation:</span></span>

1. <span data-ttu-id="76d3d-247">Crie uma implementação de `IObjectModelValidator` que não marque nenhum campo como inválido.</span><span class="sxs-lookup"><span data-stu-id="76d3d-247">Create an implementation of `IObjectModelValidator` that doesn't mark any fields as invalid.</span></span>

   [!code-csharp[](validation/sample/Attributes/NullObjectModelValidator.cs?name=snippet_DisableValidation)]

1. <span data-ttu-id="76d3d-248">Adicione o seguinte código ao `Startup.ConfigureServices` para substituir a implementação `IObjectModelValidator` padrão no contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="76d3d-248">Add the following code to `Startup.ConfigureServices` to replace the default `IObjectModelValidator` implementation in the dependency injection container.</span></span>

   [!code-csharp[](validation/sample/Startup.cs?name=snippet_DisableValidation)]

<span data-ttu-id="76d3d-249">Talvez você ainda veja erros de estado de modelo que se originam do model binding.</span><span class="sxs-lookup"><span data-stu-id="76d3d-249">You might still see model state errors that originate from model binding.</span></span>

## <a name="client-side-validation"></a><span data-ttu-id="76d3d-250">Validação do lado do cliente</span><span class="sxs-lookup"><span data-stu-id="76d3d-250">Client-side validation</span></span>

<span data-ttu-id="76d3d-251">A validação do lado do cliente impede o envio até que o formulário seja válido.</span><span class="sxs-lookup"><span data-stu-id="76d3d-251">Client-side validation prevents submission until the form is valid.</span></span> <span data-ttu-id="76d3d-252">O botão Enviar executa o JavaScript que envia o formulário ou exibe mensagens de erro.</span><span class="sxs-lookup"><span data-stu-id="76d3d-252">The Submit button runs JavaScript that either submits the form or displays error messages.</span></span>

<span data-ttu-id="76d3d-253">A validação do lado do cliente evita uma viagem de ida e volta desnecessária ao servidor quando há erros de entrada em um formulário.</span><span class="sxs-lookup"><span data-stu-id="76d3d-253">Client-side validation avoids an unnecessary round trip to the server when there are input errors on a form.</span></span> <span data-ttu-id="76d3d-254">O script a seguir faz referência nas validações de suporte *_Layout.cshtml* e *_ValidationScriptsPartial.cshtml* no lado do cliente:</span><span class="sxs-lookup"><span data-stu-id="76d3d-254">The following script references in *_Layout.cshtml* and *_ValidationScriptsPartial.cshtml* support client-side validation:</span></span>

[!code-cshtml[](validation/sample/Views/Shared/_Layout.cshtml?name=snippet_ScriptTag)]

[!code-cshtml[](validation/sample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_ScriptTags)]

<span data-ttu-id="76d3d-255">O script do [jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive) é uma biblioteca de front-end personalizada da Microsoft que se baseia no popular plug-in [jQuery Validate](https://jqueryvalidation.org/).</span><span class="sxs-lookup"><span data-stu-id="76d3d-255">The [jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive) script is a custom Microsoft front-end library that builds on the popular [jQuery Validate](https://jqueryvalidation.org/) plugin.</span></span> <span data-ttu-id="76d3d-256">Sem o jQuery Unobtrusive Validation, você teria que codificar a mesma lógica de validação em dois locais: uma vez nos atributos de validação do lado do servidor nas propriedades do modelo e, em seguida, novamente nos scripts do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="76d3d-256">Without jQuery Unobtrusive Validation, you would have to code the same validation logic in two places: once in the server-side validation attributes on model properties, and then again in client-side scripts.</span></span> <span data-ttu-id="76d3d-257">Em vez disso, os [Auxiliares de Marca](xref:mvc/views/tag-helpers/intro) e os [Auxiliares HTML](xref:mvc/views/overview) usam os atributos de validação e os metadados de tipo das propriedades do modelo para renderizar atributos `data-` de HTML 5 para os elementos de formulário que precisam de validação.</span><span class="sxs-lookup"><span data-stu-id="76d3d-257">Instead, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use the validation attributes and type metadata from model properties to render HTML 5 `data-` attributes for the form elements that need validation.</span></span> <span data-ttu-id="76d3d-258">O jQuery Unobtrusive Validation analisa os atributos `data-` e passa a lógica para o jQuery Validate, "copiando" efetivamente a lógica de validação do lado do servidor para o cliente.</span><span class="sxs-lookup"><span data-stu-id="76d3d-258">jQuery Unobtrusive Validation parses the `data-` attributes and passes the logic to jQuery Validate, effectively "copying" the server-side validation logic to the client.</span></span> <span data-ttu-id="76d3d-259">Você pode exibir erros de validação no cliente usando os auxiliares de marca conforme mostrado aqui:</span><span class="sxs-lookup"><span data-stu-id="76d3d-259">You can display validation errors on the client using tag helpers as shown here:</span></span>

[!code-cshtml[](validation/sample/Views/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=4-5)]

<span data-ttu-id="76d3d-260">Os auxiliares de marca acima renderizam o HTML a seguir.</span><span class="sxs-lookup"><span data-stu-id="76d3d-260">The preceding tag helpers render the following HTML.</span></span>

```html
<form action="/Movies/Create" method="post">
    <div class="form-horizontal">
        <h4>Movie</h4>
        <div class="text-danger"></div>
        <div class="form-group">
            <label class="col-md-2 control-label" for="ReleaseDate">ReleaseDate</label>
            <div class="col-md-10">
                <input class="form-control" type="datetime"
                data-val="true" data-val-required="The ReleaseDate field is required."
                id="ReleaseDate" name="ReleaseDate" value="">
                <span class="text-danger field-validation-valid"
                data-valmsg-for="ReleaseDate" data-valmsg-replace="true"></span>
            </div>
        </div>
    </div>
</form>
```

<span data-ttu-id="76d3d-261">Observe que os atributos `data-` na saída HTML correspondem aos atributos de validação da propriedade `ReleaseDate`.</span><span class="sxs-lookup"><span data-stu-id="76d3d-261">Notice that the `data-` attributes in the HTML output correspond to the validation attributes for the `ReleaseDate` property.</span></span> <span data-ttu-id="76d3d-262">O atributo `data-val-required` conterá uma mensagem de erro a ser exibida se o usuário não preencher o campo de data de lançamento.</span><span class="sxs-lookup"><span data-stu-id="76d3d-262">The `data-val-required` attribute contains an error message to display if the user doesn't fill in the release date field.</span></span> <span data-ttu-id="76d3d-263">O jQuery Unobtrusive Validation passa esse valor para o método [`required()`](https://jqueryvalidation.org/required-method/) do jQuery Validate, que, por sua vez, exibe essa mensagem no elemento **\<span>** complementar.</span><span class="sxs-lookup"><span data-stu-id="76d3d-263">jQuery Unobtrusive Validation passes this value to the jQuery Validate [`required()`](https://jqueryvalidation.org/required-method/) method, which then displays that message in the accompanying **\<span>** element.</span></span>

<span data-ttu-id="76d3d-264">A validação de tipo de dados é baseada no tipo .NET de uma propriedade, a menos que seja substituída por um atributo `[DataType]`.</span><span class="sxs-lookup"><span data-stu-id="76d3d-264">Data type validation is based on the .NET type of a property, unless that is overridden by a `[DataType]` attribute.</span></span> <span data-ttu-id="76d3d-265">Os navegadores têm suas próprias mensagens de erro padrão, mas o pacote de validação do jQuery Validation Unobtrusive pode substituir essas mensagens.</span><span class="sxs-lookup"><span data-stu-id="76d3d-265">Browsers have their own default error messages, but the jQuery Validation Unobtrusive Validation package can override those messages.</span></span> <span data-ttu-id="76d3d-266">Os atributos `[DataType]` e as subclasses como `[EmailAddress]` permitem que você especifique a mensagem de erro.</span><span class="sxs-lookup"><span data-stu-id="76d3d-266">`[DataType]` attributes and subclasses such as `[EmailAddress]` let you specify the error message.</span></span>

### <a name="add-validation-to-dynamic-forms"></a><span data-ttu-id="76d3d-267">Adicionar validação a formulários dinâmicos</span><span class="sxs-lookup"><span data-stu-id="76d3d-267">Add Validation to Dynamic Forms</span></span>

<span data-ttu-id="76d3d-268">O jQuery Unobtrusive Validation passa parâmetros e a lógica de validação para o jQuery Validate quando a página é carregada pela primeira vez.</span><span class="sxs-lookup"><span data-stu-id="76d3d-268">jQuery Unobtrusive Validation passes validation logic and parameters to jQuery Validate when the page first loads.</span></span> <span data-ttu-id="76d3d-269">Portanto, a validação não funciona automaticamente em formulários gerados dinamicamente.</span><span class="sxs-lookup"><span data-stu-id="76d3d-269">Therefore, validation doesn't work automatically on dynamically generated forms.</span></span> <span data-ttu-id="76d3d-270">Para habilitar a validação é necessário instruir o jQuery Unobtrusive Validation a analisar o formulário dinâmico imediatamente depois de criá-lo.</span><span class="sxs-lookup"><span data-stu-id="76d3d-270">To enable validation, tell jQuery Unobtrusive Validation to parse the dynamic form immediately after you create it.</span></span> <span data-ttu-id="76d3d-271">Por exemplo, o código a seguir define a validação do lado do cliente em um formulário adicionado por meio do AJAX.</span><span class="sxs-lookup"><span data-stu-id="76d3d-271">For example, the following code sets up client-side validation on a form added via AJAX.</span></span>

```js
$.get({
    url: "https://url/that/returns/a/form",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add form. " + errorThrown);
    },
    success: function(newFormHTML) {
        var container = document.getElementById("form-container");
        container.insertAdjacentHTML("beforeend", newFormHTML);
        var forms = container.getElementsByTagName("form");
        var newForm = forms[forms.length - 1];
        $.validator.unobtrusive.parse(newForm);
    }
})
```

<span data-ttu-id="76d3d-272">O método `$.validator.unobtrusive.parse()` aceita um seletor do jQuery para um argumento seu.</span><span class="sxs-lookup"><span data-stu-id="76d3d-272">The `$.validator.unobtrusive.parse()` method accepts a jQuery selector for its one argument.</span></span> <span data-ttu-id="76d3d-273">Esse método instrui o jQuery Unobtrusive Validation a analisar os atributos `data-` de formulários nesse seletor.</span><span class="sxs-lookup"><span data-stu-id="76d3d-273">This method tells jQuery Unobtrusive Validation to parse the `data-` attributes of forms within that selector.</span></span> <span data-ttu-id="76d3d-274">Depois, os valores desses atributos são passados para o plug-in do jQuery Validate.</span><span class="sxs-lookup"><span data-stu-id="76d3d-274">The values of those attributes are then passed to the jQuery Validate plugin.</span></span>

### <a name="add-validation-to-dynamic-controls"></a><span data-ttu-id="76d3d-275">Adicionar validação a controles dinâmicos</span><span class="sxs-lookup"><span data-stu-id="76d3d-275">Add Validation to Dynamic Controls</span></span>

<span data-ttu-id="76d3d-276">O método `$.validator.unobtrusive.parse()` funciona em um formulário inteiro, não em controles individuais gerados dinamicamente, como `<input>` e `<select/>`.</span><span class="sxs-lookup"><span data-stu-id="76d3d-276">The `$.validator.unobtrusive.parse()` method works on an entire form, not on individual dynamically generated controls, such as `<input>` and `<select/>`.</span></span> <span data-ttu-id="76d3d-277">Para uma nova análise do formulário, remova os dados de validação que foram adicionados ao formulário mesmo quando foi analisado anteriormente, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="76d3d-277">To reparse the form, remove the validation data that was added when the form was parsed earlier, as shown in the following example:</span></span>

```js
$.get({
    url: "https://url/that/returns/a/control",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add control. " + errorThrown);
    },
    success: function(newInputHTML) {
        var form = document.getElementById("my-form");
        form.insertAdjacentHTML("beforeend", newInputHTML);
        $(form).removeData("validator")    // Added by jQuery Validate
               .removeData("unobtrusiveValidation");   // Added by jQuery Unobtrusive Validation
        $.validator.unobtrusive.parse(form);
    }
})
```

## <a name="custom-client-side-validation"></a><span data-ttu-id="76d3d-278">Validação personalizada do lado do cliente</span><span class="sxs-lookup"><span data-stu-id="76d3d-278">Custom client-side validation</span></span>

<span data-ttu-id="76d3d-279">A validação personalizada do lado do cliente é feita por meio da geração de atributos de HTML `data-` que funcionam com um adaptador do jQuery Validate personalizado.</span><span class="sxs-lookup"><span data-stu-id="76d3d-279">Custom client-side validation is done by generating `data-` HTML attributes that work with a custom jQuery Validate adapter.</span></span> <span data-ttu-id="76d3d-280">O seguinte código do adaptador de exemplo foi escrito para os atributos `ClassicMovie` e `ClassicMovie2` que foram apresentados no início deste artigo:</span><span class="sxs-lookup"><span data-stu-id="76d3d-280">The following sample adapter code was written for the `ClassicMovie` and `ClassicMovie2` attributes that were introduced earlier in this article:</span></span>

[!code-javascript[](validation/sample/wwwroot/js/classicMovieValidator.js?name=snippet_UnobtrusiveValidation)]

<span data-ttu-id="76d3d-281">Para obter informações sobre como escrever adaptadores, consulte a [documentação do jQuery Validate](https://jqueryvalidation.org/documentation/).</span><span class="sxs-lookup"><span data-stu-id="76d3d-281">For information about how to write adapters, see the [jQuery Validate documentation](https://jqueryvalidation.org/documentation/).</span></span>

<span data-ttu-id="76d3d-282">O uso de um adaptador para um determinado campo é disparado por atributos `data-` que:</span><span class="sxs-lookup"><span data-stu-id="76d3d-282">The use of an adapter for a given field is triggered by `data-` attributes that:</span></span>

* <span data-ttu-id="76d3d-283">Marcam o campo como sujeito à validação do sinalizador (`data-val="true"`).</span><span class="sxs-lookup"><span data-stu-id="76d3d-283">Flag the field as being subject to validation (`data-val="true"`).</span></span>
* <span data-ttu-id="76d3d-284">Identifique o nome de uma regra de validação e o texto da mensagem de erro (por exemplo, `data-val-rulename="Error message."`).</span><span class="sxs-lookup"><span data-stu-id="76d3d-284">Identify a validation rule name and error message text (for example, `data-val-rulename="Error message."`).</span></span>
* <span data-ttu-id="76d3d-285">Forneça os parâmetros adicionais que o validador precisa (por exemplo, `data-val-rulename-parm1="value"`).</span><span class="sxs-lookup"><span data-stu-id="76d3d-285">Provide any additional parameters the validator needs (for example, `data-val-rulename-parm1="value"`).</span></span>

<span data-ttu-id="76d3d-286">A exemplo a seguir mostra os atributos `data-` para o atributo `ClassicMovie` do aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="76d3d-286">The following example shows the `data-` attributes for the sample app's `ClassicMovie` attribute:</span></span>

```html
<input class="form-control" type="datetime"
    data-val="true"
    data-val-classicmovie1="Classic movies must have a release year earlier than 1960."
    data-val-classicmovie1-year="1960"
    data-val-required="The ReleaseDate field is required."
    id="ReleaseDate" name="ReleaseDate" value="">
```

<span data-ttu-id="76d3d-287">Conforme observado anteriormente, os [Auxiliares de Marca](xref:mvc/views/tag-helpers/intro) e [Auxiliares de HTML](xref:mvc/views/overview) usam informações de atributos de validação para renderizar atributos `data-`.</span><span class="sxs-lookup"><span data-stu-id="76d3d-287">As noted earlier, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use information from validation attributes to render `data-` attributes.</span></span> <span data-ttu-id="76d3d-288">Há duas opções para escrever código que resulte na criação de atributos HTML `data-` personalizados:</span><span class="sxs-lookup"><span data-stu-id="76d3d-288">There are two options for writing code that results in the creation of custom `data-` HTML attributes:</span></span>

* <span data-ttu-id="76d3d-289">Criar uma classe que deriva de `AttributeAdapterBase<TAttribute>` e uma classe que implementa `IValidationAttributeAdapterProvider` e registrar o atributo e o adaptador na DI.</span><span class="sxs-lookup"><span data-stu-id="76d3d-289">Create a class that derives from `AttributeAdapterBase<TAttribute>` and a class that implements `IValidationAttributeAdapterProvider`, and register your attribute and its adapter in DI.</span></span> <span data-ttu-id="76d3d-290">Este método segue a [entidade de segurança de responsabilidade única](https://wikipedia.org/wiki/Single_responsibility_principle) em que o código de validação relacionado ao cliente e ao servidor fica em classes separadas.</span><span class="sxs-lookup"><span data-stu-id="76d3d-290">This method follows the [single responsibility principal](https://wikipedia.org/wiki/Single_responsibility_principle) in that server-related and client-related validation code is in separate classes.</span></span> <span data-ttu-id="76d3d-291">O adaptador também tem a vantagem de que, uma vez que ele é registrado na DI, outros serviços da DI ficam disponíveis para ele, se necessário.</span><span class="sxs-lookup"><span data-stu-id="76d3d-291">The adapter also has the advantage that since it is registered in DI, other services in DI are available to it if needed.</span></span>
* <span data-ttu-id="76d3d-292">Implementar `IClientModelValidator` em na classe `ValidationAttribute`.</span><span class="sxs-lookup"><span data-stu-id="76d3d-292">Implement `IClientModelValidator` in your `ValidationAttribute` class.</span></span> <span data-ttu-id="76d3d-293">Esse método poderá ser adequado se o atributo não fizer nenhuma validação do lado do servidor e não precisar de um serviço da DI.</span><span class="sxs-lookup"><span data-stu-id="76d3d-293">This method might be appropriate if the attribute doesn't do any server-side validation and doesn't need any services from DI.</span></span>

### <a name="attributeadapter-for-client-side-validation"></a><span data-ttu-id="76d3d-294">AttributeAdapter para validação do lado do cliente</span><span class="sxs-lookup"><span data-stu-id="76d3d-294">AttributeAdapter for client-side validation</span></span>

<span data-ttu-id="76d3d-295">Esse método de renderização de atributos `data-` em HTML é usado pelo atributo `ClassicMovie` no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="76d3d-295">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie` attribute in the sample app.</span></span> <span data-ttu-id="76d3d-296">Para adicionar a validação do cliente usando esse método:</span><span class="sxs-lookup"><span data-stu-id="76d3d-296">To add client validation by using this method:</span></span>

1. <span data-ttu-id="76d3d-297">Crie uma classe de adaptador de atributo para o atributo de validação personalizado.</span><span class="sxs-lookup"><span data-stu-id="76d3d-297">Create an attribute adapter class for the custom validation attribute.</span></span> <span data-ttu-id="76d3d-298">Derive a classe de [AttributeAdapterBase\<T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="76d3d-298">Derive the class from [AttributeAdapterBase\<T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span></span> <span data-ttu-id="76d3d-299">Criar um método `AddValidation` que adiciona atributos `data-` à saída renderizada, conforme mostrado neste exemplo:</span><span class="sxs-lookup"><span data-stu-id="76d3d-299">Create an `AddValidation` method that adds `data-` attributes to the rendered output, as shown in this example:</span></span>

   [!code-csharp[](validation/sample/Attributes/ClassicMovieAttributeAdapter.cs?name=snippet_ClassicMovieAttributeAdapter)]

1. <span data-ttu-id="76d3d-300">Crie uma classe de provedor de adaptador que implementa <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span><span class="sxs-lookup"><span data-stu-id="76d3d-300">Create an adapter provider class that implements <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span></span> <span data-ttu-id="76d3d-301">No método `GetAttributeAdapter`, passe o atributo personalizado para o construtor do adaptador, conforme mostrado neste exemplo:</span><span class="sxs-lookup"><span data-stu-id="76d3d-301">In the `GetAttributeAdapter` method pass in the custom attribute to the adapter's constructor, as shown in this example:</span></span>

   [!code-csharp[](validation/sample/Attributes/CustomValidationAttributeAdapterProvider.cs?name=snippet_CustomValidationAttributeAdapterProvider)]

1. <span data-ttu-id="76d3d-302">Registre o provedor de adaptador para DI em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="76d3d-302">Register the adapter provider for DI in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](validation/sample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=8-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a><span data-ttu-id="76d3d-303">IClientModelValidator para validação do lado do cliente</span><span class="sxs-lookup"><span data-stu-id="76d3d-303">IClientModelValidator for client-side validation</span></span>

<span data-ttu-id="76d3d-304">Esse método de renderização de atributos `data-` em HTML é usado pelo atributo `ClassicMovie2` no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="76d3d-304">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie2` attribute in the sample app.</span></span> <span data-ttu-id="76d3d-305">Para adicionar a validação do cliente usando esse método:</span><span class="sxs-lookup"><span data-stu-id="76d3d-305">To add client validation by using this method:</span></span>

* <span data-ttu-id="76d3d-306">No atributo de validação personalizado, implemente a interface `IClientModelValidator` e crie um método `AddValidation`.</span><span class="sxs-lookup"><span data-stu-id="76d3d-306">In the custom validation attribute, implement the `IClientModelValidator` interface and create an `AddValidation` method.</span></span> <span data-ttu-id="76d3d-307">No método `AddValidation`, adicione atributos `data-` para validação, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="76d3d-307">In the `AddValidation` method, add `data-` attributes for validation, as shown in the following example:</span></span>

  [!code-csharp[](validation/sample/Attributes/ClassicMovie2Attribute.cs?name=snippet_ClassicMovie2Attribute)]

## <a name="disable-client-side-validation"></a><span data-ttu-id="76d3d-308">Desabilitar validação do lado do cliente</span><span class="sxs-lookup"><span data-stu-id="76d3d-308">Disable client-side validation</span></span>

<span data-ttu-id="76d3d-309">O código a seguir desabilita a validação de cliente nas exibições do MVC:</span><span class="sxs-lookup"><span data-stu-id="76d3d-309">The following code disables client validation in MVC views:</span></span>

[!code-csharp[](validation/sample_snapshot/Startup2.cs?name=snippet_DisableClientValidation)]

<span data-ttu-id="76d3d-310">E em Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="76d3d-310">And in Razor Pages:</span></span>

[!code-csharp[](validation/sample_snapshot/Startup3.cs?name=snippet_DisableClientValidation)]

<span data-ttu-id="76d3d-311">Outra opção para desabilitar a validação do cliente é comentar a referência ao `_ValidationScriptsPartial` em seu arquivo *.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="76d3d-311">Another option for disabling client validation is to comment out the reference to `_ValidationScriptsPartial` in your *.cshtml* file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="76d3d-312">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="76d3d-312">Additional resources</span></span>

* [<span data-ttu-id="76d3d-313">Namespace System.ComponentModel.DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="76d3d-313">System.ComponentModel.DataAnnotations namespace</span></span>](xref:System.ComponentModel.DataAnnotations)
* [<span data-ttu-id="76d3d-314">Model binding</span><span class="sxs-lookup"><span data-stu-id="76d3d-314">Model Binding</span></span>](model-binding.md)
