---
title: Formulários e validação de mais e ASP.NET Core
author: guardrex
description: Saiba como usar os formulários e cenários de validação de campo no mais incrivelmente.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/13/2019
uid: blazor/forms-validation
ms.openlocfilehash: 0b2e38cdbd974a28960b917fb6b5ce370f8c4659
ms.sourcegitcommit: f5f0ff65d4e2a961939762fb00e654491a2c772a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69030334"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a><span data-ttu-id="d6a92-103">Formulários e validação de mais e ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d6a92-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="d6a92-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d6a92-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d6a92-105">Há suporte para formulários e validação com mais de um e mais, usando as [anotações de dados](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="d6a92-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

> [!NOTE]
> <span data-ttu-id="d6a92-106">Os cenários de validação e formulários provavelmente serão alterados com cada versão de visualização.</span><span class="sxs-lookup"><span data-stu-id="d6a92-106">Forms and validation scenarios are likely to change with each preview release.</span></span>

<span data-ttu-id="d6a92-107">O tipo `ExampleModel` a seguir define a lógica de validação usando as anotações de dados:</span><span class="sxs-lookup"><span data-stu-id="d6a92-107">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="d6a92-108">Um formulário é definido usando o `EditForm` componente.</span><span class="sxs-lookup"><span data-stu-id="d6a92-108">A form is defined using the `EditForm` component.</span></span> <span data-ttu-id="d6a92-109">O formulário a seguir demonstra elementos típicos, componentes e código do Razor:</span><span class="sxs-lookup"><span data-stu-id="d6a92-109">The following form demonstrates typical elements, components, and Razor code:</span></span>

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

* <span data-ttu-id="d6a92-110">O formulário valida a entrada do usuário no `name` campo usando a validação definida `ExampleModel` no tipo.</span><span class="sxs-lookup"><span data-stu-id="d6a92-110">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="d6a92-111">O modelo é criado no bloco do `@code` componente e mantido em um campo privado (`exampleModel`).</span><span class="sxs-lookup"><span data-stu-id="d6a92-111">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="d6a92-112">O campo é atribuído ao `Model` atributo `<EditForm>` do elemento.</span><span class="sxs-lookup"><span data-stu-id="d6a92-112">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="d6a92-113">O `DataAnnotationsValidator` componente anexa o suporte à validação usando anotações de dados.</span><span class="sxs-lookup"><span data-stu-id="d6a92-113">The `DataAnnotationsValidator` component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="d6a92-114">O `ValidationSummary` componente resume as mensagens de validação.</span><span class="sxs-lookup"><span data-stu-id="d6a92-114">The `ValidationSummary` component summarizes validation messages.</span></span>
* <span data-ttu-id="d6a92-115">`HandleValidSubmit`é disparado quando o formulário é enviado com êxito (aprovado na validação).</span><span class="sxs-lookup"><span data-stu-id="d6a92-115">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="d6a92-116">Um conjunto de componentes de entrada internos está disponível para receber e validar a entrada do usuário.</span><span class="sxs-lookup"><span data-stu-id="d6a92-116">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="d6a92-117">As entradas são validadas quando são alteradas e quando um formulário é enviado.</span><span class="sxs-lookup"><span data-stu-id="d6a92-117">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="d6a92-118">Os componentes de entrada disponíveis são mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="d6a92-118">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="d6a92-119">Componente de entrada</span><span class="sxs-lookup"><span data-stu-id="d6a92-119">Input component</span></span> | <span data-ttu-id="d6a92-120">Renderizado como&hellip;</span><span class="sxs-lookup"><span data-stu-id="d6a92-120">Rendered as&hellip;</span></span>       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

<span data-ttu-id="d6a92-121">Todos os componentes de entrada, incluindo `EditForm`, dão suporte a atributos arbitrários.</span><span class="sxs-lookup"><span data-stu-id="d6a92-121">All of the input components, including `EditForm`, support arbitrary attributes.</span></span> <span data-ttu-id="d6a92-122">Qualquer atributo que não corresponda a um parâmetro de componente é adicionado ao elemento HTML renderizado.</span><span class="sxs-lookup"><span data-stu-id="d6a92-122">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="d6a92-123">Os componentes de entrada fornecem o comportamento padrão para validar ao editar e alterar sua classe CSS para refletir o estado do campo.</span><span class="sxs-lookup"><span data-stu-id="d6a92-123">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="d6a92-124">Alguns componentes incluem lógica de análise útil.</span><span class="sxs-lookup"><span data-stu-id="d6a92-124">Some components include useful parsing logic.</span></span> <span data-ttu-id="d6a92-125">Por exemplo, `InputDate` e `InputNumber` manipule valores não analisáveis normalmente registrando-os como erros de validação.</span><span class="sxs-lookup"><span data-stu-id="d6a92-125">For example, `InputDate` and `InputNumber` handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="d6a92-126">Os tipos que podem aceitar valores nulos também dão suporte à nulidade do campo de destino ( `int?`por exemplo,).</span><span class="sxs-lookup"><span data-stu-id="d6a92-126">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="d6a92-127">O tipo `Starship` a seguir define a lógica de validação usando um conjunto maior de propriedades e anotações de dados `ExampleModel`do que as anteriores:</span><span class="sxs-lookup"><span data-stu-id="d6a92-127">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

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

<span data-ttu-id="d6a92-128">No exemplo anterior, `Description` é opcional porque não há anotações de dados presentes.</span><span class="sxs-lookup"><span data-stu-id="d6a92-128">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="d6a92-129">O formulário a seguir valida a entrada do usuário usando a validação definida `Starship` no modelo:</span><span class="sxs-lookup"><span data-stu-id="d6a92-129">The following form validates user input using the validation defined in the `Starship` model:</span></span>

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

<span data-ttu-id="d6a92-130">O `EditForm` cria um `EditContext` como um [valor em cascata](xref:blazor/components#cascading-values-and-parameters) que controla os metadados sobre o processo de edição, incluindo quais campos foram modificados e as mensagens de validação atuais.</span><span class="sxs-lookup"><span data-stu-id="d6a92-130">The `EditForm` creates an `EditContext` as a [cascading value](xref:blazor/components#cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="d6a92-131">O `EditForm` também fornece eventos convenientes para envios válidos e inválidos (`OnValidSubmit`, `OnInvalidSubmit`).</span><span class="sxs-lookup"><span data-stu-id="d6a92-131">The `EditForm` also provides convenient events for valid and invalid submits (`OnValidSubmit`, `OnInvalidSubmit`).</span></span> <span data-ttu-id="d6a92-132">Como alternativa, use `OnSubmit` para disparar a validação e verificar os valores do campo com o código de validação personalizado.</span><span class="sxs-lookup"><span data-stu-id="d6a92-132">Alternatively, use `OnSubmit` to trigger the validation and check field values with custom validation code.</span></span>

<span data-ttu-id="d6a92-133">O `DataAnnotationsValidator` componente anexa o suporte à validação usando anotações de dados para o `EditContext`em cascata.</span><span class="sxs-lookup"><span data-stu-id="d6a92-133">The `DataAnnotationsValidator` component attaches validation support using data annotations to the cascaded `EditContext`.</span></span> <span data-ttu-id="d6a92-134">Habilitar o suporte para validação usando anotações de dados atualmente requer esse gesto explícito, mas estamos considerando tornar esse comportamento padrão que você pode então substituir.</span><span class="sxs-lookup"><span data-stu-id="d6a92-134">Enabling support for validation using data annotations currently requires this explicit gesture, but we're considering making this the default behavior that you can then override.</span></span> <span data-ttu-id="d6a92-135">Para usar um sistema de validação diferente de anotações de dados, substitua `DataAnnotationsValidator` -o por uma implementação personalizada.</span><span class="sxs-lookup"><span data-stu-id="d6a92-135">To use a different validation system than data annotations, replace the `DataAnnotationsValidator` with a custom implementation.</span></span> <span data-ttu-id="d6a92-136">A implementação de ASP.NET Core está disponível para inspeção na fonte de referência: [DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/EditContextDataAnnotationsExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="d6a92-136">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/EditContextDataAnnotationsExtensions.cs).</span></span> <span data-ttu-id="d6a92-137">*A implementação de ASP.NET Core está sujeita a atualizações rápidas durante o período de versão de visualização.*</span><span class="sxs-lookup"><span data-stu-id="d6a92-137">*The ASP.NET Core implementation is subject to rapid updates during the preview release period.*</span></span>

<span data-ttu-id="d6a92-138">O `ValidationSummary` componente resume todas as mensagens de validação, que é semelhante ao [auxiliar de marca de Resumo de validação](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="d6a92-138">The `ValidationSummary` component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper).</span></span>

<span data-ttu-id="d6a92-139">O `ValidationMessage` componente exibe mensagens de validação para um campo específico, que é semelhante ao [auxiliar de marca de mensagem de validação](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="d6a92-139">The `ValidationMessage` component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="d6a92-140">Especifique o campo para validação com o `For` atributo e uma expressão lambda nomeando a propriedade do modelo:</span><span class="sxs-lookup"><span data-stu-id="d6a92-140">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```cshtml
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="d6a92-141">Os `ValidationMessage` componentes `ValidationSummary` e oferecem suporte a atributos arbitrários.</span><span class="sxs-lookup"><span data-stu-id="d6a92-141">The `ValidationMessage` and `ValidationSummary` components support arbitrary attributes.</span></span> <span data-ttu-id="d6a92-142">Qualquer atributo que não corresponda a um parâmetro de componente é adicionado ao `<div>` elemento `<ul>` gerado ou.</span><span class="sxs-lookup"><span data-stu-id="d6a92-142">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>
