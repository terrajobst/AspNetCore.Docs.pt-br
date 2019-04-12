---
title: Validação e formulários de componentes do razor
author: guardrex
description: Saiba como usar formulários e cenários de validação de campo nos componentes do Razor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2019
uid: razor-components/forms-validation
ms.openlocfilehash: a4ed75efc6b5a733ce4ff4e82f354a8e2fd48500
ms.sourcegitcommit: 948e533e02c2a7cb6175ada20b2c9cabb7786d0b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/10/2019
ms.locfileid: "59515346"
---
# <a name="razor-components-forms-and-validation"></a><span data-ttu-id="06912-103">Validação e formulários de componentes do razor</span><span class="sxs-lookup"><span data-stu-id="06912-103">Razor Components forms and validation</span></span>

<span data-ttu-id="06912-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="06912-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="06912-105">Formulários e validação têm suporte nos componentes do Razor usando [anotações de dados](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="06912-105">Forms and validation are supported in Razor Components using [data annotations](xref:mvc/models/validation).</span></span>

> [!NOTE]
> <span data-ttu-id="06912-106">Formulários e cenários de validação são provavelmente mudarão a cada versão de visualização.</span><span class="sxs-lookup"><span data-stu-id="06912-106">Forms and validation scenarios are likely to change with each preview release.</span></span>

<span data-ttu-id="06912-107">O seguinte `ExampleModel` tipo define a lógica de validação usando anotações de dados:</span><span class="sxs-lookup"><span data-stu-id="06912-107">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="06912-108">Um formulário é definido usando o `<EditForm>` componente.</span><span class="sxs-lookup"><span data-stu-id="06912-108">A form is defined using the `<EditForm>` component.</span></span> <span data-ttu-id="06912-109">O formulário a seguir demonstra o código do Razor, componentes e elementos típicos:</span><span class="sxs-lookup"><span data-stu-id="06912-109">The following form demonstrates typical elements, components, and Razor code:</span></span>

```csharp
<EditForm Model="@exampleModel" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" bind-Value="@exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

@functions {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

* <span data-ttu-id="06912-110">O formulário valida a entrada do usuário na `name` campo usando a validação definida no `ExampleModel` tipo.</span><span class="sxs-lookup"><span data-stu-id="06912-110">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="06912-111">O modelo é criado no componente de `@functions` bloquear e mantidos em um campo particular (`exampleModel`).</span><span class="sxs-lookup"><span data-stu-id="06912-111">The model is created in the component's `@functions` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="06912-112">O campo é atribuído para o `Model` atributo do `<EditForm>`.</span><span class="sxs-lookup"><span data-stu-id="06912-112">The field is assigned to the `Model` attribute of the `<EditForm>`.</span></span>
* <span data-ttu-id="06912-113">O componente validador de anotações de dados (`<DataAnnotationsValidator>`) anexa o suporte de validação usando anotações de dados.</span><span class="sxs-lookup"><span data-stu-id="06912-113">The Data Annotations Validator component (`<DataAnnotationsValidator>`) attaches validation support using data annotations.</span></span>
* <span data-ttu-id="06912-114">O componente de resumo de validação (`<ValidationSummary>`) resume as mensagens de validação.</span><span class="sxs-lookup"><span data-stu-id="06912-114">The Validation Summary component (`<ValidationSummary>`) summarizes validation messages.</span></span>
* `HandleValidSubmit` <span data-ttu-id="06912-115">é acionado quando o formulário com êxito envia (foi aprovado na validação).</span><span class="sxs-lookup"><span data-stu-id="06912-115">is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="06912-116">Um conjunto de componentes internos de entrada estão disponíveis para receber e validar a entrada do usuário.</span><span class="sxs-lookup"><span data-stu-id="06912-116">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="06912-117">Entradas são validadas quando eles são alterados e quando um formulário é enviado.</span><span class="sxs-lookup"><span data-stu-id="06912-117">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="06912-118">Componentes de entrada disponíveis são mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="06912-118">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="06912-119">Componente de entrada</span><span class="sxs-lookup"><span data-stu-id="06912-119">Input component</span></span>   | <span data-ttu-id="06912-120">Renderizado como&hellip;</span><span class="sxs-lookup"><span data-stu-id="06912-120">Rendered as&hellip;</span></span>       |
| ----------------- | ------------------------- |
| `<InputText>`     | `<input>`                 |
| `<InputTextArea>` | `<textarea>`              |
| `<InputSelect>`   | `<select>`                |
| `<InputNumber>`   | `<input type="number">`   |
| `<InputCheckbox>` | `<input type="checkbox">` |
| `<InputDate>`     | `<input type="date">`     |

<span data-ttu-id="06912-121">Componentes de entrada fornecem o comportamento padrão para validar em Editar e alterar sua classe CSS para refletir o estado do campo.</span><span class="sxs-lookup"><span data-stu-id="06912-121">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="06912-122">Alguns componentes incluem a lógica de análise úteis.</span><span class="sxs-lookup"><span data-stu-id="06912-122">Some components include useful parsing logic.</span></span> <span data-ttu-id="06912-123">Por exemplo, `<InputDate>` e `<InputNumber>` tratar valores não analisável normalmente, registrando-os como erros de validação.</span><span class="sxs-lookup"><span data-stu-id="06912-123">For example, `<InputDate>` and `<InputNumber>` handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="06912-124">Tipos que podem aceitar valores nulos também dão suporte a nulidade do campo de destino (por exemplo, `int?`).</span><span class="sxs-lookup"><span data-stu-id="06912-124">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="06912-125">O seguinte `Starship` tipo define a lógica de validação usando um conjunto maior de propriedades e [anotações de dados](xref:mvc/models/validation) que o anterior `ExampleModel`:</span><span class="sxs-lookup"><span data-stu-id="06912-125">The following `Starship` type defines validation logic using a larger set of properties and [data annotations](xref:mvc/models/validation) than the earlier `ExampleModel`:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    [Required]
    [StringLength(16, 
        ErrorMessage = "Identifier too long (16 character limit).")]
    public string Identifier { get; set; }

    // Optional (no data annotations)
    public string Description { get; set; }

    [Required]
    public string Classification { get; set; }

    [Range(1, 100000, ErrorMessage = "Accommodation invalid (1-100000).")]
    public int MaximumAccommodation { get; set; }

    [Required]
    [Range(typeof(bool), "true", "true", 
        ErrorMessage = "Form disallowed for unapproved ships.")]
    public bool IsValidatedDesign { get; set; }

    [Required]
    public DateTime ProductionDate { get; set; }
}
```

<span data-ttu-id="06912-126">O formulário a seguir valida a entrada do usuário usando a validação definida no `Starship` modelo:</span><span class="sxs-lookup"><span data-stu-id="06912-126">The following form validates user input using the validation defined in the `Starship` model:</span></span>

```cshtml
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label for="identifier">Identifier: </label>
        <InputText id="identifier" bind-Value="@starship.Identifier" />
    </p>
    <p>
        <label for="description">Description (optional): </label>
        <InputTextArea Id="description" bind-Value="@starship.Description" />
    </p>
    <p>
        <label for="classification">Primary Classification: </label>
        <InputSelect id="classification" bind-Value="@starship.Classification">
            <option value"">Select classification ...</option>
            <option value="Defense">Defense</option>
            <option value="Exploration">Exploration</option>
            <option value="Diplomacy">Diplomacy</option>
        </InputSelect>
    </p>
    <p>
        <label for="accommodation">Maximum Accommodation: </label>
        <InputNumber id="accommodation" 
            bind-Value="@starship.MaximumAccommodation" />
    </p>
    <p>
        <label for="valid">Engineering Approval: </label>
        <InputCheckbox id="valid" bind-Value="@starship.IsValidatedDesign" />
    </p>
    <p>
        <label for="productionDate">Production Date: </label>
        <InputDate Id="productionDate" bind-Value="@starship.ProductionDate" />
    </p>

    <button type="submit">Submit</button>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>, 
        &copy;1966-2019 CBS Studios, Inc. and 
        <a href="http://www.paramount.com">Paramount Pictures</a>
    </p>
</EditForm>

@functions {
    private Starship starship = new Starship();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

<span data-ttu-id="06912-127">O `<EditForm>` cria um `EditContext` como um [valor em cascata](xref:razor-components/components#cascading-values-and-parameters) que acompanha metadados sobre o processo de edição, incluindo quais campos foram modificados e as mensagens de validação atual.</span><span class="sxs-lookup"><span data-stu-id="06912-127">The `<EditForm>` creates an `EditContext` as a [cascading value](xref:razor-components/components#cascading-values-and-parameters) that tracks metadata about the edit process, including what fields have been modified and the current validation messages.</span></span> <span data-ttu-id="06912-128">O `<EditForm>` também fornece eventos convenientes para válidas e inválidas envia (`OnValidSubmit`, `OnInvalidSubmit`).</span><span class="sxs-lookup"><span data-stu-id="06912-128">The `<EditForm>` also provides convenient events for valid and invalid submits (`OnValidSubmit`, `OnInvalidSubmit`).</span></span> <span data-ttu-id="06912-129">Como alternativa, use `OnSubmit` para acionar a validação e verificar os valores de campo com o código de validação personalizado.</span><span class="sxs-lookup"><span data-stu-id="06912-129">Alternatively, use `OnSubmit` to trigger the validation and check field values with custom validation code.</span></span>

<span data-ttu-id="06912-130">O componente validador de anotações de dados (`<DataAnnotationsValidator>`) anexa o suporte de validação usando anotações de dados a cascata `EditContext`.</span><span class="sxs-lookup"><span data-stu-id="06912-130">The Data Annotations Validator component (`<DataAnnotationsValidator>`) attaches validation support using data annotations to the cascaded `EditContext`.</span></span> <span data-ttu-id="06912-131">Habilitando o suporte para a validação usando data annotations atualmente requer esse gesto explícito, mas estamos considerando fazer isso o comportamento padrão que, em seguida, você pode substituir.</span><span class="sxs-lookup"><span data-stu-id="06912-131">Enabling support for validation using data annotations currently requires this explicit gesture, but we're considering making this the default behavior that you can then override.</span></span> <span data-ttu-id="06912-132">Para usar um sistema de validação diferente do que as anotações de dados, substitua o validador de anotações de dados com uma implementação personalizada.</span><span class="sxs-lookup"><span data-stu-id="06912-132">To use a different validation system than data annotations, replace the Data Annotations Validator with a custom implementation.</span></span> <span data-ttu-id="06912-133">A implementação do ASP.NET Core está disponível para inspeção na fonte de referência: [DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/EditContextDataAnnotationsExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="06912-133">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/EditContextDataAnnotationsExtensions.cs).</span></span> *<span data-ttu-id="06912-134">A implementação do ASP.NET Core está sujeito a atualizações rápidas durante o período de versão de visualização.</span><span class="sxs-lookup"><span data-stu-id="06912-134">The ASP.NET Core implementation is subject to rapid updates during the preview release period.</span></span>*

<span data-ttu-id="06912-135">O componente de resumo de validação (`<ValidationSummary>`) resume todas as mensagens de validação, que é semelhante ao [auxiliar de marca de resumo de validação](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="06912-135">The Validation Summary component (`<ValidationSummary>`) summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper).</span></span>

<span data-ttu-id="06912-136">O componente de mensagem de validação (`<ValidationMessage>`) exibe mensagens de validação para um campo específico, que é semelhante ao [auxiliar de marca de mensagem de validação](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="06912-136">The Validation Message component (`<ValidationMessage>`) displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="06912-137">Especificar o campo de validação com o `For` atributo e uma expressão lambda que nomeia a propriedade de modelo:</span><span class="sxs-lookup"><span data-stu-id="06912-137">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```cshtml
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

> [!NOTE]
> <span data-ttu-id="06912-138">Componentes internos de entrada têm limitações que esperamos resolver em futuras versões.</span><span class="sxs-lookup"><span data-stu-id="06912-138">Built-in input components have limitations that we expect to resolve in future releases.</span></span> <span data-ttu-id="06912-139">Por exemplo, você não pode especificar atributos arbitrários no gerado `<input>` marcas.</span><span class="sxs-lookup"><span data-stu-id="06912-139">For example, you can't specify arbitrary attributes on the generated `<input>` tags.</span></span> <span data-ttu-id="06912-140">Crie suas próprias subclasses de componente para lidar com cenários não está disponíveis.</span><span class="sxs-lookup"><span data-stu-id="06912-140">Build your own component subclasses to handle unavailable scenarios.</span></span>
