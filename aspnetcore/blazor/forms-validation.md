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
# <a name="aspnet-core-blazor-forms-and-validation"></a>Formulários e validação de mais e ASP.NET Core

Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

Há suporte para formulários e validação com mais de um e mais, usando as [anotações de dados](xref:mvc/models/validation).

O tipo de `ExampleModel` a seguir define a lógica de validação usando anotações de dados:

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

Um formulário é definido usando o componente `EditForm`. O formulário a seguir demonstra elementos típicos, componentes e código do Razor:

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

No exemplo anterior:

* O formulário valida a entrada do usuário no campo `name` usando a validação definida no tipo de `ExampleModel`. O modelo é criado no bloco de `@code` do componente e mantido em um campo privado (`_exampleModel`). O campo é atribuído ao atributo `Model` do elemento `<EditForm>`.
* O `@bind-Value` do componente de `InputText` associa:
  * A propriedade de modelo (`_exampleModel.Name`) para a propriedade `Value` do componente de `InputText`.
  * Um delegado de evento de alteração para a propriedade `ValueChanged` do componente de `InputText`.
* O componente `DataAnnotationsValidator` anexa o suporte à validação usando anotações de dados.
* O componente `ValidationSummary` resume as mensagens de validação.
* `HandleValidSubmit` é disparado quando o formulário é enviado com êxito (aprovado na validação).

Um conjunto de componentes de entrada internos está disponível para receber e validar a entrada do usuário. As entradas são validadas quando são alteradas e quando um formulário é enviado. Os componentes de entrada disponíveis são mostrados na tabela a seguir.

| Componente de entrada | Renderizado como&hellip;       |
| --------------- | ------------------------- |
| `InputText`     | `<input>`                 |
| `InputTextArea` | `<textarea>`              |
| `InputSelect`   | `<select>`                |
| `InputNumber`   | `<input type="number">`   |
| `InputCheckbox` | `<input type="checkbox">` |
| `InputDate`     | `<input type="date">`     |

Todos os componentes de entrada, incluindo `EditForm`, dão suporte a atributos arbitrários. Qualquer atributo que não corresponda a um parâmetro de componente é adicionado ao elemento HTML renderizado.

Os componentes de entrada fornecem o comportamento padrão para validar ao editar e alterar sua classe CSS para refletir o estado do campo. Alguns componentes incluem lógica de análise útil. Por exemplo, `InputDate` e `InputNumber` manipular valores não analisáveis normalmente registrando-os como erros de validação. Os tipos que podem aceitar valores nulos também dão suporte à nulidade do campo de destino (por exemplo, `int?`).

O tipo de `Starship` a seguir define a lógica de validação usando um conjunto maior de propriedades e anotações de dados do que o `ExampleModel`anterior:

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

No exemplo anterior, `Description` é opcional porque não há anotações de dados presentes.

O formulário a seguir valida a entrada do usuário usando a validação definida no modelo de `Starship`:

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

O `EditForm` cria um `EditContext` como um [valor em cascata](xref:blazor/components#cascading-values-and-parameters) que controla os metadados sobre o processo de edição, incluindo quais campos foram modificados e as mensagens de validação atuais. O `EditForm` também fornece eventos convenientes para envios válidos e inválidos (`OnValidSubmit`, `OnInvalidSubmit`). Como alternativa, use `OnSubmit` para disparar a validação e verificar os valores do campo com o código de validação personalizado.

No exemplo a seguir:

* O método `HandleSubmit` é executado quando o botão **Enviar** é selecionado.
* O formulário é validado usando o `EditContext`do formulário.
* O formulário é validado ainda mais, passando o `EditContext` para o método `ServerValidate` que chama um ponto de extremidade da API Web no servidor (*não mostrado*).
* O código adicional é executado dependendo do resultado da validação do cliente e do lado do servidor, verificando `isValid`.

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

## <a name="inputtext-based-on-the-input-event"></a>InputText com base no evento de entrada

Use o componente `InputText` para criar um componente personalizado que usa o evento `input` em vez do evento `change`.

Crie um componente com a marcação a seguir e use o componente da mesma forma que `InputText` é usado:

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue" 
    @oninput="EventCallback.Factory.CreateBinder<string>(
        this, __value => CurrentValueAsString = __value, CurrentValueAsString)" />
```

## <a name="work-with-radio-buttons"></a>Trabalhar com botões de opção

Ao trabalhar com botões de opção em um formulário, a vinculação de dados é tratada de forma diferente de outros elementos porque os botões de opção são avaliados como um grupo. O valor de cada botão de opção é fixo, mas o valor do grupo de botões de opção é o valor do botão de opção selecionado. O exemplo a seguir mostra como:

* Manipular a vinculação de dados para um grupo de botões de opção.
* Suporte à validação usando um componente de `InputRadio` personalizado.

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

O `EditForm` a seguir usa o componente `InputRadio` anterior para obter e validar uma classificação do usuário:

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

## <a name="validation-support"></a>Suporte à validação

O componente `DataAnnotationsValidator` anexa o suporte à validação usando anotações de dados para o `EditContext`em cascata. Habilitar o suporte para validação usando anotações de dados requer esse gesto explícito. Para usar um sistema de validação diferente de anotações de dados, substitua o `DataAnnotationsValidator` por uma implementação personalizada. A implementação de ASP.NET Core está disponível para inspeção na fonte de referência: [DataAnnotationsValidator](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).

Blazor executa dois tipos de validação:

* A *validação de campo* é executada quando o usuário faz a Tabulação de um campo. Durante a validação de campo, o componente `DataAnnotationsValidator` associa todos os resultados de validação relatados com o campo.
* A *validação do modelo* é executada quando o usuário envia o formulário. Durante a validação do modelo, o componente `DataAnnotationsValidator` tenta determinar o campo com base no nome do membro que o resultado da validação relata. Os resultados de validação que não estão associados a um membro individual são associados ao modelo em vez de um campo.

### <a name="validation-summary-and-validation-message-components"></a>Resumo de validação e componentes de mensagem de validação

O componente `ValidationSummary` resume todas as mensagens de validação, que é semelhante ao [auxiliar de marca de Resumo de validação](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):

```razor
<ValidationSummary />
```

Mensagens de validação de saída para um modelo específico com o parâmetro `Model`:
  
```razor
<ValidationSummary Model="@_starship" />
```

O componente `ValidationMessage` exibe mensagens de validação para um campo específico, que é semelhante ao [auxiliar de marca de mensagem de validação](xref:mvc/views/working-with-forms#the-validation-message-tag-helper). Especifique o campo para validação com o atributo `For` e uma expressão lambda nomeando a propriedade do modelo:

```razor
<ValidationMessage For="@(() => _starship.MaximumAccommodation)" />
```

Os componentes `ValidationMessage` e `ValidationSummary` dão suporte a atributos arbitrários. Qualquer atributo que não corresponda a um parâmetro de componente é adicionado ao `<div>` gerado ou ao elemento `<ul>`.

### <a name="custom-validation-attributes"></a>Atributos de validação personalizados

Para garantir que um resultado de validação esteja corretamente associado a um campo ao usar um [atributo de validação personalizado](xref:mvc/models/validation#custom-attributes), passe o <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> do contexto de validação ao criar o <xref:System.ComponentModel.DataAnnotations.ValidationResult>:

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

### <a name="opno-locblazor-data-annotations-validation-package"></a>Blazor pacote de validação de anotações de dados

O [Microsoft. AspNetCore. Components. Annotations. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) é um pacote que preenche as lacunas da experiência de validação usando o componente `DataAnnotationsValidator`. O pacote está *experimental*no momento.

### <a name="compareproperty-attribute"></a>Atributo [compareproperty]

O <xref:System.ComponentModel.DataAnnotations.CompareAttribute> não funciona bem com o componente `DataAnnotationsValidator` porque ele não associa o resultado da validação a um membro específico. Isso pode resultar em comportamento inconsistente entre a validação em nível de campo e quando todo o modelo é validado em um envio. O pacote *experimental* [Microsoft. AspNetCore. Components. Annotations. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) introduz um atributo de validação adicional, `ComparePropertyAttribute`, que funciona em relação a essas limitações. Em um aplicativo Blazor, `[CompareProperty]` é uma substituição direta para o atributo `[Compare]`.

### <a name="nested-models-collection-types-and-complex-types"></a>Modelos aninhados, tipos de coleção e tipos complexos

Blazor fornece suporte para validar a entrada de formulário usando anotações de dados com o `DataAnnotationsValidator`interno. No entanto, a `DataAnnotationsValidator` só valida as propriedades de nível superior do modelo associado ao formulário que não são propriedades de tipo de coleção ou complexas.

Para validar o gráfico de objeto inteiro do modelo associado, incluindo propriedades de tipo de coleção e complexas, use o `ObjectGraphDataAnnotationsValidator` fornecido pelo pacote *experimental* [Microsoft. AspNetCore. Components. Annotations. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) :

```razor
<EditForm Model="@_model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

Anote as propriedades do modelo com `[ValidateComplexType]`. Nas classes de modelo a seguir, a classe `ShipDescription` contém anotações de dados adicionais para validar quando o modelo está associado ao formulário:

*Starship.cs*:

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

*ShipDescription.cs*:

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

### <a name="enable-the-submit-button-based-on-form-validation"></a>Habilitar o botão enviar com base na validação do formulário

Para habilitar e desabilitar o botão enviar com base na validação do formulário:

* Use o `EditContext` do formulário para atribuir o modelo quando o componente for inicializado.
* Valide o formulário no retorno de chamada de `OnFieldChanged` do contexto para habilitar e desabilitar o botão enviar.
* Desvincule o manipulador de eventos no método `Dispose`. Para obter mais informações, consulte <xref:blazor/lifecycle#component-disposal-with-idisposable>.

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

No exemplo anterior, defina `_formInvalid` como `false` se:

* O formulário é pré-carregado com valores padrão válidos.
* Você deseja que o botão enviar seja habilitado quando o formulário é carregado.

Um efeito colateral da abordagem anterior é que um componente `ValidationSummary` é preenchido com campos inválidos depois que o usuário interage com um campo. Esse cenário pode ser resolvido de uma das seguintes maneiras:

* Não use um componente `ValidationSummary` no formulário.
* Torne o componente `ValidationSummary` visível quando o botão enviar for selecionado (por exemplo, em um método `HandleValidSubmit`).

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
