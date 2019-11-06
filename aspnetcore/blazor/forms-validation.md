---
title: Formulários e validação de mais e ASP.NET Core
author: guardrex
description: Saiba como usar os formulários e cenários de validação de campo no mais incrivelmente.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/04/2019
uid: blazor/forms-validation
ms.openlocfilehash: 09281779e7f0b31e525e0e79c2d6d9ce9ca5b8ce
ms.sourcegitcommit: 6628cd23793b66e4ce88788db641a5bbf470c3c1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73659783"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a><span data-ttu-id="48fbe-103">Formulários e validação de mais e ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="48fbe-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="48fbe-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="48fbe-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="48fbe-105">Há suporte para formulários e validação com mais de um e mais, usando as [anotações de dados](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="48fbe-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="48fbe-106">O tipo de `ExampleModel` a seguir define a lógica de validação usando anotações de dados:</span><span class="sxs-lookup"><span data-stu-id="48fbe-106">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="48fbe-107">Um formulário é definido usando o componente `EditForm`.</span><span class="sxs-lookup"><span data-stu-id="48fbe-107">A form is defined using the `EditForm` component.</span></span> <span data-ttu-id="48fbe-108">O formulário a seguir demonstra elementos típicos, componentes e código do Razor:</span><span class="sxs-lookup"><span data-stu-id="48fbe-108">The following form demonstrates typical elements, components, and Razor code:</span></span>

```csharp
<EditForm Model="@exampleModel" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="@exampleModel.Name" />

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

* <span data-ttu-id="48fbe-109">O formulário valida a entrada do usuário no campo `name` usando a validação definida no tipo de `ExampleModel`.</span><span class="sxs-lookup"><span data-stu-id="48fbe-109">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="48fbe-110">O modelo é criado no bloco de `@code` do componente e mantido em um campo privado (`exampleModel`).</span><span class="sxs-lookup"><span data-stu-id="48fbe-110">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="48fbe-111">O campo é atribuído ao atributo `Model` do elemento `<EditForm>`.</span><span class="sxs-lookup"><span data-stu-id="48fbe-111">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="48fbe-112">O componente `DataAnnotationsValidator` anexa o suporte à validação usando anotações de dados.</span><span class="sxs-lookup"><span data-stu-id="48fbe-112">The `DataAnnotationsValidator` component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="48fbe-113">O componente `ValidationSummary` resume as mensagens de validação.</span><span class="sxs-lookup"><span data-stu-id="48fbe-113">The `ValidationSummary` component summarizes validation messages.</span></span>
* <span data-ttu-id="48fbe-114">`HandleValidSubmit` é disparado quando o formulário é enviado com êxito (aprovado na validação).</span><span class="sxs-lookup"><span data-stu-id="48fbe-114">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="48fbe-115">Um conjunto de componentes de entrada internos está disponível para receber e validar a entrada do usuário.</span><span class="sxs-lookup"><span data-stu-id="48fbe-115">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="48fbe-116">As entradas são validadas quando são alteradas e quando um formulário é enviado.</span><span class="sxs-lookup"><span data-stu-id="48fbe-116">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="48fbe-117">Os componentes de entrada disponíveis são mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="48fbe-117">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="48fbe-118">Componente de entrada</span><span class="sxs-lookup"><span data-stu-id="48fbe-118">Input component</span></span> | <span data-ttu-id="48fbe-119">Renderizado como&hellip;</span><span class="sxs-lookup"><span data-stu-id="48fbe-119">Rendered as&hellip;</span></span>       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

<span data-ttu-id="48fbe-120">Todos os componentes de entrada, incluindo `EditForm`, dão suporte a atributos arbitrários.</span><span class="sxs-lookup"><span data-stu-id="48fbe-120">All of the input components, including `EditForm`, support arbitrary attributes.</span></span> <span data-ttu-id="48fbe-121">Qualquer atributo que não corresponda a um parâmetro de componente é adicionado ao elemento HTML renderizado.</span><span class="sxs-lookup"><span data-stu-id="48fbe-121">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="48fbe-122">Os componentes de entrada fornecem o comportamento padrão para validar ao editar e alterar sua classe CSS para refletir o estado do campo.</span><span class="sxs-lookup"><span data-stu-id="48fbe-122">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="48fbe-123">Alguns componentes incluem lógica de análise útil.</span><span class="sxs-lookup"><span data-stu-id="48fbe-123">Some components include useful parsing logic.</span></span> <span data-ttu-id="48fbe-124">Por exemplo, `InputDate` e `InputNumber` manipular valores não analisáveis normalmente registrando-os como erros de validação.</span><span class="sxs-lookup"><span data-stu-id="48fbe-124">For example, `InputDate` and `InputNumber` handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="48fbe-125">Os tipos que podem aceitar valores nulos também dão suporte à nulidade do campo de destino (por exemplo, `int?`).</span><span class="sxs-lookup"><span data-stu-id="48fbe-125">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="48fbe-126">O tipo de `Starship` a seguir define a lógica de validação usando um conjunto maior de propriedades e anotações de dados do que o `ExampleModel`anterior:</span><span class="sxs-lookup"><span data-stu-id="48fbe-126">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

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

<span data-ttu-id="48fbe-127">No exemplo anterior, `Description` é opcional porque não há anotações de dados presentes.</span><span class="sxs-lookup"><span data-stu-id="48fbe-127">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="48fbe-128">O formulário a seguir valida a entrada do usuário usando a validação definida no modelo de `Starship`:</span><span class="sxs-lookup"><span data-stu-id="48fbe-128">The following form validates user input using the validation defined in the `Starship` model:</span></span>

```cshtml
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

<span data-ttu-id="48fbe-129">O `EditForm` cria um `EditContext` como um [valor em cascata](xref:blazor/components#cascading-values-and-parameters) que controla os metadados sobre o processo de edição, incluindo quais campos foram modificados e as mensagens de validação atuais.</span><span class="sxs-lookup"><span data-stu-id="48fbe-129">The `EditForm` creates an `EditContext` as a [cascading value](xref:blazor/components#cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="48fbe-130">O `EditForm` também fornece eventos convenientes para envios válidos e inválidos (`OnValidSubmit`, `OnInvalidSubmit`).</span><span class="sxs-lookup"><span data-stu-id="48fbe-130">The `EditForm` also provides convenient events for valid and invalid submits (`OnValidSubmit`, `OnInvalidSubmit`).</span></span> <span data-ttu-id="48fbe-131">Como alternativa, use `OnSubmit` para disparar a validação e verificar os valores do campo com o código de validação personalizado.</span><span class="sxs-lookup"><span data-stu-id="48fbe-131">Alternatively, use `OnSubmit` to trigger the validation and check field values with custom validation code.</span></span>

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="48fbe-132">InputText com base no evento de entrada</span><span class="sxs-lookup"><span data-stu-id="48fbe-132">InputText based on the input event</span></span>

<span data-ttu-id="48fbe-133">Use o componente `InputText` para criar um componente personalizado que usa o evento `input` em vez do evento `change`.</span><span class="sxs-lookup"><span data-stu-id="48fbe-133">Use the `InputText` component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="48fbe-134">Crie um componente com a marcação a seguir e use o componente da mesma forma que `InputText` é usado:</span><span class="sxs-lookup"><span data-stu-id="48fbe-134">Create a component with the following markup, and use the component just as `InputText` is used:</span></span>

```cshtml
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="validation-support"></a><span data-ttu-id="48fbe-135">Suporte à validação</span><span class="sxs-lookup"><span data-stu-id="48fbe-135">Validation support</span></span>

<span data-ttu-id="48fbe-136">O componente `DataAnnotationsValidator` anexa o suporte à validação usando anotações de dados para o `EditContext`em cascata.</span><span class="sxs-lookup"><span data-stu-id="48fbe-136">The `DataAnnotationsValidator` component attaches validation support using data annotations to the cascaded `EditContext`.</span></span> <span data-ttu-id="48fbe-137">Habilitar o suporte para validação usando anotações de dados requer esse gesto explícito.</span><span class="sxs-lookup"><span data-stu-id="48fbe-137">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="48fbe-138">Para usar um sistema de validação diferente de anotações de dados, substitua o `DataAnnotationsValidator` por uma implementação personalizada.</span><span class="sxs-lookup"><span data-stu-id="48fbe-138">To use a different validation system than data annotations, replace the `DataAnnotationsValidator` with a custom implementation.</span></span> <span data-ttu-id="48fbe-139">A implementação de ASP.NET Core está disponível para inspeção na fonte de referência: [DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="48fbe-139">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span>

<span data-ttu-id="48fbe-140">O componente `ValidationSummary` resume todas as mensagens de validação, que é semelhante ao [auxiliar de marca de Resumo de validação](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="48fbe-140">The `ValidationSummary` component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper).</span></span>

<span data-ttu-id="48fbe-141">O componente `ValidationMessage` exibe mensagens de validação para um campo específico, que é semelhante ao [auxiliar de marca de mensagem de validação](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="48fbe-141">The `ValidationMessage` component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="48fbe-142">Especifique o campo para validação com o atributo `For` e uma expressão lambda nomeando a propriedade do modelo:</span><span class="sxs-lookup"><span data-stu-id="48fbe-142">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```cshtml
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="48fbe-143">Os componentes `ValidationMessage` e `ValidationSummary` dão suporte a atributos arbitrários.</span><span class="sxs-lookup"><span data-stu-id="48fbe-143">The `ValidationMessage` and `ValidationSummary` components support arbitrary attributes.</span></span> <span data-ttu-id="48fbe-144">Qualquer atributo que não corresponda a um parâmetro de componente é adicionado ao `<div>` gerado ou ao elemento `<ul>`.</span><span class="sxs-lookup"><span data-stu-id="48fbe-144">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="48fbe-145">**Pacote Microsoft. AspNetCore. mais. Annotations. revalidation**</span><span class="sxs-lookup"><span data-stu-id="48fbe-145">**Microsoft.AspNetCore.Blazor.DataAnnotations.Validation package**</span></span>

<span data-ttu-id="48fbe-146">O [Microsoft. AspNetCore. mais experiente. Annotations. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) é um pacote que preenche as lacunas da experiência de validação usando o componente `DataAnnotationsValidator`.</span><span class="sxs-lookup"><span data-stu-id="48fbe-146">The [Microsoft.AspNetCore.Blazor.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) is a package that fills validation experience gaps using the `DataAnnotationsValidator` component.</span></span> <span data-ttu-id="48fbe-147">O pacote está *experimental*no momento e planejamos adicionar esses cenários à estrutura de ASP.NET Core em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="48fbe-147">The package is currently *experimental*, and we plan to add these scenarios into the ASP.NET Core framework in a future release.</span></span>

<span data-ttu-id="48fbe-148">O componente `DataAnnotationsValidator` não valida as subpropriedades de propriedades complexas em um modelo de validação.</span><span class="sxs-lookup"><span data-stu-id="48fbe-148">The `DataAnnotationsValidator` component doesn't validate subproperties of complex properties on a validating model.</span></span> <span data-ttu-id="48fbe-149">Itens de propriedades de tipo de coleção não são validados.</span><span class="sxs-lookup"><span data-stu-id="48fbe-149">Items of collection-type properties aren't validated.</span></span> <span data-ttu-id="48fbe-150">Para validar esses tipos, o pacote de `Microsoft.AspNetCore.Blazor.DataAnnotations.Validation` apresenta o `ValidateComplexType` atributo de validação que funciona em conjunto com o componente de `ObjectGraphDataAnnotationsValidator`.</span><span class="sxs-lookup"><span data-stu-id="48fbe-150">To validate these types, the `Microsoft.AspNetCore.Blazor.DataAnnotations.Validation` package introduces the `ValidateComplexType` validation attribute that works in tandem with the `ObjectGraphDataAnnotationsValidator` component.</span></span> <span data-ttu-id="48fbe-151">Para obter um exemplo desses tipos em uso, consulte o [exemplo de validação mais simples no repositório GitHub ASPNET/Samples ](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation).</span><span class="sxs-lookup"><span data-stu-id="48fbe-151">For an example of these types in use, see the [Blazor Validation sample in the aspnet/samples GitHub repository ](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation).</span></span>

<span data-ttu-id="48fbe-152">O <xref:System.ComponentModel.DataAnnotations.CompareAttribute> não funciona bem com o componente `DataAnnotationsValidator`.</span><span class="sxs-lookup"><span data-stu-id="48fbe-152">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the `DataAnnotationsValidator` component.</span></span> <span data-ttu-id="48fbe-153">O pacote de `Microsoft.AspNetCore.Blazor.DataAnnotations.Validation` introduz um atributo de validação adicional, `ComparePropertyAttribute`, que funciona em relação a essas limitações.</span><span class="sxs-lookup"><span data-stu-id="48fbe-153">The `Microsoft.AspNetCore.Blazor.DataAnnotations.Validation` package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="48fbe-154">Em um aplicativo mais incrivelmente, `ComparePropertyAttribute` é uma substituição direta para o `CompareAttribute`.</span><span class="sxs-lookup"><span data-stu-id="48fbe-154">In a Blazor app, `ComparePropertyAttribute` is a direct replacement for the `CompareAttribute`.</span></span> <span data-ttu-id="48fbe-155">Para obter mais informações, consulte [compareAttribute ignorado com OnValidSubmit EditForm (ASPNET/AspNetCore \#10643)](https://github.com/aspnet/AspNetCore/issues/10643#issuecomment-543909748).</span><span class="sxs-lookup"><span data-stu-id="48fbe-155">For more information, see [CompareAttribute ignored with OnValidSubmit EditForm (aspnet/AspNetCore \#10643)](https://github.com/aspnet/AspNetCore/issues/10643#issuecomment-543909748).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.1"

### <a name="validation-of-complex-or-collection-type-properties"></a><span data-ttu-id="48fbe-156">Validação de propriedades de tipo de coleção ou complexas</span><span class="sxs-lookup"><span data-stu-id="48fbe-156">Validation of complex or collection type properties</span></span>

<span data-ttu-id="48fbe-157">Os atributos de validação aplicados às propriedades de um modelo são validados quando o formulário é enviado.</span><span class="sxs-lookup"><span data-stu-id="48fbe-157">Validation attributes applied to the properties of a model validate when the form is submitted.</span></span> <span data-ttu-id="48fbe-158">No entanto, as propriedades de coleções ou tipos de dados complexos de um modelo não são validadas no envio de formulários pelo componente `DataAnnotationsValidator`.</span><span class="sxs-lookup"><span data-stu-id="48fbe-158">However, the properties of collections or complex data types of a model aren't validated on form submission by the `DataAnnotationsValidator` component.</span></span> <span data-ttu-id="48fbe-159">Para honrar os atributos de validação aninhados nesse cenário, use um componente de validação personalizado.</span><span class="sxs-lookup"><span data-stu-id="48fbe-159">To honor the nested validation attributes in this scenario, use a custom validation component.</span></span> <span data-ttu-id="48fbe-160">Para obter um exemplo, consulte o [exemplo de validação mais simples no repositório do GitHub ASPNET/Samples](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation).</span><span class="sxs-lookup"><span data-stu-id="48fbe-160">For an example, see the [Blazor Validation sample in the aspnet/samples GitHub repository](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation).</span></span>

::: moniker-end
