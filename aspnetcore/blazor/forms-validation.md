---
title: ASP.NET Core Blazor formulários e validação
author: guardrex
description: Saiba como usar formulários e cenários de validação de campo no Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
uid: blazor/forms-validation
ms.openlocfilehash: f4c1845ee4b6ff9274b7117167367ccdd9f36c12
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74943687"
---
# <a name="aspnet-core-opno-locblazor-forms-and-validation"></a><span data-ttu-id="70ae3-103">ASP.NET Core Blazor formulários e validação</span><span class="sxs-lookup"><span data-stu-id="70ae3-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="70ae3-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="70ae3-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="70ae3-105">Há suporte para formulários e validação em Blazor usando [anotações de dados](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="70ae3-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="70ae3-106">O tipo de `ExampleModel` a seguir define a lógica de validação usando anotações de dados:</span><span class="sxs-lookup"><span data-stu-id="70ae3-106">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="70ae3-107">Um formulário é definido usando o componente `EditForm`.</span><span class="sxs-lookup"><span data-stu-id="70ae3-107">A form is defined using the `EditForm` component.</span></span> <span data-ttu-id="70ae3-108">O formulário a seguir demonstra elementos típicos, componentes e código do Razor:</span><span class="sxs-lookup"><span data-stu-id="70ae3-108">The following form demonstrates typical elements, components, and Razor code:</span></span>

```csharp
<EditForm Model="@exampleModel" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

* <span data-ttu-id="70ae3-109">O formulário valida a entrada do usuário no campo `name` usando a validação definida no tipo de `ExampleModel`.</span><span class="sxs-lookup"><span data-stu-id="70ae3-109">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="70ae3-110">O modelo é criado no bloco de `@code` do componente e mantido em um campo privado (`exampleModel`).</span><span class="sxs-lookup"><span data-stu-id="70ae3-110">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="70ae3-111">O campo é atribuído ao atributo `Model` do elemento `<EditForm>`.</span><span class="sxs-lookup"><span data-stu-id="70ae3-111">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="70ae3-112">O componente `DataAnnotationsValidator` anexa o suporte à validação usando anotações de dados.</span><span class="sxs-lookup"><span data-stu-id="70ae3-112">The `DataAnnotationsValidator` component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="70ae3-113">O componente `ValidationSummary` resume as mensagens de validação.</span><span class="sxs-lookup"><span data-stu-id="70ae3-113">The `ValidationSummary` component summarizes validation messages.</span></span>
* <span data-ttu-id="70ae3-114">`HandleValidSubmit` é disparado quando o formulário é enviado com êxito (aprovado na validação).</span><span class="sxs-lookup"><span data-stu-id="70ae3-114">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="70ae3-115">Um conjunto de componentes de entrada internos está disponível para receber e validar a entrada do usuário.</span><span class="sxs-lookup"><span data-stu-id="70ae3-115">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="70ae3-116">As entradas são validadas quando são alteradas e quando um formulário é enviado.</span><span class="sxs-lookup"><span data-stu-id="70ae3-116">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="70ae3-117">Os componentes de entrada disponíveis são mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="70ae3-117">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="70ae3-118">Componente de entrada</span><span class="sxs-lookup"><span data-stu-id="70ae3-118">Input component</span></span> | <span data-ttu-id="70ae3-119">Renderizado como&hellip;</span><span class="sxs-lookup"><span data-stu-id="70ae3-119">Rendered as&hellip;</span></span>       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

<span data-ttu-id="70ae3-120">Todos os componentes de entrada, incluindo `EditForm`, dão suporte a atributos arbitrários.</span><span class="sxs-lookup"><span data-stu-id="70ae3-120">All of the input components, including `EditForm`, support arbitrary attributes.</span></span> <span data-ttu-id="70ae3-121">Qualquer atributo que não corresponda a um parâmetro de componente é adicionado ao elemento HTML renderizado.</span><span class="sxs-lookup"><span data-stu-id="70ae3-121">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="70ae3-122">Os componentes de entrada fornecem o comportamento padrão para validar ao editar e alterar sua classe CSS para refletir o estado do campo.</span><span class="sxs-lookup"><span data-stu-id="70ae3-122">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="70ae3-123">Alguns componentes incluem lógica de análise útil.</span><span class="sxs-lookup"><span data-stu-id="70ae3-123">Some components include useful parsing logic.</span></span> <span data-ttu-id="70ae3-124">Por exemplo, `InputDate` e `InputNumber` manipular valores não analisáveis normalmente registrando-os como erros de validação.</span><span class="sxs-lookup"><span data-stu-id="70ae3-124">For example, `InputDate` and `InputNumber` handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="70ae3-125">Os tipos que podem aceitar valores nulos também dão suporte à nulidade do campo de destino (por exemplo, `int?`).</span><span class="sxs-lookup"><span data-stu-id="70ae3-125">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="70ae3-126">O tipo de `Starship` a seguir define a lógica de validação usando um conjunto maior de propriedades e anotações de dados do que o `ExampleModel`anterior:</span><span class="sxs-lookup"><span data-stu-id="70ae3-126">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    [Required]
    [StringLength(16, ErrorMessage = "Identifier too long (16 character limit).")]
    public string Identifier { get; set; }

    public string Description { get; set; }

    [Required]
    public string Classification { get; set; }

    [Range(1, 100000, ErrorMessage = "Accommodation invalid (1-100000).")]
    public int MaximumAccommodation { get; set; }

    [Required]
    [Range(typeof(bool), "true", "true", 
        ErrorMessage = "This form disallows unapproved ships.")]
    public bool IsValidatedDesign { get; set; }

    [Required]
    public DateTime ProductionDate { get; set; }
}
```

<span data-ttu-id="70ae3-127">No exemplo anterior, `Description` é opcional porque não há anotações de dados presentes.</span><span class="sxs-lookup"><span data-stu-id="70ae3-127">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="70ae3-128">O formulário a seguir valida a entrada do usuário usando a validação definida no modelo de `Starship`:</span><span class="sxs-lookup"><span data-stu-id="70ae3-128">The following form validates user input using the validation defined in the `Starship` model:</span></span>

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label for="identifier">Identifier: </label>
        <InputText id="identifier" @bind-Value="starship.Identifier" />
    </p>
    <p>
        <label for="description">Description (optional): </label>
        <InputTextArea id="description" @bind-Value="starship.Description" />
    </p>
    <p>
        <label for="classification">Primary Classification: </label>
        <InputSelect id="classification" @bind-Value="starship.Classification">
            <option value="">Select classification ...</option>
            <option value="Exploration">Exploration</option>
            <option value="Diplomacy">Diplomacy</option>
            <option value="Defense">Defense</option>
        </InputSelect>
    </p>
    <p>
        <label for="accommodation">Maximum Accommodation: </label>
        <InputNumber id="accommodation" 
            @bind-Value="starship.MaximumAccommodation" />
    </p>
    <p>
        <label for="valid">Engineering Approval: </label>
        <InputCheckbox id="valid" @bind-Value="starship.IsValidatedDesign" />
    </p>
    <p>
        <label for="productionDate">Production Date: </label>
        <InputDate id="productionDate" @bind-Value="starship.ProductionDate" />
    </p>

    <button type="submit">Submit</button>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>, 
        &copy;1966-2019 CBS Studios, Inc. and 
        <a href="https://www.paramount.com">Paramount Pictures</a>
    </p>
</EditForm>

@code {
    private Starship starship = new Starship();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

<span data-ttu-id="70ae3-129">O `EditForm` cria um `EditContext` como um [valor em cascata](xref:blazor/components#cascading-values-and-parameters) que controla os metadados sobre o processo de edição, incluindo quais campos foram modificados e as mensagens de validação atuais.</span><span class="sxs-lookup"><span data-stu-id="70ae3-129">The `EditForm` creates an `EditContext` as a [cascading value](xref:blazor/components#cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="70ae3-130">O `EditForm` também fornece eventos convenientes para envios válidos e inválidos (`OnValidSubmit`, `OnInvalidSubmit`).</span><span class="sxs-lookup"><span data-stu-id="70ae3-130">The `EditForm` also provides convenient events for valid and invalid submits (`OnValidSubmit`, `OnInvalidSubmit`).</span></span> <span data-ttu-id="70ae3-131">Como alternativa, use `OnSubmit` para disparar a validação e verificar os valores do campo com o código de validação personalizado.</span><span class="sxs-lookup"><span data-stu-id="70ae3-131">Alternatively, use `OnSubmit` to trigger the validation and check field values with custom validation code.</span></span>

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="70ae3-132">InputText com base no evento de entrada</span><span class="sxs-lookup"><span data-stu-id="70ae3-132">InputText based on the input event</span></span>

<span data-ttu-id="70ae3-133">Use o componente `InputText` para criar um componente personalizado que usa o evento `input` em vez do evento `change`.</span><span class="sxs-lookup"><span data-stu-id="70ae3-133">Use the `InputText` component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="70ae3-134">Crie um componente com a marcação a seguir e use o componente da mesma forma que `InputText` é usado:</span><span class="sxs-lookup"><span data-stu-id="70ae3-134">Create a component with the following markup, and use the component just as `InputText` is used:</span></span>

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="validation-support"></a><span data-ttu-id="70ae3-135">Suporte à validação</span><span class="sxs-lookup"><span data-stu-id="70ae3-135">Validation support</span></span>

<span data-ttu-id="70ae3-136">O componente `DataAnnotationsValidator` anexa o suporte à validação usando anotações de dados para o `EditContext`em cascata.</span><span class="sxs-lookup"><span data-stu-id="70ae3-136">The `DataAnnotationsValidator` component attaches validation support using data annotations to the cascaded `EditContext`.</span></span> <span data-ttu-id="70ae3-137">Habilitar o suporte para validação usando anotações de dados requer esse gesto explícito.</span><span class="sxs-lookup"><span data-stu-id="70ae3-137">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="70ae3-138">Para usar um sistema de validação diferente de anotações de dados, substitua o `DataAnnotationsValidator` por uma implementação personalizada.</span><span class="sxs-lookup"><span data-stu-id="70ae3-138">To use a different validation system than data annotations, replace the `DataAnnotationsValidator` with a custom implementation.</span></span> <span data-ttu-id="70ae3-139">A implementação de ASP.NET Core está disponível para inspeção na fonte de referência: [DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="70ae3-139">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span>

Blazor<span data-ttu-id="70ae3-140"> executa dois tipos de validação:</span><span class="sxs-lookup"><span data-stu-id="70ae3-140"> performs two types of validation:</span></span>

* <span data-ttu-id="70ae3-141">A *validação de campo* é executada quando o usuário faz a Tabulação de um campo.</span><span class="sxs-lookup"><span data-stu-id="70ae3-141">*Field validation* is performed when the user tabs out of a field.</span></span> <span data-ttu-id="70ae3-142">Durante a validação de campo, o componente `DataAnnotationsValidator` associa todos os resultados de validação relatados com o campo.</span><span class="sxs-lookup"><span data-stu-id="70ae3-142">During field validation, the `DataAnnotationsValidator` component associates all reported validation results with the field.</span></span>
* <span data-ttu-id="70ae3-143">A *validação do modelo* é executada quando o usuário envia o formulário.</span><span class="sxs-lookup"><span data-stu-id="70ae3-143">*Model validation* is performed when the user submits the form.</span></span> <span data-ttu-id="70ae3-144">Durante a validação do modelo, o componente `DataAnnotationsValidator` tenta determinar o campo com base no nome do membro que o resultado da validação relata.</span><span class="sxs-lookup"><span data-stu-id="70ae3-144">During model validation, the `DataAnnotationsValidator` component attempts to determine the field based on the member name that the validation result reports.</span></span> <span data-ttu-id="70ae3-145">Os resultados de validação que não estão associados a um membro individual são associados ao modelo em vez de um campo.</span><span class="sxs-lookup"><span data-stu-id="70ae3-145">Validation results that aren't associated with an individual member are associated with the model rather than a field.</span></span>

### <a name="validation-summary-and-validation-message-components"></a><span data-ttu-id="70ae3-146">Resumo de validação e componentes de mensagem de validação</span><span class="sxs-lookup"><span data-stu-id="70ae3-146">Validation Summary and Validation Message components</span></span>

<span data-ttu-id="70ae3-147">O componente `ValidationSummary` resume todas as mensagens de validação, que é semelhante ao [auxiliar de marca de Resumo de validação](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span><span class="sxs-lookup"><span data-stu-id="70ae3-147">The `ValidationSummary` component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span></span>

```razor
<ValidationSummary />
```

<span data-ttu-id="70ae3-148">Mensagens de validação de saída para um modelo específico com o parâmetro `Model`:</span><span class="sxs-lookup"><span data-stu-id="70ae3-148">Output validation messages for a specific model with the `Model` parameter:</span></span>
  
```razor
<ValidationSummary Model="@starship" />
```

<span data-ttu-id="70ae3-149">O componente `ValidationMessage` exibe mensagens de validação para um campo específico, que é semelhante ao [auxiliar de marca de mensagem de validação](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="70ae3-149">The `ValidationMessage` component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="70ae3-150">Especifique o campo para validação com o atributo `For` e uma expressão lambda nomeando a propriedade do modelo:</span><span class="sxs-lookup"><span data-stu-id="70ae3-150">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="70ae3-151">Os componentes `ValidationMessage` e `ValidationSummary` dão suporte a atributos arbitrários.</span><span class="sxs-lookup"><span data-stu-id="70ae3-151">The `ValidationMessage` and `ValidationSummary` components support arbitrary attributes.</span></span> <span data-ttu-id="70ae3-152">Qualquer atributo que não corresponda a um parâmetro de componente é adicionado ao `<div>` gerado ou ao elemento `<ul>`.</span><span class="sxs-lookup"><span data-stu-id="70ae3-152">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

### <a name="custom-validation-attributes"></a><span data-ttu-id="70ae3-153">Atributos de validação personalizados</span><span class="sxs-lookup"><span data-stu-id="70ae3-153">Custom validation attributes</span></span>

<span data-ttu-id="70ae3-154">Para garantir que um resultado de validação esteja corretamente associado a um campo ao usar um [atributo de validação personalizado](xref:mvc/models/validation#custom-attributes), passe o <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> do contexto de validação ao criar o <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span><span class="sxs-lookup"><span data-stu-id="70ae3-154">To ensure that a validation result is correctly associated with a field when using a [custom validation attribute](xref:mvc/models/validation#custom-attributes), pass the validation context's <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> when creating the <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

private class MyCustomValidator : ValidationAttribute
{
    protected override ValidationResult IsValid(object value, 
        ValidationContext validationContext)
    {
        ...

        return new ValidationResult("Validation message to user.",
            new[] { validationContext.MemberName });
    }
}
```

::: moniker range=">= aspnetcore-3.1"

### <a name="opno-locblazor-data-annotations-validation-package"></a>Blazor<span data-ttu-id="70ae3-155"> pacote de validação de anotações de dados</span><span class="sxs-lookup"><span data-stu-id="70ae3-155"> data annotations validation package</span></span>

<span data-ttu-id="70ae3-156">O [Microsoft. AspNetCore.Blazor. Annotations. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) é um pacote que preenche as lacunas da experiência de validação usando o componente `DataAnnotationsValidator`.</span><span class="sxs-lookup"><span data-stu-id="70ae3-156">The [Microsoft.AspNetCore.Blazor.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) is a package that fills validation experience gaps using the `DataAnnotationsValidator` component.</span></span> <span data-ttu-id="70ae3-157">O pacote está *experimental*no momento.</span><span class="sxs-lookup"><span data-stu-id="70ae3-157">The package is currently *experimental*.</span></span>

### <a name="compareproperty-attribute"></a><span data-ttu-id="70ae3-158">Atributo [compareproperty]</span><span class="sxs-lookup"><span data-stu-id="70ae3-158">[CompareProperty] attribute</span></span>

<span data-ttu-id="70ae3-159">O <xref:System.ComponentModel.DataAnnotations.CompareAttribute> não funciona bem com o componente `DataAnnotationsValidator`.</span><span class="sxs-lookup"><span data-stu-id="70ae3-159">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the `DataAnnotationsValidator` component.</span></span> <span data-ttu-id="70ae3-160">O [Microsoft. AspNetCore.Blazor. Annotations.](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) o pacote *experimental* de validação introduz um atributo de validação adicional, `ComparePropertyAttribute`, que funciona em relação a essas limitações.</span><span class="sxs-lookup"><span data-stu-id="70ae3-160">The [Microsoft.AspNetCore.Blazor.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) *experimental* package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="70ae3-161">Em um aplicativo Blazor, `[CompareProperty]` é uma substituição direta para o atributo `[Compare]`.</span><span class="sxs-lookup"><span data-stu-id="70ae3-161">In a Blazor app, `[CompareProperty]` is a direct replacement for the `[Compare]` attribute.</span></span> <span data-ttu-id="70ae3-162">Para obter mais informações, consulte [compareAttribute ignorado com OnValidSubmit EditForm (ASPNET/AspNetCore #10643)](https://github.com/aspnet/AspNetCore/issues/10643#issuecomment-543909748).</span><span class="sxs-lookup"><span data-stu-id="70ae3-162">For more information, see [CompareAttribute ignored with OnValidSubmit EditForm (aspnet/AspNetCore #10643)](https://github.com/aspnet/AspNetCore/issues/10643#issuecomment-543909748).</span></span>

### <a name="nested-models-collection-types-and-complex-types"></a><span data-ttu-id="70ae3-163">Modelos aninhados, tipos de coleção e tipos complexos</span><span class="sxs-lookup"><span data-stu-id="70ae3-163">Nested models, collection types, and complex types</span></span>

Blazor<span data-ttu-id="70ae3-164"> fornece suporte para validar a entrada de formulário usando anotações de dados com o `DataAnnotationsValidator`interno.</span><span class="sxs-lookup"><span data-stu-id="70ae3-164"> provides support for validating form input using data annotations with the built-in `DataAnnotationsValidator`.</span></span> <span data-ttu-id="70ae3-165">No entanto, a `DataAnnotationsValidator` só valida as propriedades de nível superior do modelo associado ao formulário que não são propriedades de tipo de coleção ou complexas.</span><span class="sxs-lookup"><span data-stu-id="70ae3-165">However, the `DataAnnotationsValidator` only validates top-level properties of the model bound to the form that aren't collection- or complex-type properties.</span></span>

<span data-ttu-id="70ae3-166">Para validar o gráfico de objeto inteiro do modelo associado, incluindo propriedades de tipo de coleção e complexas, use o `ObjectGraphDataAnnotationsValidator` fornecido pelo [Microsoft. AspNetCore.Blazorexperimental. Pacote Annotations. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) :</span><span class="sxs-lookup"><span data-stu-id="70ae3-166">To validate the bound model's entire object graph, including collection- and complex-type properties, use the `ObjectGraphDataAnnotationsValidator` provided by the *experimental* [Microsoft.AspNetCore.Blazor.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) package:</span></span>

```razor
<EditForm Model="@model" OnValidSubmit="@HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

<span data-ttu-id="70ae3-167">Anote as propriedades do modelo com `[ValidateComplexType]`.</span><span class="sxs-lookup"><span data-stu-id="70ae3-167">Annotate model properties with `[ValidateComplexType]`.</span></span> <span data-ttu-id="70ae3-168">Nas classes de modelo a seguir, a classe `ShipDescription` contém anotações de dados adicionais para validar quando o modelo está associado ao formulário:</span><span class="sxs-lookup"><span data-stu-id="70ae3-168">In the following model classes, the `ShipDescription` class contains additional data annotations to validate when the model is bound to the form:</span></span>

<span data-ttu-id="70ae3-169">*Starship.cs*:</span><span class="sxs-lookup"><span data-stu-id="70ae3-169">*Starship.cs*:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    ...

    [ValidateComplexType]
    public ShipDescription ShipDescription { get; set; }

    ...
}
```

<span data-ttu-id="70ae3-170">*ShipDescription.cs*:</span><span class="sxs-lookup"><span data-stu-id="70ae3-170">*ShipDescription.cs*:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class ShipDescription
{
    [Required]
    [StringLength(40, ErrorMessage = "Description too long (40 char).")]
    public string ShortDescription { get; set; }
    
    [Required]
    [StringLength(240, ErrorMessage = "Description too long (240 char).")]
    public string LongDescription { get; set; }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.1"

### <a name="validation-of-complex-or-collection-type-properties"></a><span data-ttu-id="70ae3-171">Validação de propriedades de tipo de coleção ou complexas</span><span class="sxs-lookup"><span data-stu-id="70ae3-171">Validation of complex or collection type properties</span></span>

<span data-ttu-id="70ae3-172">Os atributos de validação aplicados às propriedades de um modelo são validados quando o formulário é enviado.</span><span class="sxs-lookup"><span data-stu-id="70ae3-172">Validation attributes applied to the properties of a model validate when the form is submitted.</span></span> <span data-ttu-id="70ae3-173">No entanto, as propriedades de coleções ou tipos de dados complexos de um modelo não são validadas no envio de formulários pelo componente `DataAnnotationsValidator`.</span><span class="sxs-lookup"><span data-stu-id="70ae3-173">However, the properties of collections or complex data types of a model aren't validated on form submission by the `DataAnnotationsValidator` component.</span></span> <span data-ttu-id="70ae3-174">Para honrar os atributos de validação aninhados nesse cenário, use um componente de validação personalizado.</span><span class="sxs-lookup"><span data-stu-id="70ae3-174">To honor the nested validation attributes in this scenario, use a custom validation component.</span></span> <span data-ttu-id="70ae3-175">Para obter um exemplo, consulte o [exemplo de validação deBlazor (ASPNET/samples)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation).</span><span class="sxs-lookup"><span data-stu-id="70ae3-175">For an example, see the [Blazor Validation sample (aspnet/samples)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation).</span></span>

::: moniker-end
