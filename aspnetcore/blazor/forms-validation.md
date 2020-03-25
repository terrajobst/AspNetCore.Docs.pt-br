---
title: ASP.NET Core Blazor formulários e validação
author: guardrex
description: Saiba como usar formulários e cenários de validação de campo no Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/forms-validation
ms.openlocfilehash: 0359a9337860d9b8ce0b81d8833a034a898b05a5
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "80218954"
---
# <a name="aspnet-core-blazor-forms-and-validation"></a><span data-ttu-id="78dc5-103">Formulários e validação de mais e ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="78dc5-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="78dc5-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="78dc5-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="78dc5-105">Há suporte para formulários e validação com mais de um e mais, usando as [anotações de dados](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="78dc5-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="78dc5-106">O tipo de `ExampleModel` a seguir define a lógica de validação usando anotações de dados:</span><span class="sxs-lookup"><span data-stu-id="78dc5-106">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="78dc5-107">Um formulário é definido usando o componente `EditForm`.</span><span class="sxs-lookup"><span data-stu-id="78dc5-107">A form is defined using the `EditForm` component.</span></span> <span data-ttu-id="78dc5-108">O formulário a seguir demonstra elementos típicos, componentes e código do Razor:</span><span class="sxs-lookup"><span data-stu-id="78dc5-108">The following form demonstrates typical elements, components, and Razor code:</span></span>

```razor
<EditForm Model="@_exampleModel" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="_exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

@code {
    private ExampleModel _exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

<span data-ttu-id="78dc5-109">No exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="78dc5-109">In the preceding example:</span></span>

* <span data-ttu-id="78dc5-110">O formulário valida a entrada do usuário no campo `name` usando a validação definida no tipo de `ExampleModel`.</span><span class="sxs-lookup"><span data-stu-id="78dc5-110">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="78dc5-111">O modelo é criado no bloco de `@code` do componente e mantido em um campo privado (`_exampleModel`).</span><span class="sxs-lookup"><span data-stu-id="78dc5-111">The model is created in the component's `@code` block and held in a private field (`_exampleModel`).</span></span> <span data-ttu-id="78dc5-112">O campo é atribuído ao atributo `Model` do elemento `<EditForm>`.</span><span class="sxs-lookup"><span data-stu-id="78dc5-112">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="78dc5-113">O `@bind-Value` do componente de `InputText` associa:</span><span class="sxs-lookup"><span data-stu-id="78dc5-113">The `InputText` component's `@bind-Value` binds:</span></span>
  * <span data-ttu-id="78dc5-114">A propriedade de modelo (`_exampleModel.Name`) para a propriedade `Value` do componente de `InputText`.</span><span class="sxs-lookup"><span data-stu-id="78dc5-114">The model property (`_exampleModel.Name`) to the `InputText` component's `Value` property.</span></span>
  * <span data-ttu-id="78dc5-115">Um delegado de evento de alteração para a propriedade `ValueChanged` do componente de `InputText`.</span><span class="sxs-lookup"><span data-stu-id="78dc5-115">A change event delegate to the `InputText` component's `ValueChanged` property.</span></span>
* <span data-ttu-id="78dc5-116">O componente `DataAnnotationsValidator` anexa o suporte à validação usando anotações de dados.</span><span class="sxs-lookup"><span data-stu-id="78dc5-116">The `DataAnnotationsValidator` component attaches validation support using data annotations.</span></span>
* <span data-ttu-id="78dc5-117">O componente `ValidationSummary` resume as mensagens de validação.</span><span class="sxs-lookup"><span data-stu-id="78dc5-117">The `ValidationSummary` component summarizes validation messages.</span></span>
* <span data-ttu-id="78dc5-118">`HandleValidSubmit` é disparado quando o formulário é enviado com êxito (aprovado na validação).</span><span class="sxs-lookup"><span data-stu-id="78dc5-118">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

<span data-ttu-id="78dc5-119">Um conjunto de componentes de entrada internos está disponível para receber e validar a entrada do usuário.</span><span class="sxs-lookup"><span data-stu-id="78dc5-119">A set of built-in input components are available to receive and validate user input.</span></span> <span data-ttu-id="78dc5-120">As entradas são validadas quando são alteradas e quando um formulário é enviado.</span><span class="sxs-lookup"><span data-stu-id="78dc5-120">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="78dc5-121">Os componentes de entrada disponíveis são mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="78dc5-121">Available input components are shown in the following table.</span></span>

| <span data-ttu-id="78dc5-122">Componente de entrada</span><span class="sxs-lookup"><span data-stu-id="78dc5-122">Input component</span></span> | <span data-ttu-id="78dc5-123">Renderizado como&hellip;</span><span class="sxs-lookup"><span data-stu-id="78dc5-123">Rendered as&hellip;</span></span>       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

<span data-ttu-id="78dc5-124">Todos os componentes de entrada, incluindo `EditForm`, dão suporte a atributos arbitrários.</span><span class="sxs-lookup"><span data-stu-id="78dc5-124">All of the input components, including `EditForm`, support arbitrary attributes.</span></span> <span data-ttu-id="78dc5-125">Qualquer atributo que não corresponda a um parâmetro de componente é adicionado ao elemento HTML renderizado.</span><span class="sxs-lookup"><span data-stu-id="78dc5-125">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="78dc5-126">Os componentes de entrada fornecem o comportamento padrão para validar ao editar e alterar sua classe CSS para refletir o estado do campo.</span><span class="sxs-lookup"><span data-stu-id="78dc5-126">Input components provide default behavior for validating on edit and changing their CSS class to reflect the field state.</span></span> <span data-ttu-id="78dc5-127">Alguns componentes incluem lógica de análise útil.</span><span class="sxs-lookup"><span data-stu-id="78dc5-127">Some components include useful parsing logic.</span></span> <span data-ttu-id="78dc5-128">Por exemplo, `InputDate` e `InputNumber` manipular valores não analisáveis normalmente registrando-os como erros de validação.</span><span class="sxs-lookup"><span data-stu-id="78dc5-128">For example, `InputDate` and `InputNumber` handle unparseable values gracefully by registering them as validation errors.</span></span> <span data-ttu-id="78dc5-129">Os tipos que podem aceitar valores nulos também dão suporte à nulidade do campo de destino (por exemplo, `int?`).</span><span class="sxs-lookup"><span data-stu-id="78dc5-129">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="78dc5-130">O tipo de `Starship` a seguir define a lógica de validação usando um conjunto maior de propriedades e anotações de dados do que o `ExampleModel`anterior:</span><span class="sxs-lookup"><span data-stu-id="78dc5-130">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

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

<span data-ttu-id="78dc5-131">No exemplo anterior, `Description` é opcional porque não há anotações de dados presentes.</span><span class="sxs-lookup"><span data-stu-id="78dc5-131">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="78dc5-132">O formulário a seguir valida a entrada do usuário usando a validação definida no modelo de `Starship`:</span><span class="sxs-lookup"><span data-stu-id="78dc5-132">The following form validates user input using the validation defined in the `Starship` model:</span></span>

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@_starship" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label>
            Identifier:
            <InputText @bind-Value="_starship.Identifier" />
        </label>
    </p>
    <p>
        <label>
            Description (optional):
            <InputTextArea @bind-Value="_starship.Description" />
        </label>
    </p>
    <p>
        <label>
            Primary Classification:
            <InputSelect @bind-Value="_starship.Classification">
                <option value="">Select classification ...</option>
                <option value="Exploration">Exploration</option>
                <option value="Diplomacy">Diplomacy</option>
                <option value="Defense">Defense</option>
            </InputSelect>
        </label>
    </p>
    <p>
        <label>
            Maximum Accommodation:
            <InputNumber @bind-Value="_starship.MaximumAccommodation" />
        </label>
    </p>
    <p>
        <label>
            Engineering Approval:
            <InputCheckbox @bind-Value="_starship.IsValidatedDesign" />
        </label>
    </p>
    <p>
        <label>
            Production Date:
            <InputDate @bind-Value="_starship.ProductionDate" />
        </label>
    </p>

    <button type="submit">Submit</button>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>, 
        &copy;1966-2019 CBS Studios, Inc. and 
        <a href="https://www.paramount.com">Paramount Pictures</a>
    </p>
</EditForm>

@code {
    private Starship _starship = new Starship();

    private void HandleValidSubmit()
    {
        Console.WriteLine("OnValidSubmit");
    }
}
```

<span data-ttu-id="78dc5-133">O `EditForm` cria um `EditContext` como um [valor em cascata](xref:blazor/components#cascading-values-and-parameters) que controla os metadados sobre o processo de edição, incluindo quais campos foram modificados e as mensagens de validação atuais.</span><span class="sxs-lookup"><span data-stu-id="78dc5-133">The `EditForm` creates an `EditContext` as a [cascading value](xref:blazor/components#cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span> <span data-ttu-id="78dc5-134">O `EditForm` também fornece eventos convenientes para envios válidos e inválidos (`OnValidSubmit`, `OnInvalidSubmit`).</span><span class="sxs-lookup"><span data-stu-id="78dc5-134">The `EditForm` also provides convenient events for valid and invalid submits (`OnValidSubmit`, `OnInvalidSubmit`).</span></span> <span data-ttu-id="78dc5-135">Como alternativa, use `OnSubmit` para disparar a validação e verificar os valores do campo com o código de validação personalizado.</span><span class="sxs-lookup"><span data-stu-id="78dc5-135">Alternatively, use `OnSubmit` to trigger the validation and check field values with custom validation code.</span></span>

<span data-ttu-id="78dc5-136">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="78dc5-136">In the following example:</span></span>

* <span data-ttu-id="78dc5-137">O método `HandleSubmit` é executado quando o botão **Enviar** é selecionado.</span><span class="sxs-lookup"><span data-stu-id="78dc5-137">The `HandleSubmit` method runs when the **Submit** button is selected.</span></span>
* <span data-ttu-id="78dc5-138">O formulário é validado usando o `EditContext`do formulário.</span><span class="sxs-lookup"><span data-stu-id="78dc5-138">The form is validated using the form's `EditContext`.</span></span>
* <span data-ttu-id="78dc5-139">O formulário é validado ainda mais, passando o `EditContext` para o método `ServerValidate` que chama um ponto de extremidade da API Web no servidor (*não mostrado*).</span><span class="sxs-lookup"><span data-stu-id="78dc5-139">The form is further validated by passing the `EditContext` to the `ServerValidate` method that calls a web API endpoint on the server (*not shown*).</span></span>
* <span data-ttu-id="78dc5-140">O código adicional é executado dependendo do resultado da validação do cliente e do lado do servidor, verificando `isValid`.</span><span class="sxs-lookup"><span data-stu-id="78dc5-140">Additional code is run depending on the result of the client- and server-side validation by checking `isValid`.</span></span>

```razor
<EditForm EditContext="@_editContext" OnSubmit="@HandleSubmit">

    ...

    <button type="submit">Submit</button>
</EditForm>

@code {
    private Starship _starship = new Starship();
    private EditContext _editContext;

    protected override void OnInitialized()
    {
        _editContext = new EditContext(_starship);
    }

    private async Task HandleSubmit()
    {
        var isValid = _editContext.Validate() && 
            await ServerValidate(_editContext);

        if (isValid)
        {
            ...
        }
        else
        {
            ...
        }
    }

    private async Task<bool> ServerValidate(EditContext editContext)
    {
        var serverChecksValid = ...

        return serverChecksValid;
    }
}
```

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="78dc5-141">InputText com base no evento de entrada</span><span class="sxs-lookup"><span data-stu-id="78dc5-141">InputText based on the input event</span></span>

<span data-ttu-id="78dc5-142">Use o componente `InputText` para criar um componente personalizado que usa o evento `input` em vez do evento `change`.</span><span class="sxs-lookup"><span data-stu-id="78dc5-142">Use the `InputText` component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="78dc5-143">Crie um componente com a marcação a seguir e use o componente da mesma forma que `InputText` é usado:</span><span class="sxs-lookup"><span data-stu-id="78dc5-143">Create a component with the following markup, and use the component just as `InputText` is used:</span></span>

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="work-with-radio-buttons"></a><span data-ttu-id="78dc5-144">Trabalhar com botões de opção</span><span class="sxs-lookup"><span data-stu-id="78dc5-144">Work with radio buttons</span></span>

<span data-ttu-id="78dc5-145">Ao trabalhar com botões de opção em um formulário, a vinculação de dados é tratada de forma diferente de outros elementos porque os botões de opção são avaliados como um grupo.</span><span class="sxs-lookup"><span data-stu-id="78dc5-145">When working with radio buttons in a form, data binding is handled differently than other elements because radio buttons are evaluated as a group.</span></span> <span data-ttu-id="78dc5-146">O valor de cada botão de opção é fixo, mas o valor do grupo de botões de opção é o valor do botão de opção selecionado.</span><span class="sxs-lookup"><span data-stu-id="78dc5-146">The value of each radio button is fixed, but the value of the radio button group is the value of the selected radio button.</span></span> <span data-ttu-id="78dc5-147">O exemplo a seguir mostra como:</span><span class="sxs-lookup"><span data-stu-id="78dc5-147">The following example shows how to:</span></span>

* <span data-ttu-id="78dc5-148">Manipular a vinculação de dados para um grupo de botões de opção.</span><span class="sxs-lookup"><span data-stu-id="78dc5-148">Handle data binding for a radio button group.</span></span>
* <span data-ttu-id="78dc5-149">Suporte à validação usando um componente de `InputRadio` personalizado.</span><span class="sxs-lookup"><span data-stu-id="78dc5-149">Support validation using a custom `InputRadio` component.</span></span>

```razor
@using System.Globalization
@typeparam TValue
@inherits InputBase<TValue>

<input @attributes="AdditionalAttributes" type="radio" value="@SelectedValue" 
       checked="@(SelectedValue.Equals(Value))" @onchange="OnChange" />

@code {
    [Parameter]
    public TValue SelectedValue { get; set; }

    private void OnChange(ChangeEventArgs args)
    {
        CurrentValueAsString = args.Value.ToString();
    }

    protected override bool TryParseValueFromString(string value, 
        out TValue result, out string errorMessage)
    {
        var success = BindConverter.TryConvertTo<TValue>(
            value, CultureInfo.CurrentCulture, out var parsedValue);
        if (success)
        {
            result = parsedValue;
            errorMessage = null;

            return true;
        }
        else
        {
            result = default;
            errorMessage = $"{FieldIdentifier.FieldName} field isn't valid.";

            return false;
        }
    }
}
```

<span data-ttu-id="78dc5-150">O `EditForm` a seguir usa o componente `InputRadio` anterior para obter e validar uma classificação do usuário:</span><span class="sxs-lookup"><span data-stu-id="78dc5-150">The following `EditForm` uses the preceding `InputRadio` component to obtain and validate a rating from the user:</span></span>

```razor
@page "/RadioButtonExample"
@using System.ComponentModel.DataAnnotations

<h1>Radio Button Group Test</h1>

<EditForm Model="_model" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    @for (int i = 1; i <= 5; i++)
    {
        <label>
            <InputRadio name="rate" SelectedValue="i" @bind-Value="_model.Rating" />
            @i
        </label>
    }

    <button type="submit">Submit</button>
</EditForm>

<p>You chose: @_model.Rating</p>

@code {
    private Model _model = new Model();

    private void HandleValidSubmit()
    {
        Console.WriteLine("valid");
    }

    public class Model
    {
        [Range(1, 5)]
        public int Rating { get; set; }
    }
}
```

## <a name="validation-support"></a><span data-ttu-id="78dc5-151">Suporte à validação</span><span class="sxs-lookup"><span data-stu-id="78dc5-151">Validation support</span></span>

<span data-ttu-id="78dc5-152">O componente `DataAnnotationsValidator` anexa o suporte à validação usando anotações de dados para o `EditContext`em cascata.</span><span class="sxs-lookup"><span data-stu-id="78dc5-152">The `DataAnnotationsValidator` component attaches validation support using data annotations to the cascaded `EditContext`.</span></span> <span data-ttu-id="78dc5-153">Habilitar o suporte para validação usando anotações de dados requer esse gesto explícito.</span><span class="sxs-lookup"><span data-stu-id="78dc5-153">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="78dc5-154">Para usar um sistema de validação diferente de anotações de dados, substitua o `DataAnnotationsValidator` por uma implementação personalizada.</span><span class="sxs-lookup"><span data-stu-id="78dc5-154">To use a different validation system than data annotations, replace the `DataAnnotationsValidator` with a custom implementation.</span></span> <span data-ttu-id="78dc5-155">A implementação de ASP.NET Core está disponível para inspeção na fonte de referência: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="78dc5-155">The ASP.NET Core implementation is available for inspection in the reference source: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span>

Blazor<span data-ttu-id="78dc5-156"> executa dois tipos de validação:</span><span class="sxs-lookup"><span data-stu-id="78dc5-156"> performs two types of validation:</span></span>

* <span data-ttu-id="78dc5-157">A *validação de campo* é executada quando o usuário faz a Tabulação de um campo.</span><span class="sxs-lookup"><span data-stu-id="78dc5-157">*Field validation* is performed when the user tabs out of a field.</span></span> <span data-ttu-id="78dc5-158">Durante a validação de campo, o componente `DataAnnotationsValidator` associa todos os resultados de validação relatados com o campo.</span><span class="sxs-lookup"><span data-stu-id="78dc5-158">During field validation, the `DataAnnotationsValidator` component associates all reported validation results with the field.</span></span>
* <span data-ttu-id="78dc5-159">A *validação do modelo* é executada quando o usuário envia o formulário.</span><span class="sxs-lookup"><span data-stu-id="78dc5-159">*Model validation* is performed when the user submits the form.</span></span> <span data-ttu-id="78dc5-160">Durante a validação do modelo, o componente `DataAnnotationsValidator` tenta determinar o campo com base no nome do membro que o resultado da validação relata.</span><span class="sxs-lookup"><span data-stu-id="78dc5-160">During model validation, the `DataAnnotationsValidator` component attempts to determine the field based on the member name that the validation result reports.</span></span> <span data-ttu-id="78dc5-161">Os resultados de validação que não estão associados a um membro individual são associados ao modelo em vez de um campo.</span><span class="sxs-lookup"><span data-stu-id="78dc5-161">Validation results that aren't associated with an individual member are associated with the model rather than a field.</span></span>

### <a name="validation-summary-and-validation-message-components"></a><span data-ttu-id="78dc5-162">Resumo de validação e componentes de mensagem de validação</span><span class="sxs-lookup"><span data-stu-id="78dc5-162">Validation Summary and Validation Message components</span></span>

<span data-ttu-id="78dc5-163">O componente `ValidationSummary` resume todas as mensagens de validação, que é semelhante ao [auxiliar de marca de Resumo de validação](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span><span class="sxs-lookup"><span data-stu-id="78dc5-163">The `ValidationSummary` component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span></span>

```razor
<ValidationSummary />
```

<span data-ttu-id="78dc5-164">Mensagens de validação de saída para um modelo específico com o parâmetro `Model`:</span><span class="sxs-lookup"><span data-stu-id="78dc5-164">Output validation messages for a specific model with the `Model` parameter:</span></span>
  
```razor
<ValidationSummary Model="@_starship" />
```

<span data-ttu-id="78dc5-165">O componente `ValidationMessage` exibe mensagens de validação para um campo específico, que é semelhante ao [auxiliar de marca de mensagem de validação](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="78dc5-165">The `ValidationMessage` component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="78dc5-166">Especifique o campo para validação com o atributo `For` e uma expressão lambda nomeando a propriedade do modelo:</span><span class="sxs-lookup"><span data-stu-id="78dc5-166">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```razor
<ValidationMessage For="@(() => _starship.MaximumAccommodation)" />
```

<span data-ttu-id="78dc5-167">Os componentes `ValidationMessage` e `ValidationSummary` dão suporte a atributos arbitrários.</span><span class="sxs-lookup"><span data-stu-id="78dc5-167">The `ValidationMessage` and `ValidationSummary` components support arbitrary attributes.</span></span> <span data-ttu-id="78dc5-168">Qualquer atributo que não corresponda a um parâmetro de componente é adicionado ao `<div>` gerado ou ao elemento `<ul>`.</span><span class="sxs-lookup"><span data-stu-id="78dc5-168">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

### <a name="custom-validation-attributes"></a><span data-ttu-id="78dc5-169">Atributos de validação personalizados</span><span class="sxs-lookup"><span data-stu-id="78dc5-169">Custom validation attributes</span></span>

<span data-ttu-id="78dc5-170">Para garantir que um resultado de validação esteja corretamente associado a um campo ao usar um [atributo de validação personalizado](xref:mvc/models/validation#custom-attributes), passe o <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> do contexto de validação ao criar o <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span><span class="sxs-lookup"><span data-stu-id="78dc5-170">To ensure that a validation result is correctly associated with a field when using a [custom validation attribute](xref:mvc/models/validation#custom-attributes), pass the validation context's <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> when creating the <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span></span>

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

### <a name="opno-locblazor-data-annotations-validation-package"></a>Blazor<span data-ttu-id="78dc5-171"> pacote de validação de anotações de dados</span><span class="sxs-lookup"><span data-stu-id="78dc5-171"> data annotations validation package</span></span>

<span data-ttu-id="78dc5-172">O [Microsoft. AspNetCore. Components. Annotations. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) é um pacote que preenche as lacunas da experiência de validação usando o componente `DataAnnotationsValidator`.</span><span class="sxs-lookup"><span data-stu-id="78dc5-172">The [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) is a package that fills validation experience gaps using the `DataAnnotationsValidator` component.</span></span> <span data-ttu-id="78dc5-173">O pacote está *experimental*no momento.</span><span class="sxs-lookup"><span data-stu-id="78dc5-173">The package is currently *experimental*.</span></span>

### <a name="compareproperty-attribute"></a><span data-ttu-id="78dc5-174">Atributo [compareproperty]</span><span class="sxs-lookup"><span data-stu-id="78dc5-174">[CompareProperty] attribute</span></span>

<span data-ttu-id="78dc5-175">O <xref:System.ComponentModel.DataAnnotations.CompareAttribute> não funciona bem com o componente `DataAnnotationsValidator` porque ele não associa o resultado da validação a um membro específico.</span><span class="sxs-lookup"><span data-stu-id="78dc5-175">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the `DataAnnotationsValidator` component because it doesn't associate the validation result with a specific member.</span></span> <span data-ttu-id="78dc5-176">Isso pode resultar em comportamento inconsistente entre a validação em nível de campo e quando todo o modelo é validado em um envio.</span><span class="sxs-lookup"><span data-stu-id="78dc5-176">This can result in inconsistent behavior between field-level validation and when the entire model is validated on a submit.</span></span> <span data-ttu-id="78dc5-177">O pacote *experimental* [Microsoft. AspNetCore. Components. Annotations. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) introduz um atributo de validação adicional, `ComparePropertyAttribute`, que funciona em relação a essas limitações.</span><span class="sxs-lookup"><span data-stu-id="78dc5-177">The [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *experimental* package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="78dc5-178">Em um aplicativo Blazor, `[CompareProperty]` é uma substituição direta para o atributo `[Compare]`.</span><span class="sxs-lookup"><span data-stu-id="78dc5-178">In a Blazor app, `[CompareProperty]` is a direct replacement for the `[Compare]` attribute.</span></span>

### <a name="nested-models-collection-types-and-complex-types"></a><span data-ttu-id="78dc5-179">Modelos aninhados, tipos de coleção e tipos complexos</span><span class="sxs-lookup"><span data-stu-id="78dc5-179">Nested models, collection types, and complex types</span></span>

Blazor<span data-ttu-id="78dc5-180"> fornece suporte para validar a entrada de formulário usando anotações de dados com o `DataAnnotationsValidator`interno.</span><span class="sxs-lookup"><span data-stu-id="78dc5-180"> provides support for validating form input using data annotations with the built-in `DataAnnotationsValidator`.</span></span> <span data-ttu-id="78dc5-181">No entanto, a `DataAnnotationsValidator` só valida as propriedades de nível superior do modelo associado ao formulário que não são propriedades de tipo de coleção ou complexas.</span><span class="sxs-lookup"><span data-stu-id="78dc5-181">However, the `DataAnnotationsValidator` only validates top-level properties of the model bound to the form that aren't collection- or complex-type properties.</span></span>

<span data-ttu-id="78dc5-182">Para validar o gráfico de objeto inteiro do modelo associado, incluindo propriedades de tipo de coleção e complexas, use o `ObjectGraphDataAnnotationsValidator` fornecido pelo pacote *experimental* [Microsoft. AspNetCore. Components. Annotations. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) :</span><span class="sxs-lookup"><span data-stu-id="78dc5-182">To validate the bound model's entire object graph, including collection- and complex-type properties, use the `ObjectGraphDataAnnotationsValidator` provided by the *experimental* [Microsoft.AspNetCore.Components.DataAnnotations.Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package:</span></span>

```razor
<EditForm Model="@_model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

<span data-ttu-id="78dc5-183">Anote as propriedades do modelo com `[ValidateComplexType]`.</span><span class="sxs-lookup"><span data-stu-id="78dc5-183">Annotate model properties with `[ValidateComplexType]`.</span></span> <span data-ttu-id="78dc5-184">Nas classes de modelo a seguir, a classe `ShipDescription` contém anotações de dados adicionais para validar quando o modelo está associado ao formulário:</span><span class="sxs-lookup"><span data-stu-id="78dc5-184">In the following model classes, the `ShipDescription` class contains additional data annotations to validate when the model is bound to the form:</span></span>

<span data-ttu-id="78dc5-185">*Starship.cs*:</span><span class="sxs-lookup"><span data-stu-id="78dc5-185">*Starship.cs*:</span></span>

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

<span data-ttu-id="78dc5-186">*ShipDescription.cs*:</span><span class="sxs-lookup"><span data-stu-id="78dc5-186">*ShipDescription.cs*:</span></span>

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

### <a name="enable-the-submit-button-based-on-form-validation"></a><span data-ttu-id="78dc5-187">Habilitar o botão enviar com base na validação do formulário</span><span class="sxs-lookup"><span data-stu-id="78dc5-187">Enable the submit button based on form validation</span></span>

<span data-ttu-id="78dc5-188">Para habilitar e desabilitar o botão enviar com base na validação do formulário:</span><span class="sxs-lookup"><span data-stu-id="78dc5-188">To enable and disable the submit button based on form validation:</span></span>

* <span data-ttu-id="78dc5-189">Use o `EditContext` do formulário para atribuir o modelo quando o componente for inicializado.</span><span class="sxs-lookup"><span data-stu-id="78dc5-189">Use the form's `EditContext` to assign the model when the component is initialized.</span></span>
* <span data-ttu-id="78dc5-190">Valide o formulário no retorno de chamada de `OnFieldChanged` do contexto para habilitar e desabilitar o botão enviar.</span><span class="sxs-lookup"><span data-stu-id="78dc5-190">Validate the form in the context's `OnFieldChanged` callback to enable and disable the submit button.</span></span>
* <span data-ttu-id="78dc5-191">Desvincule o manipulador de eventos no método `Dispose`.</span><span class="sxs-lookup"><span data-stu-id="78dc5-191">Unhook the event handler in the `Dispose` method.</span></span> <span data-ttu-id="78dc5-192">Para obter mais informações, consulte <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="78dc5-192">For more information, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>

```razor
@implements IDisposable

<EditForm EditContext="@_editContext">
    <DataAnnotationsValidator />
    <ValidationSummary />

    ...

    <button type="submit" disabled="@_formInvalid">Submit</button>
</EditForm>

@code {
    private Starship _starship = new Starship();
    private bool _formInvalid = true;
    private EditContext _editContext;

    protected override void OnInitialized()
    {
        _editContext = new EditContext(_starship);
        _editContext.OnFieldChanged += HandleFieldChanged;
    }

    private void HandleFieldChanged(object sender, FieldChangedEventArgs e)
    {
        _formInvalid = !_editContext.Validate();
        StateHasChanged();
    }

    public void Dispose()
    {
        _editContext.OnFieldChanged -= HandleFieldChanged;
    }
}
```

<span data-ttu-id="78dc5-193">No exemplo anterior, defina `_formInvalid` como `false` se:</span><span class="sxs-lookup"><span data-stu-id="78dc5-193">In the preceding example, set `_formInvalid` to `false` if:</span></span>

* <span data-ttu-id="78dc5-194">O formulário é pré-carregado com valores padrão válidos.</span><span class="sxs-lookup"><span data-stu-id="78dc5-194">The form is preloaded with valid default values.</span></span>
* <span data-ttu-id="78dc5-195">Você deseja que o botão enviar seja habilitado quando o formulário é carregado.</span><span class="sxs-lookup"><span data-stu-id="78dc5-195">You want the submit button enabled when the form loads.</span></span>

<span data-ttu-id="78dc5-196">Um efeito colateral da abordagem anterior é que um componente `ValidationSummary` é preenchido com campos inválidos depois que o usuário interage com um campo.</span><span class="sxs-lookup"><span data-stu-id="78dc5-196">A side effect of the preceding approach is that a `ValidationSummary` component is populated with invalid fields after the user interacts with any one field.</span></span> <span data-ttu-id="78dc5-197">Esse cenário pode ser resolvido de uma das seguintes maneiras:</span><span class="sxs-lookup"><span data-stu-id="78dc5-197">This scenario can be addressed in either of the following ways:</span></span>

* <span data-ttu-id="78dc5-198">Não use um componente `ValidationSummary` no formulário.</span><span class="sxs-lookup"><span data-stu-id="78dc5-198">Don't use a `ValidationSummary` component on the form.</span></span>
* <span data-ttu-id="78dc5-199">Torne o componente `ValidationSummary` visível quando o botão enviar for selecionado (por exemplo, em um método `HandleValidSubmit`).</span><span class="sxs-lookup"><span data-stu-id="78dc5-199">Make the `ValidationSummary` component visible when the submit button is selected (for example, in a `HandleValidSubmit` method).</span></span>

```razor
<EditForm EditContext="@_editContext" OnValidSubmit="HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary style="@_displaySummary" />

    ...

    <button type="submit" disabled="@_formInvalid">Submit</button>
</EditForm>

@code {
    private string _displaySummary = "display:none";

    ...

    private void HandleValidSubmit()
    {
        _displaySummary = "display:block";
    }
}
```
