---
title: Validação de modelo no ASP.NET Core MVC
author: rick-anderson
description: Saiba mais sobre a validação de modelo no ASP.NET Core MVC e Razor Pages.
ms.author: riande
ms.custom: mvc
ms.date: 12/15/2019
uid: mvc/models/validation
ms.openlocfilehash: b697f02183c76b9a96471a748a86c144fde47bb0
ms.sourcegitcommit: f259889044d1fc0f0c7e3882df0008157ced4915
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2020
ms.locfileid: "76268753"
---
# <a name="model-validation-in-aspnet-core-mvc-and-razor-pages"></a><span data-ttu-id="749b6-103">Validação de modelo no ASP.NET Core MVC e Razor Pages</span><span class="sxs-lookup"><span data-stu-id="749b6-103">Model validation in ASP.NET Core MVC and Razor Pages</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="749b6-104">Por [Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="749b6-104">By [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="749b6-105">Este artigo explica como validar a entrada do usuário em um aplicativo em ASP.NET Core MVC ou Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="749b6-105">This article explains how to validate user input in an ASP.NET Core MVC or Razor Pages app.</span></span>

<span data-ttu-id="749b6-106">[Exibir ou baixar um código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/samples) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="749b6-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="model-state"></a><span data-ttu-id="749b6-107">Estado do modelo</span><span class="sxs-lookup"><span data-stu-id="749b6-107">Model state</span></span>

<span data-ttu-id="749b6-108">O estado do modelo representa erros que vêm de dois subsistemas: model binding e validação de modelo.</span><span class="sxs-lookup"><span data-stu-id="749b6-108">Model state represents errors that come from two subsystems: model binding and model validation.</span></span> <span data-ttu-id="749b6-109">Os erros originados na [Associação de modelo](model-binding.md) geralmente são erros de conversão de dados.</span><span class="sxs-lookup"><span data-stu-id="749b6-109">Errors that originate from [model binding](model-binding.md) are generally data conversion errors.</span></span> <span data-ttu-id="749b6-110">Por exemplo, um "x" é inserido em um campo de número inteiro.</span><span class="sxs-lookup"><span data-stu-id="749b6-110">For example, an "x" is entered in an integer field.</span></span> <span data-ttu-id="749b6-111">A validação do modelo ocorre após a associação de modelo e relata erros em que os dados não estão em conformidade com as regras de negócio.</span><span class="sxs-lookup"><span data-stu-id="749b6-111">Model validation occurs after model binding and reports errors where data doesn't conform to business rules.</span></span> <span data-ttu-id="749b6-112">Por exemplo, um 0 é inserido em um campo que espera uma classificação entre 1 e 5.</span><span class="sxs-lookup"><span data-stu-id="749b6-112">For example, a 0 is entered in a field that expects a rating between 1 and 5.</span></span>

<span data-ttu-id="749b6-113">A associação de modelo e a validação de modelo ocorrem antes da execução de uma ação de controlador ou um método de manipulador de Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="749b6-113">Both model binding and model validation occur before the execution of a controller action or a Razor Pages handler method.</span></span> <span data-ttu-id="749b6-114">Nos aplicativos Web, é responsabilidade do aplicativo inspecionar `ModelState.IsValid` e reagir adequadamente.</span><span class="sxs-lookup"><span data-stu-id="749b6-114">For web apps, it's the app's responsibility to inspect `ModelState.IsValid` and react appropriately.</span></span> <span data-ttu-id="749b6-115">Geralmente, os aplicativos Web reexibem a página com uma mensagem de erro:</span><span class="sxs-lookup"><span data-stu-id="749b6-115">Web apps typically redisplay the page with an error message:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

<span data-ttu-id="749b6-116">Os controladores de API Web não precisarão verificar `ModelState.IsValid` se eles tiverem o atributo `[ApiController]`.</span><span class="sxs-lookup"><span data-stu-id="749b6-116">Web API controllers don't have to check `ModelState.IsValid` if they have the `[ApiController]` attribute.</span></span> <span data-ttu-id="749b6-117">Nesse caso, uma resposta HTTP 400 automática contendo detalhes do erro é retornada quando o estado do modelo é inválido.</span><span class="sxs-lookup"><span data-stu-id="749b6-117">In that case, an automatic HTTP 400 response containing error details is returned when model state is invalid.</span></span> <span data-ttu-id="749b6-118">Para obter mais informações, veja [Respostas automáticas HTTP 400](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="749b6-118">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="rerun-validation"></a><span data-ttu-id="749b6-119">Executar validação novamente</span><span class="sxs-lookup"><span data-stu-id="749b6-119">Rerun validation</span></span>

<span data-ttu-id="749b6-120">A validação é automática, mas talvez seja necessário repeti-la manualmente.</span><span class="sxs-lookup"><span data-stu-id="749b6-120">Validation is automatic, but you might want to repeat it manually.</span></span> <span data-ttu-id="749b6-121">Por exemplo, você pode calcular um valor para uma propriedade e executar novamente a validação após a configuração da propriedade com o valor computado.</span><span class="sxs-lookup"><span data-stu-id="749b6-121">For example, you might compute a value for a property and want to rerun validation after setting the property to the computed value.</span></span> <span data-ttu-id="749b6-122">Para reexecutar a validação, chame o método `TryValidateModel`, conforme mostrado aqui:</span><span class="sxs-lookup"><span data-stu-id="749b6-122">To rerun validation, call the `TryValidateModel` method, as shown here:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml.cs?name=snippet_TryValidate&highlight=3-6)]

## <a name="validation-attributes"></a><span data-ttu-id="749b6-123">Atributos de validação</span><span class="sxs-lookup"><span data-stu-id="749b6-123">Validation attributes</span></span>

<span data-ttu-id="749b6-124">Os atributos de validação permitem que você especifique regras de validação para propriedades do modelo.</span><span class="sxs-lookup"><span data-stu-id="749b6-124">Validation attributes let you specify validation rules for model properties.</span></span> <span data-ttu-id="749b6-125">O exemplo a seguir do aplicativo de exemplo mostra uma classe de modelo que é anotada com atributos de validação.</span><span class="sxs-lookup"><span data-stu-id="749b6-125">The following example from the sample app shows a model class that is annotated with validation attributes.</span></span> <span data-ttu-id="749b6-126">O atributo `[ClassicMovie]` é um atributo de validação personalizado e os outros são atributos internos.</span><span class="sxs-lookup"><span data-stu-id="749b6-126">The `[ClassicMovie]` attribute is a custom validation attribute and the others are built-in.</span></span> <span data-ttu-id="749b6-127">Não mostrado é `[ClassicMovieWithClientValidator]`.</span><span class="sxs-lookup"><span data-stu-id="749b6-127">Not shown is `[ClassicMovieWithClientValidator]`.</span></span> <span data-ttu-id="749b6-128">`[ClassicMovieWithClientValidator]` mostra uma maneira alternativa de implementar um atributo personalizado.</span><span class="sxs-lookup"><span data-stu-id="749b6-128">`[ClassicMovieWithClientValidator]` shows an alternative way to implement a custom attribute.</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/Movie.cs?name=snippet_Class)]

## <a name="built-in-attributes"></a><span data-ttu-id="749b6-129">Atributos internos</span><span class="sxs-lookup"><span data-stu-id="749b6-129">Built-in attributes</span></span>

<span data-ttu-id="749b6-130">Aqui estão alguns dos atributos de validação internos:</span><span class="sxs-lookup"><span data-stu-id="749b6-130">Here are some of the built-in validation attributes:</span></span>

* <span data-ttu-id="749b6-131">`[CreditCard]`: valida que a propriedade tem um formato de cartão de crédito.</span><span class="sxs-lookup"><span data-stu-id="749b6-131">`[CreditCard]`: Validates that the property has a credit card format.</span></span>
* <span data-ttu-id="749b6-132">`[Compare]`: valida que duas propriedades em um modelo correspondem.</span><span class="sxs-lookup"><span data-stu-id="749b6-132">`[Compare]`: Validates that two properties in a model match.</span></span>
* <span data-ttu-id="749b6-133">`[EmailAddress]`: valida que a propriedade tem um formato de email.</span><span class="sxs-lookup"><span data-stu-id="749b6-133">`[EmailAddress]`: Validates that the property has an email format.</span></span>
* <span data-ttu-id="749b6-134">`[Phone]`: valida que a propriedade tem um formato de número de telefone.</span><span class="sxs-lookup"><span data-stu-id="749b6-134">`[Phone]`: Validates that the property has a telephone number format.</span></span>
* <span data-ttu-id="749b6-135">`[Range]`: valida que o valor da propriedade cai em um intervalo especificado.</span><span class="sxs-lookup"><span data-stu-id="749b6-135">`[Range]`: Validates that the property value falls within a specified range.</span></span>
* <span data-ttu-id="749b6-136">`[RegularExpression]`: valida que o valor da propriedade corresponde a uma expressão regular especificada.</span><span class="sxs-lookup"><span data-stu-id="749b6-136">`[RegularExpression]`: Validates that the property value matches a specified regular expression.</span></span>
* <span data-ttu-id="749b6-137">`[Required]`: valida que o campo não é nulo.</span><span class="sxs-lookup"><span data-stu-id="749b6-137">`[Required]`: Validates that the field is not null.</span></span> <span data-ttu-id="749b6-138">Consulte [`[Required]` atributo](#required-attribute) para obter detalhes sobre o comportamento desse atributo.</span><span class="sxs-lookup"><span data-stu-id="749b6-138">See [`[Required]` attribute](#required-attribute) for details about this attribute's behavior.</span></span>
* <span data-ttu-id="749b6-139">`[StringLength]`: valida que um valor de propriedade da cadeia de caracteres não excede um limite de comprimento especificado.</span><span class="sxs-lookup"><span data-stu-id="749b6-139">`[StringLength]`: Validates that a string property value doesn't exceed a specified length limit.</span></span>
* <span data-ttu-id="749b6-140">`[Url]`: valida que a propriedade tem um formato de URL.</span><span class="sxs-lookup"><span data-stu-id="749b6-140">`[Url]`: Validates that the property has a URL format.</span></span>
* <span data-ttu-id="749b6-141">`[Remote]`: valida a entrada no cliente chamando um método de ação no servidor.</span><span class="sxs-lookup"><span data-stu-id="749b6-141">`[Remote]`: Validates input on the client by calling an action method on the server.</span></span> <span data-ttu-id="749b6-142">Consulte [`[Remote]` atributo](#remote-attribute) para obter detalhes sobre o comportamento desse atributo.</span><span class="sxs-lookup"><span data-stu-id="749b6-142">See [`[Remote]` attribute](#remote-attribute) for details about this attribute's behavior.</span></span>

<span data-ttu-id="749b6-143">Uma lista completa de atributos de validação pode ser encontrada no namespace [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations).</span><span class="sxs-lookup"><span data-stu-id="749b6-143">A complete list of validation attributes can be found in the [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) namespace.</span></span>

### <a name="error-messages"></a><span data-ttu-id="749b6-144">Mensagens de erro</span><span class="sxs-lookup"><span data-stu-id="749b6-144">Error messages</span></span>

<span data-ttu-id="749b6-145">Os atributos de validação permitem que você especifique a mensagem de erro a ser exibido para uma entrada inválida.</span><span class="sxs-lookup"><span data-stu-id="749b6-145">Validation attributes let you specify the error message to be displayed for invalid input.</span></span> <span data-ttu-id="749b6-146">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="749b6-146">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

<span data-ttu-id="749b6-147">Internamente, a chamada de atributos `String.Format` com um espaço reservado para o nome do campo e, às vezes, espaços reservados adicionais.</span><span class="sxs-lookup"><span data-stu-id="749b6-147">Internally, the attributes call `String.Format` with a placeholder for the field name and sometimes additional placeholders.</span></span> <span data-ttu-id="749b6-148">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="749b6-148">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

<span data-ttu-id="749b6-149">Quando aplicada a uma propriedade `Name`, a mensagem de erro criada pelo código anterior seria "Comprimento do nome ter entre 6 e 8.".</span><span class="sxs-lookup"><span data-stu-id="749b6-149">When applied to a `Name` property, the error message created by the preceding code would be "Name length must be between 6 and 8.".</span></span>

<span data-ttu-id="749b6-150">Para descobrir quais parâmetros são passados para `String.Format` no caso de uma mensagem de erro de um atributo específico, consulte o [código-fonte de DataAnnotations](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span><span class="sxs-lookup"><span data-stu-id="749b6-150">To find out which parameters are passed to `String.Format` for a particular attribute's error message, see the [DataAnnotations source code](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span></span>

## <a name="required-attribute"></a><span data-ttu-id="749b6-151">Atributo [Required]</span><span class="sxs-lookup"><span data-stu-id="749b6-151">[Required] attribute</span></span>

<span data-ttu-id="749b6-152">O sistema de validação no .NET Core 3,0 e posterior trata parâmetros não anuláveis ou propriedades associadas como se tivessem um atributo `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="749b6-152">The validation system in .NET Core 3.0 and later treats non-nullable parameters or bound properties as if they had a `[Required]` attribute.</span></span> <span data-ttu-id="749b6-153">[Tipos de valor](/dotnet/csharp/language-reference/keywords/value-types) como `decimal` e `int` são não anuláveis.</span><span class="sxs-lookup"><span data-stu-id="749b6-153">[Value types](/dotnet/csharp/language-reference/keywords/value-types) such as `decimal` and `int` are non-nullable.</span></span> <span data-ttu-id="749b6-154">Esse comportamento pode ser desabilitado Configurando <xref:Microsoft.AspNetCore.Mvc.MvcOptions.SuppressImplicitRequiredAttributeForNonNullableReferenceTypes> no `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="749b6-154">This behavior can be disabled by configuring <xref:Microsoft.AspNetCore.Mvc.MvcOptions.SuppressImplicitRequiredAttributeForNonNullableReferenceTypes> in `Startup.ConfigureServices`:</span></span>

<span data-ttu-id="749b6-155">' ' serviços Csharp. Addcontrollers (opções = > opções. SuppressImplicitRequiredAttributeForNonNullableReferenceTypes = true); ...</span><span class="sxs-lookup"><span data-stu-id="749b6-155">\`\`csharp services.AddControllers(options => options.SuppressImplicitRequiredAttributeForNonNullableReferenceTypes = true); ...</span></span>

### <a name="required-validation-on-the-server"></a><span data-ttu-id="749b6-156">Validação de [Required] no servidor</span><span class="sxs-lookup"><span data-stu-id="749b6-156">[Required] validation on the server</span></span>

<span data-ttu-id="749b6-157">No servidor, um valor obrigatório será considerado ausente se a propriedade for nula.</span><span class="sxs-lookup"><span data-stu-id="749b6-157">On the server, a required value is considered missing if the property is null.</span></span> <span data-ttu-id="749b6-158">Um campo não anulável é sempre válido e a `[Required]` mensagem de erro do atributo nunca é exibida.</span><span class="sxs-lookup"><span data-stu-id="749b6-158">A non-nullable field is always valid, and the `[Required]` attribute's error message is never displayed.</span></span>

<span data-ttu-id="749b6-159">No entanto, o model binding para uma propriedade que não permite valor nulo pode falhar, resultando em uma mensagem de erro como `The value '' is invalid`.</span><span class="sxs-lookup"><span data-stu-id="749b6-159">However, model binding for a non-nullable property may fail, resulting in an error message such as `The value '' is invalid`.</span></span> <span data-ttu-id="749b6-160">Para especificar uma mensagem de erro personalizada para a validação de tipos não anuláveis do lado do servidor, você tem as seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="749b6-160">To specify a custom error message for server-side validation of non-nullable types, you have the following options:</span></span>

* <span data-ttu-id="749b6-161">Tornar o campo anulável (por exemplo, `decimal?` em vez de `decimal`).</span><span class="sxs-lookup"><span data-stu-id="749b6-161">Make the field nullable (for example, `decimal?` instead of `decimal`).</span></span> <span data-ttu-id="749b6-162">Tipos de valor [Nullable\<T>](/dotnet/csharp/programming-guide/nullable-types/) são tratados como tipos que permitem valor nulo padrão.</span><span class="sxs-lookup"><span data-stu-id="749b6-162">[Nullable\<T>](/dotnet/csharp/programming-guide/nullable-types/) value types are treated like standard nullable types.</span></span>
* <span data-ttu-id="749b6-163">Especifique a mensagem de erro padrão a ser usada pelo model binding, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="749b6-163">Specify the default error message to be used by model binding, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=5-6)]

  <span data-ttu-id="749b6-164">Para obter mais informações sobre erros de model binding para os quais você pode definir mensagens padrão, consulte <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span><span class="sxs-lookup"><span data-stu-id="749b6-164">For more information about model binding errors that you can set default messages for, see <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span></span>

### <a name="required-validation-on-the-client"></a><span data-ttu-id="749b6-165">Validação de [Required] no cliente</span><span class="sxs-lookup"><span data-stu-id="749b6-165">[Required] validation on the client</span></span>

<span data-ttu-id="749b6-166">Cadeias de caracteres e tipos que não permitem valor nulo são tratados de maneira diferente no cliente em comparação com servidor.</span><span class="sxs-lookup"><span data-stu-id="749b6-166">Non-nullable types and strings are handled differently on the client compared to the server.</span></span> <span data-ttu-id="749b6-167">No cliente:</span><span class="sxs-lookup"><span data-stu-id="749b6-167">On the client:</span></span>

* <span data-ttu-id="749b6-168">Um valor será considerado presente apenas se a entrada for inserida para ele.</span><span class="sxs-lookup"><span data-stu-id="749b6-168">A value is considered present only if input is entered for it.</span></span> <span data-ttu-id="749b6-169">Portanto, a validação do lado do cliente lida com tipos que não permitem valor nulo da mesma maneira que com tipos que permitem valor nulo.</span><span class="sxs-lookup"><span data-stu-id="749b6-169">Therefore, client-side validation handles non-nullable types the same as nullable types.</span></span>
* <span data-ttu-id="749b6-170">O espaço em branco em um campo de cadeia de caracteres é considerado uma entrada válida pelo método [required](https://jqueryvalidation.org/required-method/) do jQuery Validation.</span><span class="sxs-lookup"><span data-stu-id="749b6-170">Whitespace in a string field is considered valid input by the jQuery Validation [required](https://jqueryvalidation.org/required-method/) method.</span></span> <span data-ttu-id="749b6-171">A validação do lado do servidor considerará um campo de cadeia de caracteres necessário inválido somente se o espaço em branco for inserido.</span><span class="sxs-lookup"><span data-stu-id="749b6-171">Server-side validation considers a required string field invalid if only whitespace is entered.</span></span>

<span data-ttu-id="749b6-172">Conforme observado anteriormente, os tipos que não permitem valor nulo são tratados como se tivessem um atributo `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="749b6-172">As noted earlier, non-nullable types are treated as though they had a `[Required]` attribute.</span></span> <span data-ttu-id="749b6-173">Isso significa que você obtém a validação do lado do cliente mesmo que não aplique o atributo `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="749b6-173">That means you get client-side validation even if you don't apply the `[Required]` attribute.</span></span> <span data-ttu-id="749b6-174">Mas se não for usar o atributo, você obterá uma mensagem de erro padrão.</span><span class="sxs-lookup"><span data-stu-id="749b6-174">But if you don't use the attribute, you get a default error message.</span></span> <span data-ttu-id="749b6-175">Para especificar uma mensagem de erro personalizada, use o atributo.</span><span class="sxs-lookup"><span data-stu-id="749b6-175">To specify a custom error message, use the attribute.</span></span>

## <a name="remote-attribute"></a><span data-ttu-id="749b6-176">Atributo [Remote]</span><span class="sxs-lookup"><span data-stu-id="749b6-176">[Remote] attribute</span></span>

<span data-ttu-id="749b6-177">O atributo `[Remote]` implementa a validação do lado do cliente, exigindo a chamada de um método no servidor para determinar se a entrada do campo é válida.</span><span class="sxs-lookup"><span data-stu-id="749b6-177">The `[Remote]` attribute implements client-side validation that requires calling a method on the server to determine whether field input is valid.</span></span> <span data-ttu-id="749b6-178">Por exemplo, o aplicativo pode precisar verificar se um nome de usuário já está em uso.</span><span class="sxs-lookup"><span data-stu-id="749b6-178">For example, the app may need to verify whether a user name is already in use.</span></span>

<span data-ttu-id="749b6-179">Para implementar a validação remota:</span><span class="sxs-lookup"><span data-stu-id="749b6-179">To implement remote validation:</span></span>

1. <span data-ttu-id="749b6-180">Crie um método de ação para JavaScript para chamar.</span><span class="sxs-lookup"><span data-stu-id="749b6-180">Create an action method for JavaScript to call.</span></span>  <span data-ttu-id="749b6-181">O método [remoto](https://jqueryvalidation.org/remote-method/) do jQuery Validate espera uma resposta JSON:</span><span class="sxs-lookup"><span data-stu-id="749b6-181">The jQuery Validate [remote](https://jqueryvalidation.org/remote-method/) method expects a JSON response:</span></span>

   * <span data-ttu-id="749b6-182">`true` significa que os dados de entrada são válidos.</span><span class="sxs-lookup"><span data-stu-id="749b6-182">`true` means the input data is valid.</span></span>
   * <span data-ttu-id="749b6-183">`false`, `undefined` ou `null` significa que a entrada é inválida.</span><span class="sxs-lookup"><span data-stu-id="749b6-183">`false`, `undefined`, or `null` means the input is invalid.</span></span> <span data-ttu-id="749b6-184">Exiba a mensagem de erro padrão.</span><span class="sxs-lookup"><span data-stu-id="749b6-184">Display the default error message.</span></span>
   * <span data-ttu-id="749b6-185">Qualquer outra cadeia de caracteres significa que a entrada é inválida.</span><span class="sxs-lookup"><span data-stu-id="749b6-185">Any other string means the input is invalid.</span></span> <span data-ttu-id="749b6-186">Exiba a cadeia de caracteres como uma mensagem de erro personalizada.</span><span class="sxs-lookup"><span data-stu-id="749b6-186">Display the string as a custom error message.</span></span>

   <span data-ttu-id="749b6-187">Aqui está um exemplo de um método de ação que retorna uma mensagem de erro personalizada:</span><span class="sxs-lookup"><span data-stu-id="749b6-187">Here's an example of an action method that returns a custom error message:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. <span data-ttu-id="749b6-188">Na classe de modelo, anote a propriedade com um atributo `[Remote]` que aponta para o método de ação de validação, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="749b6-188">In the model class, annotate the property with a `[Remote]` attribute that points to the validation action method, as shown in the following example:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Models/User.cs?name=snippet_Email)]
 
   <span data-ttu-id="749b6-189">O atributo `[Remote]` está no namespace `Microsoft.AspNetCore.Mvc`.</span><span class="sxs-lookup"><span data-stu-id="749b6-189">The `[Remote]` attribute is in the `Microsoft.AspNetCore.Mvc` namespace.</span></span>
   
### <a name="additional-fields"></a><span data-ttu-id="749b6-190">Campos adicionais</span><span class="sxs-lookup"><span data-stu-id="749b6-190">Additional fields</span></span>

<span data-ttu-id="749b6-191">A propriedade `AdditionalFields` do atributo `[Remote]` permite validar combinações de campos em relação aos dados no servidor.</span><span class="sxs-lookup"><span data-stu-id="749b6-191">The `AdditionalFields` property of the `[Remote]` attribute lets you validate combinations of fields against data on the server.</span></span> <span data-ttu-id="749b6-192">Por exemplo, se o modelo `User` tinha as propriedades `FirstName` e `LastName`, pode ser interessante verificar se nenhum usuário existente já tem esse par de nomes.</span><span class="sxs-lookup"><span data-stu-id="749b6-192">For example, if the `User` model had `FirstName` and `LastName` properties, you might want to verify that no existing users already have that pair of names.</span></span> <span data-ttu-id="749b6-193">O exemplo a seguir mostra como usar `AdditionalFields`:</span><span class="sxs-lookup"><span data-stu-id="749b6-193">The following example shows how to use `AdditionalFields`:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/User.cs?name=snippet_Name&highlight=1,5)]

<span data-ttu-id="749b6-194">`AdditionalFields` pode ser definido explicitamente para as cadeias de caracteres "FirstName" e "LastName", mas o uso do operador [nameof](/dotnet/csharp/language-reference/keywords/nameof) simplifica a refatoração posterior.</span><span class="sxs-lookup"><span data-stu-id="749b6-194">`AdditionalFields` could be set explicitly to the strings "FirstName" and "LastName", but using the [nameof](/dotnet/csharp/language-reference/keywords/nameof) operator simplifies later refactoring.</span></span> <span data-ttu-id="749b6-195">O método de ação para essa validação deve aceitar os argumentos `firstName` e `lastName`:</span><span class="sxs-lookup"><span data-stu-id="749b6-195">The action method for this validation must accept both `firstName` and `lastName` arguments:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyName)]

<span data-ttu-id="749b6-196">Quando o usuário insere o primeiro nome ou o sobrenome, o JavaScript faz uma chamada remota para ver se esse par de nomes foi usado.</span><span class="sxs-lookup"><span data-stu-id="749b6-196">When the user enters a first or last name, JavaScript makes a remote call to see if that pair of names has been taken.</span></span>

<span data-ttu-id="749b6-197">Para validar dois ou mais campos adicionais, forneça-os como uma lista delimitada por vírgulas.</span><span class="sxs-lookup"><span data-stu-id="749b6-197">To validate two or more additional fields, provide them as a comma-delimited list.</span></span> <span data-ttu-id="749b6-198">Por exemplo, para adicionar uma propriedade `MiddleName` ao modelo, defina o atributo `[Remote]`, conforme mostrado no seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="749b6-198">For example, to add a `MiddleName` property to the model, set the `[Remote]` attribute as shown in the following example:</span></span>

```cs
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

<span data-ttu-id="749b6-199">`AdditionalFields`, como todos os argumentos de atributo, deve ser uma expressão de constante.</span><span class="sxs-lookup"><span data-stu-id="749b6-199">`AdditionalFields`, like all attribute arguments, must be a constant expression.</span></span> <span data-ttu-id="749b6-200">Portanto, não use uma [cadeia de caracteres interpolada](/dotnet/csharp/language-reference/keywords/interpolated-strings) nem chame <xref:System.String.Join*> para inicializar `AdditionalFields`.</span><span class="sxs-lookup"><span data-stu-id="749b6-200">Therefore, don't use an [interpolated string](/dotnet/csharp/language-reference/keywords/interpolated-strings) or call <xref:System.String.Join*> to initialize `AdditionalFields`.</span></span>

## <a name="alternatives-to-built-in-attributes"></a><span data-ttu-id="749b6-201">Alternativas aos atributos internos</span><span class="sxs-lookup"><span data-stu-id="749b6-201">Alternatives to built-in attributes</span></span>

<span data-ttu-id="749b6-202">Se precisar de validação não fornecida por atributos internos, você poderá:</span><span class="sxs-lookup"><span data-stu-id="749b6-202">If you need validation not provided by built-in attributes, you can:</span></span>

* <span data-ttu-id="749b6-203">[Criar atributos personalizados](#custom-attributes).</span><span class="sxs-lookup"><span data-stu-id="749b6-203">[Create custom attributes](#custom-attributes).</span></span>
* <span data-ttu-id="749b6-204">[Implementar IValidatableObject](#ivalidatableobject).</span><span class="sxs-lookup"><span data-stu-id="749b6-204">[Implement IValidatableObject](#ivalidatableobject).</span></span>

## <a name="custom-attributes"></a><span data-ttu-id="749b6-205">Atributos personalizados</span><span class="sxs-lookup"><span data-stu-id="749b6-205">Custom attributes</span></span>

<span data-ttu-id="749b6-206">Para cenários não compatíveis com os atributos de validação internos, você pode criar atributos de validação personalizados.</span><span class="sxs-lookup"><span data-stu-id="749b6-206">For scenarios that the built-in validation attributes don't handle, you can create custom validation attributes.</span></span> <span data-ttu-id="749b6-207">Crie uma classe que herda de <xref:System.ComponentModel.DataAnnotations.ValidationAttribute> e substitua o método <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*>.</span><span class="sxs-lookup"><span data-stu-id="749b6-207">Create a class that inherits from <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>, and override the <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> method.</span></span>

<span data-ttu-id="749b6-208">O método `IsValid` aceita um objeto chamado *valor*, que é a entrada a ser validada.</span><span class="sxs-lookup"><span data-stu-id="749b6-208">The `IsValid` method accepts an object named *value*, which is the input to be validated.</span></span> <span data-ttu-id="749b6-209">Uma sobrecarga também aceita um objeto `ValidationContext`, que fornece informações adicionais, como a instância do modelo criada pelo model binding.</span><span class="sxs-lookup"><span data-stu-id="749b6-209">An overload also accepts a `ValidationContext` object, which provides additional information, such as the model instance created by model binding.</span></span>

<span data-ttu-id="749b6-210">O exemplo a seguir valida se a data de lançamento de um filme no gênero *Clássico* não é posterior a um ano especificado.</span><span class="sxs-lookup"><span data-stu-id="749b6-210">The following example validates that the release date for a movie in the *Classic* genre isn't later than a specified year.</span></span> <span data-ttu-id="749b6-211">O atributo `[ClassicMovie]`:</span><span class="sxs-lookup"><span data-stu-id="749b6-211">The `[ClassicMovie]` attribute:</span></span>

* <span data-ttu-id="749b6-212">Só é executado no servidor.</span><span class="sxs-lookup"><span data-stu-id="749b6-212">Is only run on the server.</span></span>
* <span data-ttu-id="749b6-213">Para filmes clássicos, o valida a data de lançamento:</span><span class="sxs-lookup"><span data-stu-id="749b6-213">For Classic movies, validates the release date:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieAttribute.cs?name=snippet_Class)]

<span data-ttu-id="749b6-214">A variável `movie` no exemplo anterior representa um objeto `Movie` que contém os dados do envio do formulário.</span><span class="sxs-lookup"><span data-stu-id="749b6-214">The `movie` variable in the preceding example represents a `Movie` object that contains the data from the form submission.</span></span> <span data-ttu-id="749b6-215">Quando a validação falha, um `ValidationResult` com uma mensagem erro será retornado.</span><span class="sxs-lookup"><span data-stu-id="749b6-215">When validation fails, a `ValidationResult` with an error message is returned.</span></span>

## <a name="ivalidatableobject"></a><span data-ttu-id="749b6-216">IValidatableObject</span><span class="sxs-lookup"><span data-stu-id="749b6-216">IValidatableObject</span></span>

<span data-ttu-id="749b6-217">O exemplo anterior funciona apenas com tipos `Movie`.</span><span class="sxs-lookup"><span data-stu-id="749b6-217">The preceding example works only with `Movie` types.</span></span> <span data-ttu-id="749b6-218">Outra opção para validação de nível de classe é implementar `IValidatableObject` na classe de modelo, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="749b6-218">Another option for class-level validation is to implement `IValidatableObject` in the model class, as shown in the following example:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/ValidatableMovie.cs?name=snippet_Class&highlight=1,26-34)]

## <a name="top-level-node-validation"></a><span data-ttu-id="749b6-219">Validação de nó de nível superior</span><span class="sxs-lookup"><span data-stu-id="749b6-219">Top-level node validation</span></span>

<span data-ttu-id="749b6-220">Os nós de nível superior incluem:</span><span class="sxs-lookup"><span data-stu-id="749b6-220">Top-level nodes include:</span></span>

* <span data-ttu-id="749b6-221">Parâmetros de ação</span><span class="sxs-lookup"><span data-stu-id="749b6-221">Action parameters</span></span>
* <span data-ttu-id="749b6-222">Propriedades do controlador</span><span class="sxs-lookup"><span data-stu-id="749b6-222">Controller properties</span></span>
* <span data-ttu-id="749b6-223">Parâmetros do manipulador de página</span><span class="sxs-lookup"><span data-stu-id="749b6-223">Page handler parameters</span></span>
* <span data-ttu-id="749b6-224">Propriedades do modelo de página</span><span class="sxs-lookup"><span data-stu-id="749b6-224">Page model properties</span></span>

<span data-ttu-id="749b6-225">Os nós de nível superior associados ao modelo são validados além de validar as propriedades do modelo.</span><span class="sxs-lookup"><span data-stu-id="749b6-225">Model-bound top-level nodes are validated in addition to validating model properties.</span></span> <span data-ttu-id="749b6-226">No exemplo a seguir do aplicativo de exemplo, o método `VerifyPhone` usa o <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> para validar o parâmetro de ação `phone`:</span><span class="sxs-lookup"><span data-stu-id="749b6-226">In the following example from the sample app, the `VerifyPhone` method uses the <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> to validate the `phone` action parameter:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

<span data-ttu-id="749b6-227">Os nós de nível superior podem usar <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> com atributos de validação.</span><span class="sxs-lookup"><span data-stu-id="749b6-227">Top-level nodes can use <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> with validation attributes.</span></span> <span data-ttu-id="749b6-228">No exemplo a seguir do aplicativo de exemplo, o método `CheckAge` especifica que o parâmetro `age` deve ser associado da cadeia de caracteres de consulta quando o formulário é enviado:</span><span class="sxs-lookup"><span data-stu-id="749b6-228">In the following example from the sample app, the `CheckAge` method specifies that the `age` parameter must be bound from the query string when the form is submitted:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_CheckAgeSignature)]

<span data-ttu-id="749b6-229">Na página Verificar Idade (*CheckAge.cshtml*), há dois formulários.</span><span class="sxs-lookup"><span data-stu-id="749b6-229">In the Check Age page (*CheckAge.cshtml*), there are two forms.</span></span> <span data-ttu-id="749b6-230">O primeiro formulário envia um `Age` valor de `99` como um parâmetro de cadeia de caracteres de consulta: `https://localhost:5001/Users/CheckAge?Age=99`.</span><span class="sxs-lookup"><span data-stu-id="749b6-230">The first form submits an `Age` value of `99` as a query string parameter: `https://localhost:5001/Users/CheckAge?Age=99`.</span></span>

<span data-ttu-id="749b6-231">Quando um parâmetro `age` formatado corretamente da cadeia de caracteres de consulta é enviado, o formulário é validado.</span><span class="sxs-lookup"><span data-stu-id="749b6-231">When a properly formatted `age` parameter from the query string is submitted, the form validates.</span></span>

<span data-ttu-id="749b6-232">O segundo formulário na página Verificar Idade envia o valor `Age` no corpo da solicitação e a validação falha.</span><span class="sxs-lookup"><span data-stu-id="749b6-232">The second form on the Check Age page submits the `Age` value in the body of the request, and validation fails.</span></span> <span data-ttu-id="749b6-233">A associação falha porque o parâmetro `age` deve vir de uma cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="749b6-233">Binding fails because the `age` parameter must come from a query string.</span></span>

## <a name="maximum-errors"></a><span data-ttu-id="749b6-234">Máximo de erros</span><span class="sxs-lookup"><span data-stu-id="749b6-234">Maximum errors</span></span>

<span data-ttu-id="749b6-235">A validação para quando o número máximo de erros é atingido (200 por padrão).</span><span class="sxs-lookup"><span data-stu-id="749b6-235">Validation stops when the maximum number of errors is reached (200 by default).</span></span> <span data-ttu-id="749b6-236">Você pode configurar esse número com o seguinte código no `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="749b6-236">You can configure this number with the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=4)]

## <a name="maximum-recursion"></a><span data-ttu-id="749b6-237">Recursão máxima</span><span class="sxs-lookup"><span data-stu-id="749b6-237">Maximum recursion</span></span>

<span data-ttu-id="749b6-238"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> percorre o grafo de objeto do modelo que está sendo validado.</span><span class="sxs-lookup"><span data-stu-id="749b6-238"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> traverses the object graph of the model being validated.</span></span> <span data-ttu-id="749b6-239">Para modelos que são de profundidade ou são recursivos infinitamente, a validação pode resultar em estouro de pilha.</span><span class="sxs-lookup"><span data-stu-id="749b6-239">For models that are deep or are infinitely recursive, validation may result in stack overflow.</span></span> <span data-ttu-id="749b6-240">O [MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) fornece uma maneira de interromper a validação antes que a recursão visitante exceda uma profundidade configurada.</span><span class="sxs-lookup"><span data-stu-id="749b6-240">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) provides a way to stop validation early if the visitor recursion exceeds a configured depth.</span></span> <span data-ttu-id="749b6-241">O valor padrão de `MvcOptions.MaxValidationDepth` é 32.</span><span class="sxs-lookup"><span data-stu-id="749b6-241">The default value of `MvcOptions.MaxValidationDepth` is 32.</span></span>

## <a name="automatic-short-circuit"></a><span data-ttu-id="749b6-242">Curto-circuito automático</span><span class="sxs-lookup"><span data-stu-id="749b6-242">Automatic short-circuit</span></span>

<span data-ttu-id="749b6-243">A validação sofrerá curto-circuito (será ignorada) automaticamente se o grafo do modelo não exigir validação.</span><span class="sxs-lookup"><span data-stu-id="749b6-243">Validation is automatically short-circuited (skipped) if the model graph doesn't require validation.</span></span> <span data-ttu-id="749b6-244">Objetos em que o runtime ignora a validação para inclusão de coleções de primitivos (como `byte[]`, `string[]` e `Dictionary<string, string>`) e grafos de objetos complexos que não têm um validador.</span><span class="sxs-lookup"><span data-stu-id="749b6-244">Objects that the runtime skips validation for include collections of primitives (such as `byte[]`, `string[]`, `Dictionary<string, string>`) and complex object graphs that don't have any validators.</span></span>

## <a name="disable-validation"></a><span data-ttu-id="749b6-245">Desabilitar validação</span><span class="sxs-lookup"><span data-stu-id="749b6-245">Disable validation</span></span>

<span data-ttu-id="749b6-246">Para desabilitar a validação:</span><span class="sxs-lookup"><span data-stu-id="749b6-246">To disable validation:</span></span>

1. <span data-ttu-id="749b6-247">Crie uma implementação de `IObjectModelValidator` que não marque nenhum campo como inválido.</span><span class="sxs-lookup"><span data-stu-id="749b6-247">Create an implementation of `IObjectModelValidator` that doesn't mark any fields as invalid.</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/NullObjectModelValidator.cs?name=snippet_Class)]

1. <span data-ttu-id="749b6-248">Adicione o seguinte código ao `Startup.ConfigureServices` para substituir a implementação `IObjectModelValidator` padrão no contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="749b6-248">Add the following code to `Startup.ConfigureServices` to replace the default `IObjectModelValidator` implementation in the dependency injection container.</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_DisableValidation)]

<span data-ttu-id="749b6-249">Talvez você ainda veja erros de estado de modelo que se originam do model binding.</span><span class="sxs-lookup"><span data-stu-id="749b6-249">You might still see model state errors that originate from model binding.</span></span>

## <a name="client-side-validation"></a><span data-ttu-id="749b6-250">Validação do lado do cliente</span><span class="sxs-lookup"><span data-stu-id="749b6-250">Client-side validation</span></span>

<span data-ttu-id="749b6-251">A validação do lado do cliente impede o envio até que o formulário seja válido.</span><span class="sxs-lookup"><span data-stu-id="749b6-251">Client-side validation prevents submission until the form is valid.</span></span> <span data-ttu-id="749b6-252">O botão Enviar executa o JavaScript que envia o formulário ou exibe mensagens de erro.</span><span class="sxs-lookup"><span data-stu-id="749b6-252">The Submit button runs JavaScript that either submits the form or displays error messages.</span></span>

<span data-ttu-id="749b6-253">A validação do lado do cliente evita uma viagem de ida e volta desnecessária ao servidor quando há erros de entrada em um formulário.</span><span class="sxs-lookup"><span data-stu-id="749b6-253">Client-side validation avoids an unnecessary round trip to the server when there are input errors on a form.</span></span> <span data-ttu-id="749b6-254">O script a seguir faz referência nas validações de suporte *_Layout.cshtml* e *_ValidationScriptsPartial.cshtml* no lado do cliente:</span><span class="sxs-lookup"><span data-stu-id="749b6-254">The following script references in *_Layout.cshtml* and *_ValidationScriptsPartial.cshtml* support client-side validation:</span></span>

[!code-cshtml[](validation/samples/3.x/ValidationSample/Views/Shared/_Layout.cshtml?name=snippet_Scripts)]

[!code-cshtml[](validation/samples/3.x/ValidationSample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_Scripts)]

<span data-ttu-id="749b6-255">O script do [jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive) é uma biblioteca de front-end personalizada da Microsoft que se baseia no popular plug-in [jQuery Validate](https://jqueryvalidation.org/).</span><span class="sxs-lookup"><span data-stu-id="749b6-255">The [jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive) script is a custom Microsoft front-end library that builds on the popular [jQuery Validate](https://jqueryvalidation.org/) plugin.</span></span> <span data-ttu-id="749b6-256">Sem o jQuery Unobtrusive Validation, você teria que codificar a mesma lógica de validação em dois locais: uma vez nos atributos de validação do lado do servidor nas propriedades do modelo e, em seguida, novamente nos scripts do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="749b6-256">Without jQuery Unobtrusive Validation, you would have to code the same validation logic in two places: once in the server-side validation attributes on model properties, and then again in client-side scripts.</span></span> <span data-ttu-id="749b6-257">Em vez disso, os [Auxiliares de Marca](xref:mvc/views/tag-helpers/intro) e os [Auxiliares HTML](xref:mvc/views/overview) usam os atributos de validação e os metadados de tipo das propriedades do modelo para renderizar atributos `data-` de HTML 5 para os elementos de formulário que precisam de validação.</span><span class="sxs-lookup"><span data-stu-id="749b6-257">Instead, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use the validation attributes and type metadata from model properties to render HTML 5 `data-` attributes for the form elements that need validation.</span></span> <span data-ttu-id="749b6-258">O jQuery Unobtrusive Validation analisa os atributos `data-` e passa a lógica para o jQuery Validate, "copiando" efetivamente a lógica de validação do lado do servidor para o cliente.</span><span class="sxs-lookup"><span data-stu-id="749b6-258">jQuery Unobtrusive Validation parses the `data-` attributes and passes the logic to jQuery Validate, effectively "copying" the server-side validation logic to the client.</span></span> <span data-ttu-id="749b6-259">Você pode exibir erros de validação no cliente usando os auxiliares de marca conforme mostrado aqui:</span><span class="sxs-lookup"><span data-stu-id="749b6-259">You can display validation errors on the client using tag helpers as shown here:</span></span>

[!code-cshtml[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=3-4)]

<span data-ttu-id="749b6-260">Os auxiliares de marcação anteriores renderizam o seguinte HTML:</span><span class="sxs-lookup"><span data-stu-id="749b6-260">The preceding tag helpers render the following HTML:</span></span>

```html
<div class="form-group">
    <label class="control-label" for="Movie_ReleaseDate">Release Date</label>
    <input class="form-control" type="date" data-val="true"
        data-val-required="The Release Date field is required."
        id="Movie_ReleaseDate" name="Movie.ReleaseDate" value="">
    <span class="text-danger field-validation-valid"
        data-valmsg-for="Movie.ReleaseDate" data-valmsg-replace="true"></span>
</div>
```

<span data-ttu-id="749b6-261">Observe que os atributos `data-` na saída HTML correspondem aos atributos de validação da propriedade `Movie.ReleaseDate`.</span><span class="sxs-lookup"><span data-stu-id="749b6-261">Notice that the `data-` attributes in the HTML output correspond to the validation attributes for the `Movie.ReleaseDate` property.</span></span> <span data-ttu-id="749b6-262">O atributo `data-val-required` conterá uma mensagem de erro a ser exibida se o usuário não preencher o campo de data de lançamento.</span><span class="sxs-lookup"><span data-stu-id="749b6-262">The `data-val-required` attribute contains an error message to display if the user doesn't fill in the release date field.</span></span> <span data-ttu-id="749b6-263">a validação não invasiva do jQuery passa esse valor para o método de validação do jQuery [requerido ()](https://jqueryvalidation.org/required-method/) , que, em seguida, exibe essa mensagem no elemento **> do\<span** em acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="749b6-263">jQuery Unobtrusive Validation passes this value to the jQuery Validate [required()](https://jqueryvalidation.org/required-method/) method, which then displays that message in the accompanying **\<span>** element.</span></span>

<span data-ttu-id="749b6-264">A validação de tipo de dados é baseada no tipo .NET de uma propriedade, a menos que seja substituída por um atributo `[DataType]`.</span><span class="sxs-lookup"><span data-stu-id="749b6-264">Data type validation is based on the .NET type of a property, unless that is overridden by a `[DataType]` attribute.</span></span> <span data-ttu-id="749b6-265">Os navegadores têm suas próprias mensagens de erro padrão, mas o pacote de validação do jQuery Validation Unobtrusive pode substituir essas mensagens.</span><span class="sxs-lookup"><span data-stu-id="749b6-265">Browsers have their own default error messages, but the jQuery Validation Unobtrusive Validation package can override those messages.</span></span> <span data-ttu-id="749b6-266">Os atributos `[DataType]` e as subclasses como `[EmailAddress]` permitem que você especifique a mensagem de erro.</span><span class="sxs-lookup"><span data-stu-id="749b6-266">`[DataType]` attributes and subclasses such as `[EmailAddress]` let you specify the error message.</span></span>

## <a name="unobtrusive-validation"></a><span data-ttu-id="749b6-267">Validação não invasiva</span><span class="sxs-lookup"><span data-stu-id="749b6-267">Unobtrusive validation</span></span>

<span data-ttu-id="749b6-268">Para obter informações sobre a validação não invasiva, consulte [este problema do GitHub](https://github.com/aspnet/AspNetCore.Docs/issues/1111).</span><span class="sxs-lookup"><span data-stu-id="749b6-268">For information on unobtrusive validation, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/1111).</span></span>

### <a name="add-validation-to-dynamic-forms"></a><span data-ttu-id="749b6-269">Adicionar validação a formulários dinâmicos</span><span class="sxs-lookup"><span data-stu-id="749b6-269">Add Validation to Dynamic Forms</span></span>

<span data-ttu-id="749b6-270">O jQuery Unobtrusive Validation passa parâmetros e a lógica de validação para o jQuery Validate quando a página é carregada pela primeira vez.</span><span class="sxs-lookup"><span data-stu-id="749b6-270">jQuery Unobtrusive Validation passes validation logic and parameters to jQuery Validate when the page first loads.</span></span> <span data-ttu-id="749b6-271">Portanto, a validação não funciona automaticamente em formulários gerados dinamicamente.</span><span class="sxs-lookup"><span data-stu-id="749b6-271">Therefore, validation doesn't work automatically on dynamically generated forms.</span></span> <span data-ttu-id="749b6-272">Para habilitar a validação é necessário instruir o jQuery Unobtrusive Validation a analisar o formulário dinâmico imediatamente depois de criá-lo.</span><span class="sxs-lookup"><span data-stu-id="749b6-272">To enable validation, tell jQuery Unobtrusive Validation to parse the dynamic form immediately after you create it.</span></span> <span data-ttu-id="749b6-273">Por exemplo, o código a seguir define a validação do lado do cliente em um formulário adicionado por meio do AJAX.</span><span class="sxs-lookup"><span data-stu-id="749b6-273">For example, the following code sets up client-side validation on a form added via AJAX.</span></span>

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

<span data-ttu-id="749b6-274">O método `$.validator.unobtrusive.parse()` aceita um seletor do jQuery para um argumento seu.</span><span class="sxs-lookup"><span data-stu-id="749b6-274">The `$.validator.unobtrusive.parse()` method accepts a jQuery selector for its one argument.</span></span> <span data-ttu-id="749b6-275">Esse método instrui o jQuery Unobtrusive Validation a analisar os atributos `data-` de formulários nesse seletor.</span><span class="sxs-lookup"><span data-stu-id="749b6-275">This method tells jQuery Unobtrusive Validation to parse the `data-` attributes of forms within that selector.</span></span> <span data-ttu-id="749b6-276">Depois, os valores desses atributos são passados para o plug-in do jQuery Validate.</span><span class="sxs-lookup"><span data-stu-id="749b6-276">The values of those attributes are then passed to the jQuery Validate plugin.</span></span>

### <a name="add-validation-to-dynamic-controls"></a><span data-ttu-id="749b6-277">Adicionar validação a controles dinâmicos</span><span class="sxs-lookup"><span data-stu-id="749b6-277">Add Validation to Dynamic Controls</span></span>

<span data-ttu-id="749b6-278">O método `$.validator.unobtrusive.parse()` funciona em um formulário inteiro, não em controles individuais gerados dinamicamente, como `<input>` e `<select/>`.</span><span class="sxs-lookup"><span data-stu-id="749b6-278">The `$.validator.unobtrusive.parse()` method works on an entire form, not on individual dynamically generated controls, such as `<input>` and `<select/>`.</span></span> <span data-ttu-id="749b6-279">Para uma nova análise do formulário, remova os dados de validação que foram adicionados ao formulário mesmo quando foi analisado anteriormente, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="749b6-279">To reparse the form, remove the validation data that was added when the form was parsed earlier, as shown in the following example:</span></span>

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

## <a name="custom-client-side-validation"></a><span data-ttu-id="749b6-280">Validação personalizada do lado do cliente</span><span class="sxs-lookup"><span data-stu-id="749b6-280">Custom client-side validation</span></span>

<span data-ttu-id="749b6-281">A validação personalizada do lado do cliente é feita por meio da geração de atributos de HTML `data-` que funcionam com um adaptador do jQuery Validate personalizado.</span><span class="sxs-lookup"><span data-stu-id="749b6-281">Custom client-side validation is done by generating `data-` HTML attributes that work with a custom jQuery Validate adapter.</span></span> <span data-ttu-id="749b6-282">O seguinte código do adaptador de exemplo foi escrito para os atributos `[ClassicMovie]` e `[ClassicMovieWithClientValidator]` que foram apresentados no início deste artigo:</span><span class="sxs-lookup"><span data-stu-id="749b6-282">The following sample adapter code was written for the `[ClassicMovie]` and `[ClassicMovieWithClientValidator]` attributes that were introduced earlier in this article:</span></span>

[!code-javascript[](validation/samples/3.x/ValidationSample/wwwroot/js/classicMovieValidator.js)]

<span data-ttu-id="749b6-283">Para obter informações sobre como escrever adaptadores, consulte a [documentação do jQuery Validate](https://jqueryvalidation.org/documentation/).</span><span class="sxs-lookup"><span data-stu-id="749b6-283">For information about how to write adapters, see the [jQuery Validate documentation](https://jqueryvalidation.org/documentation/).</span></span>

<span data-ttu-id="749b6-284">O uso de um adaptador para um determinado campo é disparado por atributos `data-` que:</span><span class="sxs-lookup"><span data-stu-id="749b6-284">The use of an adapter for a given field is triggered by `data-` attributes that:</span></span>

* <span data-ttu-id="749b6-285">Marcam o campo como sujeito à validação do sinalizador (`data-val="true"`).</span><span class="sxs-lookup"><span data-stu-id="749b6-285">Flag the field as being subject to validation (`data-val="true"`).</span></span>
* <span data-ttu-id="749b6-286">Identifique o nome de uma regra de validação e o texto da mensagem de erro (por exemplo, `data-val-rulename="Error message."`).</span><span class="sxs-lookup"><span data-stu-id="749b6-286">Identify a validation rule name and error message text (for example, `data-val-rulename="Error message."`).</span></span>
* <span data-ttu-id="749b6-287">Forneça os parâmetros adicionais que o validador precisa (por exemplo, `data-val-rulename-param1="value"`).</span><span class="sxs-lookup"><span data-stu-id="749b6-287">Provide any additional parameters the validator needs (for example, `data-val-rulename-param1="value"`).</span></span>

<span data-ttu-id="749b6-288">A exemplo a seguir mostra os atributos `data-` para o atributo `ClassicMovie` do aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="749b6-288">The following example shows the `data-` attributes for the sample app's `ClassicMovie` attribute:</span></span>

```html
<input class="form-control" type="date"
    data-val="true"
    data-val-classicmovie="Classic movies must have a release year no later than 1960."
    data-val-classicmovie-year="1960"
    data-val-required="The Release Date field is required."
    id="Movie_ReleaseDate" name="Movie.ReleaseDate" value="">
```

<span data-ttu-id="749b6-289">Conforme observado anteriormente, os [Auxiliares de Marca](xref:mvc/views/tag-helpers/intro) e [Auxiliares de HTML](xref:mvc/views/overview) usam informações de atributos de validação para renderizar atributos `data-`.</span><span class="sxs-lookup"><span data-stu-id="749b6-289">As noted earlier, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use information from validation attributes to render `data-` attributes.</span></span> <span data-ttu-id="749b6-290">Há duas opções para escrever código que resulte na criação de atributos HTML `data-` personalizados:</span><span class="sxs-lookup"><span data-stu-id="749b6-290">There are two options for writing code that results in the creation of custom `data-` HTML attributes:</span></span>

* <span data-ttu-id="749b6-291">Criar uma classe que deriva de `AttributeAdapterBase<TAttribute>` e uma classe que implementa `IValidationAttributeAdapterProvider` e registrar o atributo e o adaptador na DI.</span><span class="sxs-lookup"><span data-stu-id="749b6-291">Create a class that derives from `AttributeAdapterBase<TAttribute>` and a class that implements `IValidationAttributeAdapterProvider`, and register your attribute and its adapter in DI.</span></span> <span data-ttu-id="749b6-292">Este método segue a [entidade de segurança de responsabilidade única](https://wikipedia.org/wiki/Single_responsibility_principle) em que o código de validação relacionado ao cliente e ao servidor fica em classes separadas.</span><span class="sxs-lookup"><span data-stu-id="749b6-292">This method follows the [single responsibility principal](https://wikipedia.org/wiki/Single_responsibility_principle) in that server-related and client-related validation code is in separate classes.</span></span> <span data-ttu-id="749b6-293">O adaptador também tem a vantagem de que, uma vez que ele é registrado na DI, outros serviços da DI ficam disponíveis para ele, se necessário.</span><span class="sxs-lookup"><span data-stu-id="749b6-293">The adapter also has the advantage that since it is registered in DI, other services in DI are available to it if needed.</span></span>
* <span data-ttu-id="749b6-294">Implementar `IClientModelValidator` em na classe `ValidationAttribute`.</span><span class="sxs-lookup"><span data-stu-id="749b6-294">Implement `IClientModelValidator` in your `ValidationAttribute` class.</span></span> <span data-ttu-id="749b6-295">Esse método poderá ser adequado se o atributo não fizer nenhuma validação do lado do servidor e não precisar de um serviço da DI.</span><span class="sxs-lookup"><span data-stu-id="749b6-295">This method might be appropriate if the attribute doesn't do any server-side validation and doesn't need any services from DI.</span></span>

### <a name="attributeadapter-for-client-side-validation"></a><span data-ttu-id="749b6-296">AttributeAdapter para validação do lado do cliente</span><span class="sxs-lookup"><span data-stu-id="749b6-296">AttributeAdapter for client-side validation</span></span>

<span data-ttu-id="749b6-297">Esse método de renderização de atributos `data-` em HTML é usado pelo atributo `ClassicMovie` no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="749b6-297">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie` attribute in the sample app.</span></span> <span data-ttu-id="749b6-298">Para adicionar a validação do cliente usando esse método:</span><span class="sxs-lookup"><span data-stu-id="749b6-298">To add client validation by using this method:</span></span>

1. <span data-ttu-id="749b6-299">Crie uma classe de adaptador de atributo para o atributo de validação personalizado.</span><span class="sxs-lookup"><span data-stu-id="749b6-299">Create an attribute adapter class for the custom validation attribute.</span></span> <span data-ttu-id="749b6-300">Derive a classe de [AttributeAdapterBase\<T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="749b6-300">Derive the class from [AttributeAdapterBase\<T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span></span> <span data-ttu-id="749b6-301">Criar um método `AddValidation` que adiciona atributos `data-` à saída renderizada, conforme mostrado neste exemplo:</span><span class="sxs-lookup"><span data-stu-id="749b6-301">Create an `AddValidation` method that adds `data-` attributes to the rendered output, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieAttributeAdapter.cs?name=snippet_Class)]

1. <span data-ttu-id="749b6-302">Crie uma classe de provedor de adaptador que implementa <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span><span class="sxs-lookup"><span data-stu-id="749b6-302">Create an adapter provider class that implements <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span></span> <span data-ttu-id="749b6-303">No método `GetAttributeAdapter`, passe o atributo personalizado para o construtor do adaptador, conforme mostrado neste exemplo:</span><span class="sxs-lookup"><span data-stu-id="749b6-303">In the `GetAttributeAdapter` method pass in the custom attribute to the adapter's constructor, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/CustomValidationAttributeAdapterProvider.cs?name=snippet_Class)]

1. <span data-ttu-id="749b6-304">Registre o provedor de adaptador para DI em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="749b6-304">Register the adapter provider for DI in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=9-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a><span data-ttu-id="749b6-305">IClientModelValidator para validação do lado do cliente</span><span class="sxs-lookup"><span data-stu-id="749b6-305">IClientModelValidator for client-side validation</span></span>

<span data-ttu-id="749b6-306">Esse método de renderização de atributos `data-` em HTML é usado pelo atributo `ClassicMovieWithClientValidator` no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="749b6-306">This method of rendering `data-` attributes in HTML is used by the `ClassicMovieWithClientValidator` attribute in the sample app.</span></span> <span data-ttu-id="749b6-307">Para adicionar a validação do cliente usando esse método:</span><span class="sxs-lookup"><span data-stu-id="749b6-307">To add client validation by using this method:</span></span>

* <span data-ttu-id="749b6-308">No atributo de validação personalizado, implemente a interface `IClientModelValidator` e crie um método `AddValidation`.</span><span class="sxs-lookup"><span data-stu-id="749b6-308">In the custom validation attribute, implement the `IClientModelValidator` interface and create an `AddValidation` method.</span></span> <span data-ttu-id="749b6-309">No método `AddValidation`, adicione atributos `data-` para validação, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="749b6-309">In the `AddValidation` method, add `data-` attributes for validation, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieWithClientValidatorAttribute.cs?name=snippet_Class)]

## <a name="disable-client-side-validation"></a><span data-ttu-id="749b6-310">Desabilitar validação do lado do cliente</span><span class="sxs-lookup"><span data-stu-id="749b6-310">Disable client-side validation</span></span>

<span data-ttu-id="749b6-311">O código a seguir desabilita a validação do cliente no Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="749b6-311">The following code disables client validation in Razor Pages:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_DisableClientValidation&highlight=2-5)]

<span data-ttu-id="749b6-312">Outras opções para desabilitar a validação do lado do cliente:</span><span class="sxs-lookup"><span data-stu-id="749b6-312">Other options to disable client-side validation:</span></span>

* <span data-ttu-id="749b6-313">Comente a referência a `_ValidationScriptsPartial` em todos os arquivos *. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="749b6-313">Comment out the reference to `_ValidationScriptsPartial` in all the *.cshtml* files.</span></span>
* <span data-ttu-id="749b6-314">Remova o conteúdo do arquivo *Pages\Shared\_ValidationScriptsPartial. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="749b6-314">Remove the contents of the *Pages\Shared\_ValidationScriptsPartial.cshtml* file.</span></span>

<span data-ttu-id="749b6-315">A abordagem anterior não impedirá a validação do lado do cliente da biblioteca de classes do Razor de identidade ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="749b6-315">The preceding approach won't prevent client side validation of ASP.NET Core Identity Razor Class Library.</span></span> <span data-ttu-id="749b6-316">Para obter mais informações, consulte <xref:security/authentication/scaffold-identity>.</span><span class="sxs-lookup"><span data-stu-id="749b6-316">For more information, see <xref:security/authentication/scaffold-identity>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="749b6-317">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="749b6-317">Additional resources</span></span>

* [<span data-ttu-id="749b6-318">Namespace System.ComponentModel.DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="749b6-318">System.ComponentModel.DataAnnotations namespace</span></span>](xref:System.ComponentModel.DataAnnotations)
* [<span data-ttu-id="749b6-319">Model binding</span><span class="sxs-lookup"><span data-stu-id="749b6-319">Model Binding</span></span>](model-binding.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="749b6-320">Este artigo explica como validar a entrada do usuário em um aplicativo em ASP.NET Core MVC ou Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="749b6-320">This article explains how to validate user input in an ASP.NET Core MVC or Razor Pages app.</span></span>

<span data-ttu-id="749b6-321">[Exibir ou baixar um código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="749b6-321">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="model-state"></a><span data-ttu-id="749b6-322">Estado do modelo</span><span class="sxs-lookup"><span data-stu-id="749b6-322">Model state</span></span>

<span data-ttu-id="749b6-323">O estado do modelo representa erros que vêm de dois subsistemas: model binding e validação de modelo.</span><span class="sxs-lookup"><span data-stu-id="749b6-323">Model state represents errors that come from two subsystems: model binding and model validation.</span></span> <span data-ttu-id="749b6-324">Erros que se originam de [model binding](model-binding.md) geralmente são erros de conversão de dados (por exemplo, um "x" é inserido em um campo que espera um inteiro).</span><span class="sxs-lookup"><span data-stu-id="749b6-324">Errors that originate from [model binding](model-binding.md) are generally data conversion errors (for example, an "x" is entered in a field that expects an integer).</span></span> <span data-ttu-id="749b6-325">A validação do modelo ocorre após o model binding e relata os erros em que os dados não estão em conformidade com as regras de negócio (por exemplo, um 0 é inserido em um campo que espera uma classificação entre 1 e 5).</span><span class="sxs-lookup"><span data-stu-id="749b6-325">Model validation occurs after model binding and reports errors where the data doesn't conform to business rules (for example, a 0 is entered in a field that expects a rating between 1 and 5).</span></span>

<span data-ttu-id="749b6-326">O model binding e a validação ocorrem antes da execução de uma ação do controlador ou de um método de manipulador do Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="749b6-326">Both model binding and validation occur before the execution of a controller action or a Razor Pages handler method.</span></span> <span data-ttu-id="749b6-327">Nos aplicativos Web, é responsabilidade do aplicativo inspecionar `ModelState.IsValid` e reagir adequadamente.</span><span class="sxs-lookup"><span data-stu-id="749b6-327">For web apps, it's the app's responsibility to inspect `ModelState.IsValid` and react appropriately.</span></span> <span data-ttu-id="749b6-328">Geralmente, os aplicativos Web reexibem a página com uma mensagem de erro:</span><span class="sxs-lookup"><span data-stu-id="749b6-328">Web apps typically redisplay the page with an error message:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Create.cshtml.cs?name=snippet&highlight=3-6)]

<span data-ttu-id="749b6-329">Os controladores de API Web não precisarão verificar `ModelState.IsValid` se eles tiverem o atributo `[ApiController]`.</span><span class="sxs-lookup"><span data-stu-id="749b6-329">Web API controllers don't have to check `ModelState.IsValid` if they have the `[ApiController]` attribute.</span></span> <span data-ttu-id="749b6-330">Nesse caso, uma resposta HTTP 400 automática contendo detalhes do erro é retornada quando o estado do modelo é inválido.</span><span class="sxs-lookup"><span data-stu-id="749b6-330">In that case, an automatic HTTP 400 response containing error details is returned when model state is invalid.</span></span> <span data-ttu-id="749b6-331">Para obter mais informações, veja [Respostas automáticas HTTP 400](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="749b6-331">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="rerun-validation"></a><span data-ttu-id="749b6-332">Executar validação novamente</span><span class="sxs-lookup"><span data-stu-id="749b6-332">Rerun validation</span></span>

<span data-ttu-id="749b6-333">A validação é automática, mas talvez seja necessário repeti-la manualmente.</span><span class="sxs-lookup"><span data-stu-id="749b6-333">Validation is automatic, but you might want to repeat it manually.</span></span> <span data-ttu-id="749b6-334">Por exemplo, você pode calcular um valor para uma propriedade e executar novamente a validação após a configuração da propriedade com o valor computado.</span><span class="sxs-lookup"><span data-stu-id="749b6-334">For example, you might compute a value for a property and want to rerun validation after setting the property to the computed value.</span></span> <span data-ttu-id="749b6-335">Para reexecutar a validação, chame o método `TryValidateModel`, conforme mostrado aqui:</span><span class="sxs-lookup"><span data-stu-id="749b6-335">To rerun validation, call the `TryValidateModel` method, as shown here:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/MoviesController.cs?name=snippet_TryValidateModel&highlight=11)]

## <a name="validation-attributes"></a><span data-ttu-id="749b6-336">Atributos de validação</span><span class="sxs-lookup"><span data-stu-id="749b6-336">Validation attributes</span></span>

<span data-ttu-id="749b6-337">Os atributos de validação permitem que você especifique regras de validação para propriedades do modelo.</span><span class="sxs-lookup"><span data-stu-id="749b6-337">Validation attributes let you specify validation rules for model properties.</span></span> <span data-ttu-id="749b6-338">O exemplo a seguir do [aplicativo de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) mostra uma classe de modelo que é anotada com atributos de validação.</span><span class="sxs-lookup"><span data-stu-id="749b6-338">The following example from [the sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) shows a model class that is annotated with validation attributes.</span></span> <span data-ttu-id="749b6-339">O atributo `[ClassicMovie]` é um atributo de validação personalizado e os outros são atributos internos.</span><span class="sxs-lookup"><span data-stu-id="749b6-339">The `[ClassicMovie]` attribute is a custom validation attribute and the others are built-in.</span></span> <span data-ttu-id="749b6-340">Não mostrado é `[ClassicMovie2]`, que mostra uma maneira alternativa de implementar um atributo personalizado.</span><span class="sxs-lookup"><span data-stu-id="749b6-340">Not shown is `[ClassicMovie2]`, which shows an alternative way to implement a custom attribute.</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/Movie.cs?name=snippet_ModelClass)]

## <a name="built-in-attributes"></a><span data-ttu-id="749b6-341">Atributos internos</span><span class="sxs-lookup"><span data-stu-id="749b6-341">Built-in attributes</span></span>

<span data-ttu-id="749b6-342">Os atributos de validação internos incluem:</span><span class="sxs-lookup"><span data-stu-id="749b6-342">Built-in validation attributes include:</span></span>

* <span data-ttu-id="749b6-343">`[CreditCard]`: valida que a propriedade tem um formato de cartão de crédito.</span><span class="sxs-lookup"><span data-stu-id="749b6-343">`[CreditCard]`: Validates that the property has a credit card format.</span></span>
* <span data-ttu-id="749b6-344">`[Compare]`: valida que duas propriedades em um modelo correspondem.</span><span class="sxs-lookup"><span data-stu-id="749b6-344">`[Compare]`: Validates that two properties in a model match.</span></span> <span data-ttu-id="749b6-345">Por exemplo, o arquivo *Register.cshtml.cs* usa `[Compare]` para validar a correspondência de duas senhas inseridas.</span><span class="sxs-lookup"><span data-stu-id="749b6-345">For example, the *Register.cshtml.cs* file uses `[Compare]` to validate the two entered passwords match.</span></span> <span data-ttu-id="749b6-346">[Scaffold identidade](xref:security/authentication/scaffold-identity) para ver o código de registro.</span><span class="sxs-lookup"><span data-stu-id="749b6-346">[Scaffold Identity](xref:security/authentication/scaffold-identity) to see the Register code.</span></span>
* <span data-ttu-id="749b6-347">`[EmailAddress]`: valida que a propriedade tem um formato de email.</span><span class="sxs-lookup"><span data-stu-id="749b6-347">`[EmailAddress]`: Validates that the property has an email format.</span></span>
* <span data-ttu-id="749b6-348">`[Phone]`: valida que a propriedade tem um formato de número de telefone.</span><span class="sxs-lookup"><span data-stu-id="749b6-348">`[Phone]`: Validates that the property has a telephone number format.</span></span>
* <span data-ttu-id="749b6-349">`[Range]`: valida que o valor da propriedade cai em um intervalo especificado.</span><span class="sxs-lookup"><span data-stu-id="749b6-349">`[Range]`: Validates that the property value falls within a specified range.</span></span>
* <span data-ttu-id="749b6-350">`[RegularExpression]`: valida que o valor da propriedade corresponde a uma expressão regular especificada.</span><span class="sxs-lookup"><span data-stu-id="749b6-350">`[RegularExpression]`: Validates that the property value matches a specified regular expression.</span></span>
* <span data-ttu-id="749b6-351">`[Required]`: valida que o campo não é nulo.</span><span class="sxs-lookup"><span data-stu-id="749b6-351">`[Required]`: Validates that the field is not null.</span></span> <span data-ttu-id="749b6-352">Consulte [`[Required]` atributo](#required-attribute) para obter detalhes sobre o comportamento desse atributo.</span><span class="sxs-lookup"><span data-stu-id="749b6-352">See [`[Required]` attribute](#required-attribute) for details about this attribute's behavior.</span></span>
* <span data-ttu-id="749b6-353">`[StringLength]`: valida que um valor de propriedade da cadeia de caracteres não excede um limite de comprimento especificado.</span><span class="sxs-lookup"><span data-stu-id="749b6-353">`[StringLength]`: Validates that a string property value doesn't exceed a specified length limit.</span></span>
* <span data-ttu-id="749b6-354">`[Url]`: valida que a propriedade tem um formato de URL.</span><span class="sxs-lookup"><span data-stu-id="749b6-354">`[Url]`: Validates that the property has a URL format.</span></span>
* <span data-ttu-id="749b6-355">`[Remote]`: valida a entrada no cliente chamando um método de ação no servidor.</span><span class="sxs-lookup"><span data-stu-id="749b6-355">`[Remote]`: Validates input on the client by calling an action method on the server.</span></span> <span data-ttu-id="749b6-356">Consulte [`[Remote]` atributo](#remote-attribute) para obter detalhes sobre o comportamento desse atributo.</span><span class="sxs-lookup"><span data-stu-id="749b6-356">See [`[Remote]` attribute](#remote-attribute) for details about this attribute's behavior.</span></span>

<span data-ttu-id="749b6-357">Ao usar o atributo `[RegularExpression]` com validação do lado do cliente, o Regex é executado em JavaScript no cliente.</span><span class="sxs-lookup"><span data-stu-id="749b6-357">When using the `[RegularExpression]` attribute with client-side validation, the regex is executed in JavaScript on the client.</span></span> <span data-ttu-id="749b6-358">Isso significa que o comportamento de correspondência [ECMAScript](/dotnet/standard/base-types/regular-expression-options#ecmascript-matching-behavior) será usado.</span><span class="sxs-lookup"><span data-stu-id="749b6-358">This means [ECMAScript](/dotnet/standard/base-types/regular-expression-options#ecmascript-matching-behavior) matching behavior will be used.</span></span> <span data-ttu-id="749b6-359">Para obter mais informações, consulte [esse problema de GitHub](https://github.com/dotnet/corefx/issues/42487).</span><span class="sxs-lookup"><span data-stu-id="749b6-359">For more information, see [this GitHub issue](https://github.com/dotnet/corefx/issues/42487).</span></span>

<span data-ttu-id="749b6-360">Uma lista completa de atributos de validação pode ser encontrada no namespace [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations).</span><span class="sxs-lookup"><span data-stu-id="749b6-360">A complete list of validation attributes can be found in the [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) namespace.</span></span>

### <a name="error-messages"></a><span data-ttu-id="749b6-361">Mensagens de erro</span><span class="sxs-lookup"><span data-stu-id="749b6-361">Error messages</span></span>

<span data-ttu-id="749b6-362">Os atributos de validação permitem que você especifique a mensagem de erro a ser exibido para uma entrada inválida.</span><span class="sxs-lookup"><span data-stu-id="749b6-362">Validation attributes let you specify the error message to be displayed for invalid input.</span></span> <span data-ttu-id="749b6-363">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="749b6-363">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

<span data-ttu-id="749b6-364">Internamente, a chamada de atributos `String.Format` com um espaço reservado para o nome do campo e, às vezes, espaços reservados adicionais.</span><span class="sxs-lookup"><span data-stu-id="749b6-364">Internally, the attributes call `String.Format` with a placeholder for the field name and sometimes additional placeholders.</span></span> <span data-ttu-id="749b6-365">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="749b6-365">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

<span data-ttu-id="749b6-366">Quando aplicada a uma propriedade `Name`, a mensagem de erro criada pelo código anterior seria "Comprimento do nome ter entre 6 e 8.".</span><span class="sxs-lookup"><span data-stu-id="749b6-366">When applied to a `Name` property, the error message created by the preceding code would be "Name length must be between 6 and 8.".</span></span>

<span data-ttu-id="749b6-367">Para descobrir quais parâmetros são passados para `String.Format` no caso de uma mensagem de erro de um atributo específico, consulte o [código-fonte de DataAnnotations](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span><span class="sxs-lookup"><span data-stu-id="749b6-367">To find out which parameters are passed to `String.Format` for a particular attribute's error message, see the [DataAnnotations source code](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span></span>

## <a name="required-attribute"></a><span data-ttu-id="749b6-368">Atributo [Required]</span><span class="sxs-lookup"><span data-stu-id="749b6-368">[Required] attribute</span></span>

<span data-ttu-id="749b6-369">Por padrão, o sistema de validação trata parâmetros não anuláveis ou propriedades como se tivessem um atributo `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="749b6-369">By default, the validation system treats non-nullable parameters or properties as if they had a `[Required]` attribute.</span></span> <span data-ttu-id="749b6-370">[Tipos de valor](/dotnet/csharp/language-reference/keywords/value-types) como `decimal` e `int` são não anuláveis.</span><span class="sxs-lookup"><span data-stu-id="749b6-370">[Value types](/dotnet/csharp/language-reference/keywords/value-types) such as `decimal` and `int` are non-nullable.</span></span>

### <a name="required-validation-on-the-server"></a><span data-ttu-id="749b6-371">Validação de [Required] no servidor</span><span class="sxs-lookup"><span data-stu-id="749b6-371">[Required] validation on the server</span></span>

<span data-ttu-id="749b6-372">No servidor, um valor obrigatório será considerado ausente se a propriedade for nula.</span><span class="sxs-lookup"><span data-stu-id="749b6-372">On the server, a required value is considered missing if the property is null.</span></span> <span data-ttu-id="749b6-373">Um campo não anulável é sempre válido e a mensagem de erro do atributo [Required] nunca é exibida.</span><span class="sxs-lookup"><span data-stu-id="749b6-373">A non-nullable field is always valid, and the [Required] attribute's error message is never displayed.</span></span>

<span data-ttu-id="749b6-374">No entanto, o model binding para uma propriedade que não permite valor nulo pode falhar, resultando em uma mensagem de erro como `The value '' is invalid`.</span><span class="sxs-lookup"><span data-stu-id="749b6-374">However, model binding for a non-nullable property may fail, resulting in an error message such as `The value '' is invalid`.</span></span> <span data-ttu-id="749b6-375">Para especificar uma mensagem de erro personalizada para a validação de tipos não anuláveis do lado do servidor, você tem as seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="749b6-375">To specify a custom error message for server-side validation of non-nullable types, you have the following options:</span></span>

* <span data-ttu-id="749b6-376">Tornar o campo anulável (por exemplo, `decimal?` em vez de `decimal`).</span><span class="sxs-lookup"><span data-stu-id="749b6-376">Make the field nullable (for example, `decimal?` instead of `decimal`).</span></span> <span data-ttu-id="749b6-377">Tipos de valor [Nullable\<T>](/dotnet/csharp/programming-guide/nullable-types/) são tratados como tipos que permitem valor nulo padrão.</span><span class="sxs-lookup"><span data-stu-id="749b6-377">[Nullable\<T>](/dotnet/csharp/programming-guide/nullable-types/) value types are treated like standard nullable types.</span></span>
* <span data-ttu-id="749b6-378">Especifique a mensagem de erro padrão a ser usada pelo model binding, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="749b6-378">Specify the default error message to be used by model binding, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=4-5)]

  <span data-ttu-id="749b6-379">Para obter mais informações sobre erros de model binding para os quais você pode definir mensagens padrão, consulte <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span><span class="sxs-lookup"><span data-stu-id="749b6-379">For more information about model binding errors that you can set default messages for, see <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span></span>

### <a name="required-validation-on-the-client"></a><span data-ttu-id="749b6-380">Validação de [Required] no cliente</span><span class="sxs-lookup"><span data-stu-id="749b6-380">[Required] validation on the client</span></span>

<span data-ttu-id="749b6-381">Cadeias de caracteres e tipos que não permitem valor nulo são tratados de maneira diferente no cliente em comparação com servidor.</span><span class="sxs-lookup"><span data-stu-id="749b6-381">Non-nullable types and strings are handled differently on the client compared to the server.</span></span> <span data-ttu-id="749b6-382">No cliente:</span><span class="sxs-lookup"><span data-stu-id="749b6-382">On the client:</span></span>

* <span data-ttu-id="749b6-383">Um valor será considerado presente apenas se a entrada for inserida para ele.</span><span class="sxs-lookup"><span data-stu-id="749b6-383">A value is considered present only if input is entered for it.</span></span> <span data-ttu-id="749b6-384">Portanto, a validação do lado do cliente lida com tipos que não permitem valor nulo da mesma maneira que com tipos que permitem valor nulo.</span><span class="sxs-lookup"><span data-stu-id="749b6-384">Therefore, client-side validation handles non-nullable types the same as nullable types.</span></span>
* <span data-ttu-id="749b6-385">O espaço em branco em um campo de cadeia de caracteres é considerado uma entrada válida pelo método [required](https://jqueryvalidation.org/required-method/) do jQuery Validation.</span><span class="sxs-lookup"><span data-stu-id="749b6-385">Whitespace in a string field is considered valid input by the jQuery Validation [required](https://jqueryvalidation.org/required-method/) method.</span></span> <span data-ttu-id="749b6-386">A validação do lado do servidor considerará um campo de cadeia de caracteres necessário inválido somente se o espaço em branco for inserido.</span><span class="sxs-lookup"><span data-stu-id="749b6-386">Server-side validation considers a required string field invalid if only whitespace is entered.</span></span>

<span data-ttu-id="749b6-387">Conforme observado anteriormente, os tipos que não permitem valor nulo são tratados como se tivessem um atributo `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="749b6-387">As noted earlier, non-nullable types are treated as though they had a `[Required]` attribute.</span></span> <span data-ttu-id="749b6-388">Isso significa que você obtém a validação do lado do cliente mesmo que não aplique o atributo `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="749b6-388">That means you get client-side validation even if you don't apply the `[Required]` attribute.</span></span> <span data-ttu-id="749b6-389">Mas se não for usar o atributo, você obterá uma mensagem de erro padrão.</span><span class="sxs-lookup"><span data-stu-id="749b6-389">But if you don't use the attribute, you get a default error message.</span></span> <span data-ttu-id="749b6-390">Para especificar uma mensagem de erro personalizada, use o atributo.</span><span class="sxs-lookup"><span data-stu-id="749b6-390">To specify a custom error message, use the attribute.</span></span>

## <a name="remote-attribute"></a><span data-ttu-id="749b6-391">Atributo [Remote]</span><span class="sxs-lookup"><span data-stu-id="749b6-391">[Remote] attribute</span></span>

<span data-ttu-id="749b6-392">O atributo `[Remote]` implementa a validação do lado do cliente, exigindo a chamada de um método no servidor para determinar se a entrada do campo é válida.</span><span class="sxs-lookup"><span data-stu-id="749b6-392">The `[Remote]` attribute implements client-side validation that requires calling a method on the server to determine whether field input is valid.</span></span> <span data-ttu-id="749b6-393">Por exemplo, o aplicativo pode precisar verificar se um nome de usuário já está em uso.</span><span class="sxs-lookup"><span data-stu-id="749b6-393">For example, the app may need to verify whether a user name is already in use.</span></span>

<span data-ttu-id="749b6-394">Para implementar a validação remota:</span><span class="sxs-lookup"><span data-stu-id="749b6-394">To implement remote validation:</span></span>

1. <span data-ttu-id="749b6-395">Crie um método de ação para JavaScript para chamar.</span><span class="sxs-lookup"><span data-stu-id="749b6-395">Create an action method for JavaScript to call.</span></span>  <span data-ttu-id="749b6-396">O método [remoto](https://jqueryvalidation.org/remote-method/) do jQuery Validate espera uma resposta JSON:</span><span class="sxs-lookup"><span data-stu-id="749b6-396">The jQuery Validate [remote](https://jqueryvalidation.org/remote-method/) method expects a JSON response:</span></span>

   * <span data-ttu-id="749b6-397">`"true"` significa que os dados de entrada são válidos.</span><span class="sxs-lookup"><span data-stu-id="749b6-397">`"true"` means the input data is valid.</span></span>
   * <span data-ttu-id="749b6-398">`"false"`, `undefined` ou `null` significa que a entrada é inválida.</span><span class="sxs-lookup"><span data-stu-id="749b6-398">`"false"`, `undefined`, or `null` means the input is invalid.</span></span>  <span data-ttu-id="749b6-399">Exiba a mensagem de erro padrão.</span><span class="sxs-lookup"><span data-stu-id="749b6-399">Display the default error message.</span></span>
   * <span data-ttu-id="749b6-400">Qualquer outra cadeia de caracteres significa que a entrada é inválida.</span><span class="sxs-lookup"><span data-stu-id="749b6-400">Any other string means the input is invalid.</span></span> <span data-ttu-id="749b6-401">Exiba a cadeia de caracteres como uma mensagem de erro personalizada.</span><span class="sxs-lookup"><span data-stu-id="749b6-401">Display the string as a custom error message.</span></span>

   <span data-ttu-id="749b6-402">Aqui está um exemplo de um método de ação que retorna uma mensagem de erro personalizada:</span><span class="sxs-lookup"><span data-stu-id="749b6-402">Here's an example of an action method that returns a custom error message:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. <span data-ttu-id="749b6-403">Na classe de modelo, anote a propriedade com um atributo `[Remote]` que aponta para o método de ação de validação, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="749b6-403">In the model class, annotate the property with a `[Remote]` attribute that points to the validation action method, as shown in the following example:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Models/User.cs?name=snippet_UserEmailProperty)]
 
   <span data-ttu-id="749b6-404">O atributo `[Remote]` está no namespace `Microsoft.AspNetCore.Mvc`.</span><span class="sxs-lookup"><span data-stu-id="749b6-404">The `[Remote]` attribute is in the `Microsoft.AspNetCore.Mvc` namespace.</span></span> <span data-ttu-id="749b6-405">Instale o pacote NuGet [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) se você não estiver usando o metapacote `Microsoft.AspNetCore.App` ou `Microsoft.AspNetCore.All`.</span><span class="sxs-lookup"><span data-stu-id="749b6-405">Install the [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) NuGet package if you're not using the `Microsoft.AspNetCore.App` or `Microsoft.AspNetCore.All` metapackage.</span></span>
   
### <a name="additional-fields"></a><span data-ttu-id="749b6-406">Campos adicionais</span><span class="sxs-lookup"><span data-stu-id="749b6-406">Additional fields</span></span>

<span data-ttu-id="749b6-407">A propriedade `AdditionalFields` do atributo `[Remote]` permite validar combinações de campos em relação aos dados no servidor.</span><span class="sxs-lookup"><span data-stu-id="749b6-407">The `AdditionalFields` property of the `[Remote]` attribute lets you validate combinations of fields against data on the server.</span></span> <span data-ttu-id="749b6-408">Por exemplo, se o modelo `User` tinha as propriedades `FirstName` e `LastName`, pode ser interessante verificar se nenhum usuário existente já tem esse par de nomes.</span><span class="sxs-lookup"><span data-stu-id="749b6-408">For example, if the `User` model had `FirstName` and `LastName` properties, you might want to verify that no existing users already have that pair of names.</span></span> <span data-ttu-id="749b6-409">O exemplo a seguir mostra como usar `AdditionalFields`:</span><span class="sxs-lookup"><span data-stu-id="749b6-409">The following example shows how to use `AdditionalFields`:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/User.cs?name=snippet_UserNameProperties)]

<span data-ttu-id="749b6-410">`AdditionalFields` pode ser definido explicitamente para as cadeias de caracteres `"FirstName"` e `"LastName"`, mas usar o operador [nameof](/dotnet/csharp/language-reference/keywords/nameof) simplifica a refatoração posterior.</span><span class="sxs-lookup"><span data-stu-id="749b6-410">`AdditionalFields` could be set explicitly to the strings `"FirstName"` and `"LastName"`, but using the [nameof](/dotnet/csharp/language-reference/keywords/nameof) operator simplifies later refactoring.</span></span> <span data-ttu-id="749b6-411">O método de ação para essa validação deve aceitar os argumentos de primeiro nome e de sobrenome:</span><span class="sxs-lookup"><span data-stu-id="749b6-411">The action method for this validation must accept both first name and last name arguments:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyName)]

<span data-ttu-id="749b6-412">Quando o usuário insere o primeiro nome ou o sobrenome, o JavaScript faz uma chamada remota para ver se esse par de nomes foi usado.</span><span class="sxs-lookup"><span data-stu-id="749b6-412">When the user enters a first or last name, JavaScript makes a remote call to see if that pair of names has been taken.</span></span>

<span data-ttu-id="749b6-413">Para validar dois ou mais campos adicionais, forneça-os como uma lista delimitada por vírgulas.</span><span class="sxs-lookup"><span data-stu-id="749b6-413">To validate two or more additional fields, provide them as a comma-delimited list.</span></span> <span data-ttu-id="749b6-414">Por exemplo, para adicionar uma propriedade `MiddleName` ao modelo, defina o atributo `[Remote]`, conforme mostrado no seguinte exemplo:</span><span class="sxs-lookup"><span data-stu-id="749b6-414">For example, to add a `MiddleName` property to the model, set the `[Remote]` attribute as shown in the following example:</span></span>

```cs
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

<span data-ttu-id="749b6-415">`AdditionalFields`, como todos os argumentos de atributo, deve ser uma expressão de constante.</span><span class="sxs-lookup"><span data-stu-id="749b6-415">`AdditionalFields`, like all attribute arguments, must be a constant expression.</span></span> <span data-ttu-id="749b6-416">Portanto, não use uma [cadeia de caracteres interpolada](/dotnet/csharp/language-reference/keywords/interpolated-strings) nem chame <xref:System.String.Join*> para inicializar `AdditionalFields`.</span><span class="sxs-lookup"><span data-stu-id="749b6-416">Therefore, don't use an [interpolated string](/dotnet/csharp/language-reference/keywords/interpolated-strings) or call <xref:System.String.Join*> to initialize `AdditionalFields`.</span></span>

## <a name="alternatives-to-built-in-attributes"></a><span data-ttu-id="749b6-417">Alternativas aos atributos internos</span><span class="sxs-lookup"><span data-stu-id="749b6-417">Alternatives to built-in attributes</span></span>

<span data-ttu-id="749b6-418">Se precisar de validação não fornecida por atributos internos, você poderá:</span><span class="sxs-lookup"><span data-stu-id="749b6-418">If you need validation not provided by built-in attributes, you can:</span></span>

* <span data-ttu-id="749b6-419">[Criar atributos personalizados](#custom-attributes).</span><span class="sxs-lookup"><span data-stu-id="749b6-419">[Create custom attributes](#custom-attributes).</span></span>
* <span data-ttu-id="749b6-420">[Implementar IValidatableObject](#ivalidatableobject).</span><span class="sxs-lookup"><span data-stu-id="749b6-420">[Implement IValidatableObject](#ivalidatableobject).</span></span>

## <a name="custom-attributes"></a><span data-ttu-id="749b6-421">Atributos personalizados</span><span class="sxs-lookup"><span data-stu-id="749b6-421">Custom attributes</span></span>

<span data-ttu-id="749b6-422">Para cenários não compatíveis com os atributos de validação internos, você pode criar atributos de validação personalizados.</span><span class="sxs-lookup"><span data-stu-id="749b6-422">For scenarios that the built-in validation attributes don't handle, you can create custom validation attributes.</span></span> <span data-ttu-id="749b6-423">Crie uma classe que herda de <xref:System.ComponentModel.DataAnnotations.ValidationAttribute> e substitua o método <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*>.</span><span class="sxs-lookup"><span data-stu-id="749b6-423">Create a class that inherits from <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>, and override the <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> method.</span></span>

<span data-ttu-id="749b6-424">O método `IsValid` aceita um objeto chamado *valor*, que é a entrada a ser validada.</span><span class="sxs-lookup"><span data-stu-id="749b6-424">The `IsValid` method accepts an object named *value*, which is the input to be validated.</span></span> <span data-ttu-id="749b6-425">Uma sobrecarga também aceita um objeto `ValidationContext`, que fornece informações adicionais, como a instância do modelo criada pelo model binding.</span><span class="sxs-lookup"><span data-stu-id="749b6-425">An overload also accepts a `ValidationContext` object, which provides additional information, such as the model instance created by model binding.</span></span>

<span data-ttu-id="749b6-426">O exemplo a seguir valida se a data de lançamento de um filme no gênero *Clássico* não é posterior a um ano especificado.</span><span class="sxs-lookup"><span data-stu-id="749b6-426">The following example validates that the release date for a movie in the *Classic* genre isn't later than a specified year.</span></span> <span data-ttu-id="749b6-427">O atributo `[ClassicMovie2]` verificará o gênero primeiro e continuará apenas se for *Clássico*.</span><span class="sxs-lookup"><span data-stu-id="749b6-427">The `[ClassicMovie2]` attribute checks the genre first and continues only if it's *Classic*.</span></span> <span data-ttu-id="749b6-428">Para filmes identificados como clássicos, ele verifica a data de lançamento para garantir que não seja posterior ao limite passado ao construtor de atributo).</span><span class="sxs-lookup"><span data-stu-id="749b6-428">For movies identified as classics, it checks the release date to make sure it's not later than the limit passed to the attribute constructor.)</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovieAttribute.cs?name=snippet_ClassicMovieAttribute)]

<span data-ttu-id="749b6-429">A variável `movie` no exemplo anterior representa um objeto `Movie` que contém os dados do envio do formulário.</span><span class="sxs-lookup"><span data-stu-id="749b6-429">The `movie` variable in the preceding example represents a `Movie` object that contains the data from the form submission.</span></span> <span data-ttu-id="749b6-430">O método `IsValid` verifica a data e o gênero.</span><span class="sxs-lookup"><span data-stu-id="749b6-430">The `IsValid` method checks the date and genre.</span></span> <span data-ttu-id="749b6-431">Após a validação bem-sucedida, o `IsValid` retorna um código `ValidationResult.Success`.</span><span class="sxs-lookup"><span data-stu-id="749b6-431">Upon successful validation, `IsValid` returns a `ValidationResult.Success` code.</span></span> <span data-ttu-id="749b6-432">Quando a validação falha, um `ValidationResult` com uma mensagem erro será retornado.</span><span class="sxs-lookup"><span data-stu-id="749b6-432">When validation fails, a `ValidationResult` with an error message is returned.</span></span>

## <a name="ivalidatableobject"></a><span data-ttu-id="749b6-433">IValidatableObject</span><span class="sxs-lookup"><span data-stu-id="749b6-433">IValidatableObject</span></span>

<span data-ttu-id="749b6-434">O exemplo anterior funciona apenas com tipos `Movie`.</span><span class="sxs-lookup"><span data-stu-id="749b6-434">The preceding example works only with `Movie` types.</span></span> <span data-ttu-id="749b6-435">Outra opção para validação de nível de classe é implementar `IValidatableObject` na classe de modelo, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="749b6-435">Another option for class-level validation is to implement `IValidatableObject` in the model class, as shown in the following example:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/MovieIValidatable.cs?name=snippet&highlight=1,26-34)]

## <a name="top-level-node-validation"></a><span data-ttu-id="749b6-436">Validação de nó de nível superior</span><span class="sxs-lookup"><span data-stu-id="749b6-436">Top-level node validation</span></span>

<span data-ttu-id="749b6-437">Os nós de nível superior incluem:</span><span class="sxs-lookup"><span data-stu-id="749b6-437">Top-level nodes include:</span></span>

* <span data-ttu-id="749b6-438">Parâmetros de ação</span><span class="sxs-lookup"><span data-stu-id="749b6-438">Action parameters</span></span>
* <span data-ttu-id="749b6-439">Propriedades do controlador</span><span class="sxs-lookup"><span data-stu-id="749b6-439">Controller properties</span></span>
* <span data-ttu-id="749b6-440">Parâmetros do manipulador de página</span><span class="sxs-lookup"><span data-stu-id="749b6-440">Page handler parameters</span></span>
* <span data-ttu-id="749b6-441">Propriedades do modelo de página</span><span class="sxs-lookup"><span data-stu-id="749b6-441">Page model properties</span></span>

<span data-ttu-id="749b6-442">Os nós de nível superior associados ao modelo são validados além de validar as propriedades do modelo.</span><span class="sxs-lookup"><span data-stu-id="749b6-442">Model-bound top-level nodes are validated in addition to validating model properties.</span></span> <span data-ttu-id="749b6-443">No exemplo a seguir do aplicativo de exemplo, o método `VerifyPhone` usa o <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> para validar o parâmetro de ação `phone`:</span><span class="sxs-lookup"><span data-stu-id="749b6-443">In the following example from the sample app, the `VerifyPhone` method uses the <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> to validate the `phone` action parameter:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

<span data-ttu-id="749b6-444">Os nós de nível superior podem usar <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> com atributos de validação.</span><span class="sxs-lookup"><span data-stu-id="749b6-444">Top-level nodes can use <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> with validation attributes.</span></span> <span data-ttu-id="749b6-445">No exemplo a seguir do aplicativo de exemplo, o método `CheckAge` especifica que o parâmetro `age` deve ser associado da cadeia de caracteres de consulta quando o formulário é enviado:</span><span class="sxs-lookup"><span data-stu-id="749b6-445">In the following example from the sample app, the `CheckAge` method specifies that the `age` parameter must be bound from the query string when the form is submitted:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_CheckAge)]

<span data-ttu-id="749b6-446">Na página Verificar Idade (*CheckAge.cshtml*), há dois formulários.</span><span class="sxs-lookup"><span data-stu-id="749b6-446">In the Check Age page (*CheckAge.cshtml*), there are two forms.</span></span> <span data-ttu-id="749b6-447">O primeiro formulário envia um valor `Age` de `99` como uma cadeia de caracteres de consulta: `https://localhost:5001/Users/CheckAge?Age=99`.</span><span class="sxs-lookup"><span data-stu-id="749b6-447">The first form submits an `Age` value of `99` as a query string: `https://localhost:5001/Users/CheckAge?Age=99`.</span></span>

<span data-ttu-id="749b6-448">Quando um parâmetro `age` formatado corretamente da cadeia de caracteres de consulta é enviado, o formulário é validado.</span><span class="sxs-lookup"><span data-stu-id="749b6-448">When a properly formatted `age` parameter from the query string is submitted, the form validates.</span></span>

<span data-ttu-id="749b6-449">O segundo formulário na página Verificar Idade envia o valor `Age` no corpo da solicitação e a validação falha.</span><span class="sxs-lookup"><span data-stu-id="749b6-449">The second form on the Check Age page submits the `Age` value in the body of the request, and validation fails.</span></span> <span data-ttu-id="749b6-450">A associação falha porque o parâmetro `age` deve vir de uma cadeia de caracteres de consulta.</span><span class="sxs-lookup"><span data-stu-id="749b6-450">Binding fails because the `age` parameter must come from a query string.</span></span>

<span data-ttu-id="749b6-451">Ao executar com `CompatibilityVersion.Version_2_1` ou posterior, a validação do nó de nível superior é habilitada por padrão.</span><span class="sxs-lookup"><span data-stu-id="749b6-451">When running with `CompatibilityVersion.Version_2_1` or later, top-level node validation is enabled by default.</span></span> <span data-ttu-id="749b6-452">Caso contrário, a validação do nó de nível superior ficará desabilitada.</span><span class="sxs-lookup"><span data-stu-id="749b6-452">Otherwise, top-level node validation is disabled.</span></span> <span data-ttu-id="749b6-453">A opção padrão pode ser substituída pela configuração da propriedade <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> em (`Startup.ConfigureServices`), conforme mostrado aqui:</span><span class="sxs-lookup"><span data-stu-id="749b6-453">The default option can be overridden by setting the <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> property in (`Startup.ConfigureServices`), as shown here:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Startup.cs?name=snippet_AddMvc&highlight=4)]

## <a name="maximum-errors"></a><span data-ttu-id="749b6-454">Máximo de erros</span><span class="sxs-lookup"><span data-stu-id="749b6-454">Maximum errors</span></span>

<span data-ttu-id="749b6-455">A validação para quando o número máximo de erros é atingido (200 por padrão).</span><span class="sxs-lookup"><span data-stu-id="749b6-455">Validation stops when the maximum number of errors is reached (200 by default).</span></span> <span data-ttu-id="749b6-456">Você pode configurar esse número com o seguinte código no `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="749b6-456">You can configure this number with the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=3)]

## <a name="maximum-recursion"></a><span data-ttu-id="749b6-457">Recursão máxima</span><span class="sxs-lookup"><span data-stu-id="749b6-457">Maximum recursion</span></span>

<span data-ttu-id="749b6-458"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> percorre o grafo de objeto do modelo que está sendo validado.</span><span class="sxs-lookup"><span data-stu-id="749b6-458"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> traverses the object graph of the model being validated.</span></span> <span data-ttu-id="749b6-459">Para modelos que são muito profundos ou que são infinitamente recursivos, a validação pode resultar em estouro de pilha.</span><span class="sxs-lookup"><span data-stu-id="749b6-459">For models that are very deep or are infinitely recursive, validation may result in stack overflow.</span></span> <span data-ttu-id="749b6-460">O [MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) fornece uma maneira de interromper a validação antes que a recursão visitante exceda uma profundidade configurada.</span><span class="sxs-lookup"><span data-stu-id="749b6-460">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) provides a way to stop validation early if the visitor recursion exceeds a configured depth.</span></span> <span data-ttu-id="749b6-461">O valor padrão de `MvcOptions.MaxValidationDepth` é 32 quando executado com `CompatibilityVersion.Version_2_2` ou posterior.</span><span class="sxs-lookup"><span data-stu-id="749b6-461">The default value of `MvcOptions.MaxValidationDepth` is 32 when running with `CompatibilityVersion.Version_2_2` or later.</span></span> <span data-ttu-id="749b6-462">Para versões anteriores, o valor é nulo, o que significa que não há nenhuma restrição de profundidade.</span><span class="sxs-lookup"><span data-stu-id="749b6-462">For earlier versions, the value is null, which means no depth constraint.</span></span>

## <a name="automatic-short-circuit"></a><span data-ttu-id="749b6-463">Curto-circuito automático</span><span class="sxs-lookup"><span data-stu-id="749b6-463">Automatic short-circuit</span></span>

<span data-ttu-id="749b6-464">A validação sofrerá curto-circuito (será ignorada) automaticamente se o grafo do modelo não exigir validação.</span><span class="sxs-lookup"><span data-stu-id="749b6-464">Validation is automatically short-circuited (skipped) if the model graph doesn't require validation.</span></span> <span data-ttu-id="749b6-465">Objetos em que o runtime ignora a validação para inclusão de coleções de primitivos (como `byte[]`, `string[]` e `Dictionary<string, string>`) e grafos de objetos complexos que não têm um validador.</span><span class="sxs-lookup"><span data-stu-id="749b6-465">Objects that the runtime skips validation for include collections of primitives (such as `byte[]`, `string[]`, `Dictionary<string, string>`) and complex object graphs that don't have any validators.</span></span>

## <a name="disable-validation"></a><span data-ttu-id="749b6-466">Desabilitar validação</span><span class="sxs-lookup"><span data-stu-id="749b6-466">Disable validation</span></span>

<span data-ttu-id="749b6-467">Para desabilitar a validação:</span><span class="sxs-lookup"><span data-stu-id="749b6-467">To disable validation:</span></span>

1. <span data-ttu-id="749b6-468">Crie uma implementação de `IObjectModelValidator` que não marque nenhum campo como inválido.</span><span class="sxs-lookup"><span data-stu-id="749b6-468">Create an implementation of `IObjectModelValidator` that doesn't mark any fields as invalid.</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/NullObjectModelValidator.cs?name=snippet_DisableValidation)]

1. <span data-ttu-id="749b6-469">Adicione o seguinte código ao `Startup.ConfigureServices` para substituir a implementação `IObjectModelValidator` padrão no contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="749b6-469">Add the following code to `Startup.ConfigureServices` to replace the default `IObjectModelValidator` implementation in the dependency injection container.</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_DisableValidation)]

<span data-ttu-id="749b6-470">Talvez você ainda veja erros de estado de modelo que se originam do model binding.</span><span class="sxs-lookup"><span data-stu-id="749b6-470">You might still see model state errors that originate from model binding.</span></span>

## <a name="client-side-validation"></a><span data-ttu-id="749b6-471">Validação do lado do cliente</span><span class="sxs-lookup"><span data-stu-id="749b6-471">Client-side validation</span></span>

<span data-ttu-id="749b6-472">A validação do lado do cliente impede o envio até que o formulário seja válido.</span><span class="sxs-lookup"><span data-stu-id="749b6-472">Client-side validation prevents submission until the form is valid.</span></span> <span data-ttu-id="749b6-473">O botão Enviar executa o JavaScript que envia o formulário ou exibe mensagens de erro.</span><span class="sxs-lookup"><span data-stu-id="749b6-473">The Submit button runs JavaScript that either submits the form or displays error messages.</span></span>

<span data-ttu-id="749b6-474">A validação do lado do cliente evita uma viagem de ida e volta desnecessária ao servidor quando há erros de entrada em um formulário.</span><span class="sxs-lookup"><span data-stu-id="749b6-474">Client-side validation avoids an unnecessary round trip to the server when there are input errors on a form.</span></span> <span data-ttu-id="749b6-475">O script a seguir faz referência nas validações de suporte *_Layout.cshtml* e *_ValidationScriptsPartial.cshtml* no lado do cliente:</span><span class="sxs-lookup"><span data-stu-id="749b6-475">The following script references in *_Layout.cshtml* and *_ValidationScriptsPartial.cshtml* support client-side validation:</span></span>

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Shared/_Layout.cshtml?name=snippet_ScriptTag)]

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_ScriptTags)]

<span data-ttu-id="749b6-476">O script do [jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive) é uma biblioteca de front-end personalizada da Microsoft que se baseia no popular plug-in [jQuery Validate](https://jqueryvalidation.org/).</span><span class="sxs-lookup"><span data-stu-id="749b6-476">The [jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive) script is a custom Microsoft front-end library that builds on the popular [jQuery Validate](https://jqueryvalidation.org/) plugin.</span></span> <span data-ttu-id="749b6-477">Sem o jQuery Unobtrusive Validation, você teria que codificar a mesma lógica de validação em dois locais: uma vez nos atributos de validação do lado do servidor nas propriedades do modelo e, em seguida, novamente nos scripts do lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="749b6-477">Without jQuery Unobtrusive Validation, you would have to code the same validation logic in two places: once in the server-side validation attributes on model properties, and then again in client-side scripts.</span></span> <span data-ttu-id="749b6-478">Em vez disso, os [Auxiliares de Marca](xref:mvc/views/tag-helpers/intro) e os [Auxiliares HTML](xref:mvc/views/overview) usam os atributos de validação e os metadados de tipo das propriedades do modelo para renderizar atributos `data-` de HTML 5 para os elementos de formulário que precisam de validação.</span><span class="sxs-lookup"><span data-stu-id="749b6-478">Instead, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use the validation attributes and type metadata from model properties to render HTML 5 `data-` attributes for the form elements that need validation.</span></span> <span data-ttu-id="749b6-479">O jQuery Unobtrusive Validation analisa os atributos `data-` e passa a lógica para o jQuery Validate, "copiando" efetivamente a lógica de validação do lado do servidor para o cliente.</span><span class="sxs-lookup"><span data-stu-id="749b6-479">jQuery Unobtrusive Validation parses the `data-` attributes and passes the logic to jQuery Validate, effectively "copying" the server-side validation logic to the client.</span></span> <span data-ttu-id="749b6-480">Você pode exibir erros de validação no cliente usando os auxiliares de marca conforme mostrado aqui:</span><span class="sxs-lookup"><span data-stu-id="749b6-480">You can display validation errors on the client using tag helpers as shown here:</span></span>

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=4-5)]

<span data-ttu-id="749b6-481">Os auxiliares de marca acima renderizam o HTML a seguir.</span><span class="sxs-lookup"><span data-stu-id="749b6-481">The preceding tag helpers render the following HTML.</span></span>

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

<span data-ttu-id="749b6-482">Observe que os atributos `data-` na saída HTML correspondem aos atributos de validação da propriedade `ReleaseDate`.</span><span class="sxs-lookup"><span data-stu-id="749b6-482">Notice that the `data-` attributes in the HTML output correspond to the validation attributes for the `ReleaseDate` property.</span></span> <span data-ttu-id="749b6-483">O atributo `data-val-required` conterá uma mensagem de erro a ser exibida se o usuário não preencher o campo de data de lançamento.</span><span class="sxs-lookup"><span data-stu-id="749b6-483">The `data-val-required` attribute contains an error message to display if the user doesn't fill in the release date field.</span></span> <span data-ttu-id="749b6-484">a validação não invasiva do jQuery passa esse valor para o método de validação do jQuery [requerido ()](https://jqueryvalidation.org/required-method/) , que, em seguida, exibe essa mensagem no elemento **> do\<span** em acompanhamento.</span><span class="sxs-lookup"><span data-stu-id="749b6-484">jQuery Unobtrusive Validation passes this value to the jQuery Validate [required()](https://jqueryvalidation.org/required-method/) method, which then displays that message in the accompanying **\<span>** element.</span></span>

<span data-ttu-id="749b6-485">A validação de tipo de dados é baseada no tipo .NET de uma propriedade, a menos que seja substituída por um atributo `[DataType]`.</span><span class="sxs-lookup"><span data-stu-id="749b6-485">Data type validation is based on the .NET type of a property, unless that is overridden by a `[DataType]` attribute.</span></span> <span data-ttu-id="749b6-486">Os navegadores têm suas próprias mensagens de erro padrão, mas o pacote de validação do jQuery Validation Unobtrusive pode substituir essas mensagens.</span><span class="sxs-lookup"><span data-stu-id="749b6-486">Browsers have their own default error messages, but the jQuery Validation Unobtrusive Validation package can override those messages.</span></span> <span data-ttu-id="749b6-487">Os atributos `[DataType]` e as subclasses como `[EmailAddress]` permitem que você especifique a mensagem de erro.</span><span class="sxs-lookup"><span data-stu-id="749b6-487">`[DataType]` attributes and subclasses such as `[EmailAddress]` let you specify the error message.</span></span>

### <a name="add-validation-to-dynamic-forms"></a><span data-ttu-id="749b6-488">Adicionar validação a formulários dinâmicos</span><span class="sxs-lookup"><span data-stu-id="749b6-488">Add Validation to Dynamic Forms</span></span>

<span data-ttu-id="749b6-489">O jQuery Unobtrusive Validation passa parâmetros e a lógica de validação para o jQuery Validate quando a página é carregada pela primeira vez.</span><span class="sxs-lookup"><span data-stu-id="749b6-489">jQuery Unobtrusive Validation passes validation logic and parameters to jQuery Validate when the page first loads.</span></span> <span data-ttu-id="749b6-490">Portanto, a validação não funciona automaticamente em formulários gerados dinamicamente.</span><span class="sxs-lookup"><span data-stu-id="749b6-490">Therefore, validation doesn't work automatically on dynamically generated forms.</span></span> <span data-ttu-id="749b6-491">Para habilitar a validação é necessário instruir o jQuery Unobtrusive Validation a analisar o formulário dinâmico imediatamente depois de criá-lo.</span><span class="sxs-lookup"><span data-stu-id="749b6-491">To enable validation, tell jQuery Unobtrusive Validation to parse the dynamic form immediately after you create it.</span></span> <span data-ttu-id="749b6-492">Por exemplo, o código a seguir define a validação do lado do cliente em um formulário adicionado por meio do AJAX.</span><span class="sxs-lookup"><span data-stu-id="749b6-492">For example, the following code sets up client-side validation on a form added via AJAX.</span></span>

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

<span data-ttu-id="749b6-493">O método `$.validator.unobtrusive.parse()` aceita um seletor do jQuery para um argumento seu.</span><span class="sxs-lookup"><span data-stu-id="749b6-493">The `$.validator.unobtrusive.parse()` method accepts a jQuery selector for its one argument.</span></span> <span data-ttu-id="749b6-494">Esse método instrui o jQuery Unobtrusive Validation a analisar os atributos `data-` de formulários nesse seletor.</span><span class="sxs-lookup"><span data-stu-id="749b6-494">This method tells jQuery Unobtrusive Validation to parse the `data-` attributes of forms within that selector.</span></span> <span data-ttu-id="749b6-495">Depois, os valores desses atributos são passados para o plug-in do jQuery Validate.</span><span class="sxs-lookup"><span data-stu-id="749b6-495">The values of those attributes are then passed to the jQuery Validate plugin.</span></span>

### <a name="add-validation-to-dynamic-controls"></a><span data-ttu-id="749b6-496">Adicionar validação a controles dinâmicos</span><span class="sxs-lookup"><span data-stu-id="749b6-496">Add Validation to Dynamic Controls</span></span>

<span data-ttu-id="749b6-497">O método `$.validator.unobtrusive.parse()` funciona em um formulário inteiro, não em controles individuais gerados dinamicamente, como `<input>` e `<select/>`.</span><span class="sxs-lookup"><span data-stu-id="749b6-497">The `$.validator.unobtrusive.parse()` method works on an entire form, not on individual dynamically generated controls, such as `<input>` and `<select/>`.</span></span> <span data-ttu-id="749b6-498">Para uma nova análise do formulário, remova os dados de validação que foram adicionados ao formulário mesmo quando foi analisado anteriormente, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="749b6-498">To reparse the form, remove the validation data that was added when the form was parsed earlier, as shown in the following example:</span></span>

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

## <a name="custom-client-side-validation"></a><span data-ttu-id="749b6-499">Validação personalizada do lado do cliente</span><span class="sxs-lookup"><span data-stu-id="749b6-499">Custom client-side validation</span></span>

<span data-ttu-id="749b6-500">A validação personalizada do lado do cliente é feita por meio da geração de atributos de HTML `data-` que funcionam com um adaptador do jQuery Validate personalizado.</span><span class="sxs-lookup"><span data-stu-id="749b6-500">Custom client-side validation is done by generating `data-` HTML attributes that work with a custom jQuery Validate adapter.</span></span> <span data-ttu-id="749b6-501">O seguinte código do adaptador de exemplo foi escrito para os atributos `ClassicMovie` e `ClassicMovie2` que foram apresentados no início deste artigo:</span><span class="sxs-lookup"><span data-stu-id="749b6-501">The following sample adapter code was written for the `ClassicMovie` and `ClassicMovie2` attributes that were introduced earlier in this article:</span></span>

[!code-javascript[](validation/samples/2.x/ValidationSample/wwwroot/js/classicMovieValidator.js?name=snippet_UnobtrusiveValidation)]

<span data-ttu-id="749b6-502">Para obter informações sobre como escrever adaptadores, consulte a [documentação do jQuery Validate](https://jqueryvalidation.org/documentation/).</span><span class="sxs-lookup"><span data-stu-id="749b6-502">For information about how to write adapters, see the [jQuery Validate documentation](https://jqueryvalidation.org/documentation/).</span></span>

<span data-ttu-id="749b6-503">O uso de um adaptador para um determinado campo é disparado por atributos `data-` que:</span><span class="sxs-lookup"><span data-stu-id="749b6-503">The use of an adapter for a given field is triggered by `data-` attributes that:</span></span>

* <span data-ttu-id="749b6-504">Marcam o campo como sujeito à validação do sinalizador (`data-val="true"`).</span><span class="sxs-lookup"><span data-stu-id="749b6-504">Flag the field as being subject to validation (`data-val="true"`).</span></span>
* <span data-ttu-id="749b6-505">Identifique o nome de uma regra de validação e o texto da mensagem de erro (por exemplo, `data-val-rulename="Error message."`).</span><span class="sxs-lookup"><span data-stu-id="749b6-505">Identify a validation rule name and error message text (for example, `data-val-rulename="Error message."`).</span></span>
* <span data-ttu-id="749b6-506">Forneça os parâmetros adicionais que o validador precisa (por exemplo, `data-val-rulename-parm1="value"`).</span><span class="sxs-lookup"><span data-stu-id="749b6-506">Provide any additional parameters the validator needs (for example, `data-val-rulename-parm1="value"`).</span></span>

<span data-ttu-id="749b6-507">A exemplo a seguir mostra os atributos `data-` para o atributo `ClassicMovie` do aplicativo de exemplo:</span><span class="sxs-lookup"><span data-stu-id="749b6-507">The following example shows the `data-` attributes for the sample app's `ClassicMovie` attribute:</span></span>

```html
<input class="form-control" type="datetime"
    data-val="true"
    data-val-classicmovie1="Classic movies must have a release year earlier than 1960."
    data-val-classicmovie1-year="1960"
    data-val-required="The ReleaseDate field is required."
    id="ReleaseDate" name="ReleaseDate" value="">
```

<span data-ttu-id="749b6-508">Conforme observado anteriormente, os [Auxiliares de Marca](xref:mvc/views/tag-helpers/intro) e [Auxiliares de HTML](xref:mvc/views/overview) usam informações de atributos de validação para renderizar atributos `data-`.</span><span class="sxs-lookup"><span data-stu-id="749b6-508">As noted earlier, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use information from validation attributes to render `data-` attributes.</span></span> <span data-ttu-id="749b6-509">Há duas opções para escrever código que resulte na criação de atributos HTML `data-` personalizados:</span><span class="sxs-lookup"><span data-stu-id="749b6-509">There are two options for writing code that results in the creation of custom `data-` HTML attributes:</span></span>

* <span data-ttu-id="749b6-510">Criar uma classe que deriva de `AttributeAdapterBase<TAttribute>` e uma classe que implementa `IValidationAttributeAdapterProvider` e registrar o atributo e o adaptador na DI.</span><span class="sxs-lookup"><span data-stu-id="749b6-510">Create a class that derives from `AttributeAdapterBase<TAttribute>` and a class that implements `IValidationAttributeAdapterProvider`, and register your attribute and its adapter in DI.</span></span> <span data-ttu-id="749b6-511">Este método segue a [entidade de segurança de responsabilidade única](https://wikipedia.org/wiki/Single_responsibility_principle) em que o código de validação relacionado ao cliente e ao servidor fica em classes separadas.</span><span class="sxs-lookup"><span data-stu-id="749b6-511">This method follows the [single responsibility principal](https://wikipedia.org/wiki/Single_responsibility_principle) in that server-related and client-related validation code is in separate classes.</span></span> <span data-ttu-id="749b6-512">O adaptador também tem a vantagem de que, uma vez que ele é registrado na DI, outros serviços da DI ficam disponíveis para ele, se necessário.</span><span class="sxs-lookup"><span data-stu-id="749b6-512">The adapter also has the advantage that since it is registered in DI, other services in DI are available to it if needed.</span></span>
* <span data-ttu-id="749b6-513">Implementar `IClientModelValidator` em na classe `ValidationAttribute`.</span><span class="sxs-lookup"><span data-stu-id="749b6-513">Implement `IClientModelValidator` in your `ValidationAttribute` class.</span></span> <span data-ttu-id="749b6-514">Esse método poderá ser adequado se o atributo não fizer nenhuma validação do lado do servidor e não precisar de um serviço da DI.</span><span class="sxs-lookup"><span data-stu-id="749b6-514">This method might be appropriate if the attribute doesn't do any server-side validation and doesn't need any services from DI.</span></span>

### <a name="attributeadapter-for-client-side-validation"></a><span data-ttu-id="749b6-515">AttributeAdapter para validação do lado do cliente</span><span class="sxs-lookup"><span data-stu-id="749b6-515">AttributeAdapter for client-side validation</span></span>

<span data-ttu-id="749b6-516">Esse método de renderização de atributos `data-` em HTML é usado pelo atributo `ClassicMovie` no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="749b6-516">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie` attribute in the sample app.</span></span> <span data-ttu-id="749b6-517">Para adicionar a validação do cliente usando esse método:</span><span class="sxs-lookup"><span data-stu-id="749b6-517">To add client validation by using this method:</span></span>

1. <span data-ttu-id="749b6-518">Crie uma classe de adaptador de atributo para o atributo de validação personalizado.</span><span class="sxs-lookup"><span data-stu-id="749b6-518">Create an attribute adapter class for the custom validation attribute.</span></span> <span data-ttu-id="749b6-519">Derive a classe de [AttributeAdapterBase\<T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="749b6-519">Derive the class from [AttributeAdapterBase\<T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span></span> <span data-ttu-id="749b6-520">Criar um método `AddValidation` que adiciona atributos `data-` à saída renderizada, conforme mostrado neste exemplo:</span><span class="sxs-lookup"><span data-stu-id="749b6-520">Create an `AddValidation` method that adds `data-` attributes to the rendered output, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovieAttributeAdapter.cs?name=snippet_ClassicMovieAttributeAdapter)]

1. <span data-ttu-id="749b6-521">Crie uma classe de provedor de adaptador que implementa <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span><span class="sxs-lookup"><span data-stu-id="749b6-521">Create an adapter provider class that implements <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span></span> <span data-ttu-id="749b6-522">No método `GetAttributeAdapter`, passe o atributo personalizado para o construtor do adaptador, conforme mostrado neste exemplo:</span><span class="sxs-lookup"><span data-stu-id="749b6-522">In the `GetAttributeAdapter` method pass in the custom attribute to the adapter's constructor, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/CustomValidationAttributeAdapterProvider.cs?name=snippet_CustomValidationAttributeAdapterProvider)]

1. <span data-ttu-id="749b6-523">Registre o provedor de adaptador para DI em `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="749b6-523">Register the adapter provider for DI in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=8-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a><span data-ttu-id="749b6-524">IClientModelValidator para validação do lado do cliente</span><span class="sxs-lookup"><span data-stu-id="749b6-524">IClientModelValidator for client-side validation</span></span>

<span data-ttu-id="749b6-525">Esse método de renderização de atributos `data-` em HTML é usado pelo atributo `ClassicMovie2` no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="749b6-525">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie2` attribute in the sample app.</span></span> <span data-ttu-id="749b6-526">Para adicionar a validação do cliente usando esse método:</span><span class="sxs-lookup"><span data-stu-id="749b6-526">To add client validation by using this method:</span></span>

* <span data-ttu-id="749b6-527">No atributo de validação personalizado, implemente a interface `IClientModelValidator` e crie um método `AddValidation`.</span><span class="sxs-lookup"><span data-stu-id="749b6-527">In the custom validation attribute, implement the `IClientModelValidator` interface and create an `AddValidation` method.</span></span> <span data-ttu-id="749b6-528">No método `AddValidation`, adicione atributos `data-` para validação, conforme mostrado no exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="749b6-528">In the `AddValidation` method, add `data-` attributes for validation, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovie2Attribute.cs?name=snippet_ClassicMovie2Attribute)]

## <a name="disable-client-side-validation"></a><span data-ttu-id="749b6-529">Desabilitar validação do lado do cliente</span><span class="sxs-lookup"><span data-stu-id="749b6-529">Disable client-side validation</span></span>

<span data-ttu-id="749b6-530">O código a seguir desabilita a validação de cliente nas exibições do MVC:</span><span class="sxs-lookup"><span data-stu-id="749b6-530">The following code disables client validation in MVC views:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Startup2.cs?name=snippet_DisableClientValidation)]

<span data-ttu-id="749b6-531">E em Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="749b6-531">And in Razor Pages:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Startup3.cs?name=snippet_DisableClientValidation)]

<span data-ttu-id="749b6-532">Outra opção para desabilitar a validação do cliente é comentar a referência ao `_ValidationScriptsPartial` em seu arquivo *.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="749b6-532">Another option for disabling client validation is to comment out the reference to `_ValidationScriptsPartial` in your *.cshtml* file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="749b6-533">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="749b6-533">Additional resources</span></span>

* [<span data-ttu-id="749b6-534">Namespace System.ComponentModel.DataAnnotations</span><span class="sxs-lookup"><span data-stu-id="749b6-534">System.ComponentModel.DataAnnotations namespace</span></span>](xref:System.ComponentModel.DataAnnotations)
* [<span data-ttu-id="749b6-535">Model binding</span><span class="sxs-lookup"><span data-stu-id="749b6-535">Model Binding</span></span>](model-binding.md)

::: moniker-end
