---
title: Formulários e validação de mais e ASP.NET Core
author: guardrex
description: Saiba como usar os formulários e cenários de validação de campo no mais incrivelmente.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/15/2019
uid: blazor/forms-validation
ms.openlocfilehash: 2fd76db90a53e328cd2ac8f452fba58365db0384
ms.sourcegitcommit: dc5b293e08336dc236de66ed1834f7ef78359531
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/16/2019
ms.locfileid: "71011067"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a><span data-ttu-id="9e132-103">Formulários e validação de mais e ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9e132-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="9e132-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="9e132-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="9e132-105">Há suporte para formulários e validação com mais de um e mais, usando as [anotações de dados](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="9e132-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

> [!NOTE]
> <span data-ttu-id="9e132-106">Os cenários de validação e formulários provavelmente serão alterados com cada versão de visualização.</span><span class="sxs-lookup"><span data-stu-id="9e132-106">Forms and validation scenarios are likely to change with each preview release.</span></span>

<span data-ttu-id="9e132-107">O tipo `ExampleModel` a seguir define a lógica de validação usando as anotações de dados:</span><span class="sxs-lookup"><span data-stu-id="9e132-107">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="9e132-108">Um formulário é definido usando o `EditForm` componente.</span><span class="sxs-lookup"><span data-stu-id="9e132-108">A form is defined using the `EditForm` component.</span></span> <span data-ttu-id="9e132-109">O formulário a seguir demonstra elementos típicos, componentes e código do Razor:</span><span class="sxs-lookup"><span data-stu-id="9e132-109">The following form demonstrates typical elements, components, and Razor code:</span></span>

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

* <span data-ttu-id="9e132-110">O formulário valida a entrada do usuário no `name` campo usando a validação definida `ExampleModel` no tipo.</span><span class="sxs-lookup"><span data-stu-id="9e132-110">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="9e132-111">O modelo é criado no bloco do `@code` componente e mantido em um campo privado (`exampleModel`).</span><span class="sxs-lookup"><span data-stu-id="9e132-111">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="9e132-112">O campo é atribuído ao `Model` atributo `<EditForm>` do elemento.</span><span class="sxs-lookup"><span data-stu-id="9e132-112">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="9e132-113">O `DataAnnotationsValidator` componente anexa o suporte à validação usando anotações de dados.</span><span class="sxs-lookup"><span data-stu-id="9e132-113">The `DataAnnotationsValidator` component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="9e132-114">O `ValidationSummary` componente resume as mensagens de validação.</span><span class="sxs-lookup"><span data-stu-id="9e132-114">The `ValidationSummary` component summarizes validation messages.</span></span>
* <span data-ttu-id="9e132-115">`HandleValidSubmit`é disparado quando o formulário é enviado com êxito (aprovado na validação).</span><span class="sxs-lookup"><span data-stu-id="9e132-115">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="9e132-116">Um conjunto de componentes de entrada internos está disponível para receber e validar a entrada do usuário.</span><span class="sxs-lookup"><span data-stu-id="9e132-116">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="9e132-117">As entradas são validadas quando são alteradas e quando um formulário é enviado.</span><span class="sxs-lookup"><span data-stu-id="9e132-117">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="9e132-118">Os componentes de entrada disponíveis são mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="9e132-118">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="9e132-119">Componente de entrada</span><span class="sxs-lookup"><span data-stu-id="9e132-119">Input component</span></span> | <span data-ttu-id="9e132-120">Renderizado como&hellip;</span><span class="sxs-lookup"><span data-stu-id="9e132-120">Rendered as&hellip;</span></span>       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

<span data-ttu-id="9e132-121">Todos os componentes de entrada, incluindo `EditForm`, dão suporte a atributos arbitrários.</span><span class="sxs-lookup"><span data-stu-id="9e132-121">All of the input components, including `EditForm`, support arbitrary attributes.</span></span> <span data-ttu-id="9e132-122">Qualquer atributo que não corresponda a um parâmetro de componente é adicionado ao elemento HTML renderizado.</span><span class="sxs-lookup"><span data-stu-id="9e132-122">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="9e132-123">Os componentes de entrada fornecem o comportamento padrão para validar ao editar e alterar sua classe CSS para refletir o estado do campo.</span><span class="sxs-lookup"><span data-stu-id="9e132-123">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="9e132-124">Alguns componentes incluem lógica de análise útil.</span><span class="sxs-lookup"><span data-stu-id="9e132-124">Some components include useful parsing logic.</span></span> <span data-ttu-id="9e132-125">Por exemplo, `InputDate` e `InputNumber` manipule valores não analisáveis normalmente registrando-os como erros de validação.</span><span class="sxs-lookup"><span data-stu-id="9e132-125">For example, `InputDate` and `InputNumber` handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="9e132-126">Os tipos que podem aceitar valores nulos também dão suporte à nulidade do campo de destino ( `int?`por exemplo,).</span><span class="sxs-lookup"><span data-stu-id="9e132-126">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="9e132-127">O tipo `Starship` a seguir define a lógica de validação usando um conjunto maior de propriedades e anotações de dados `ExampleModel`do que as anteriores:</span><span class="sxs-lookup"><span data-stu-id="9e132-127">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

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

<span data-ttu-id="9e132-128">No exemplo anterior, `Description` é opcional porque não há anotações de dados presentes.</span><span class="sxs-lookup"><span data-stu-id="9e132-128">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="9e132-129">O formulário a seguir valida a entrada do usuário usando a validação definida `Starship` no modelo:</span><span class="sxs-lookup"><span data-stu-id="9e132-129">The following form validates user input using the validation defined in the `Starship` model:</span></span>

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

<span data-ttu-id="9e132-130">O `EditForm` cria um `EditContext` como um [valor em cascata](xref:blazor/components#cascading-values-and-parameters) que controla os metadados sobre o processo de edição, incluindo quais campos foram modificados e as mensagens de validação atuais.</span><span class="sxs-lookup"><span data-stu-id="9e132-130">The `EditForm` creates an `EditContext` as a [cascading value](xref:blazor/components#cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="9e132-131">O `EditForm` também fornece eventos convenientes para envios válidos e inválidos (`OnValidSubmit`, `OnInvalidSubmit`).</span><span class="sxs-lookup"><span data-stu-id="9e132-131">The `EditForm` also provides convenient events for valid and invalid submits (`OnValidSubmit`, `OnInvalidSubmit`).</span></span> <span data-ttu-id="9e132-132">Como alternativa, use `OnSubmit` para disparar a validação e verificar os valores do campo com o código de validação personalizado.</span><span class="sxs-lookup"><span data-stu-id="9e132-132">Alternatively, use `OnSubmit` to trigger the validation and check field values with custom validation code.</span></span>

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="9e132-133">InputText com base no evento de entrada</span><span class="sxs-lookup"><span data-stu-id="9e132-133">InputText based on the input event</span></span>

<span data-ttu-id="9e132-134">Use o `InputText` componente para criar um componente personalizado que usa o `input` evento em vez do `change` evento.</span><span class="sxs-lookup"><span data-stu-id="9e132-134">Use the `InputText` component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="9e132-135">Crie um componente com a marcação a seguir e use o componente da mesma `InputText` forma que é usado:</span><span class="sxs-lookup"><span data-stu-id="9e132-135">Create a component with the following markup, and use the component just as `InputText` is used:</span></span>

```cshtml
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="validation-support"></a><span data-ttu-id="9e132-136">Suporte à validação</span><span class="sxs-lookup"><span data-stu-id="9e132-136">Validation support</span></span>

<span data-ttu-id="9e132-137">O `DataAnnotationsValidator` componente anexa o suporte à validação usando anotações de dados para o `EditContext`em cascata.</span><span class="sxs-lookup"><span data-stu-id="9e132-137">The `DataAnnotationsValidator` component attaches validation support using data annotations to the cascaded `EditContext`.</span></span> <span data-ttu-id="9e132-138">Habilitar o suporte para validação usando anotações de dados requer esse gesto explícito.</span><span class="sxs-lookup"><span data-stu-id="9e132-138">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="9e132-139">Para usar um sistema de validação diferente de anotações de dados, substitua `DataAnnotationsValidator` -o por uma implementação personalizada.</span><span class="sxs-lookup"><span data-stu-id="9e132-139">To use a different validation system than data annotations, replace the `DataAnnotationsValidator` with a custom implementation.</span></span> <span data-ttu-id="9e132-140">A implementação de ASP.NET Core está disponível para inspeção na fonte de referência: [DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="9e132-140">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span>

<span data-ttu-id="9e132-141">O `ValidationSummary` componente resume todas as mensagens de validação, que é semelhante ao [auxiliar de marca de Resumo de validação](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="9e132-141">The `ValidationSummary` component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper).</span></span>

<span data-ttu-id="9e132-142">O `ValidationMessage` componente exibe mensagens de validação para um campo específico, que é semelhante ao [auxiliar de marca de mensagem de validação](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="9e132-142">The `ValidationMessage` component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="9e132-143">Especifique o campo para validação com o `For` atributo e uma expressão lambda nomeando a propriedade do modelo:</span><span class="sxs-lookup"><span data-stu-id="9e132-143">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```cshtml
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="9e132-144">Os `ValidationMessage` componentes `ValidationSummary` e oferecem suporte a atributos arbitrários.</span><span class="sxs-lookup"><span data-stu-id="9e132-144">The `ValidationMessage` and `ValidationSummary` components support arbitrary attributes.</span></span> <span data-ttu-id="9e132-145">Qualquer atributo que não corresponda a um parâmetro de componente é adicionado ao `<div>` elemento `<ul>` gerado ou.</span><span class="sxs-lookup"><span data-stu-id="9e132-145">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

### <a name="validation-of-complex-or-collection-type-properties"></a><span data-ttu-id="9e132-146">Validação de propriedades de tipo de coleção ou complexas</span><span class="sxs-lookup"><span data-stu-id="9e132-146">Validation of complex or collection type properties</span></span>

<span data-ttu-id="9e132-147">Os atributos de validação aplicados às propriedades de um modelo são validados quando o formulário é enviado.</span><span class="sxs-lookup"><span data-stu-id="9e132-147">Validation attributes applied to the properties of a model validate when the form is submitted.</span></span> <span data-ttu-id="9e132-148">No entanto, as propriedades de coleções ou tipos de dados complexos de um modelo não são validadas no envio de formulários.</span><span class="sxs-lookup"><span data-stu-id="9e132-148">However, the properties of collections or complex data types of a model aren't validated on form submission.</span></span> <span data-ttu-id="9e132-149">Para honrar os atributos de validação aninhados nesse cenário, use um componente de validação personalizado.</span><span class="sxs-lookup"><span data-stu-id="9e132-149">To honor the nested validation attributes in this scenario, use a custom validation component.</span></span> <span data-ttu-id="9e132-150">Para obter um exemplo, consulte o [exemplo de validação mais simples no repositório do GitHub ASPNET/Samples](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation).</span><span class="sxs-lookup"><span data-stu-id="9e132-150">For an example, see the [Blazor Validation sample in the aspnet/samples GitHub repository](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation).</span></span>
