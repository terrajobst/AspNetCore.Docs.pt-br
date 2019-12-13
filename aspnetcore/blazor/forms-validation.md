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
# <a name="aspnet-core-opno-locblazor-forms-and-validation"></a>ASP.NET Core Blazor formulários e validação

Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

Há suporte para formulários e validação em Blazor usando [anotações de dados](xref:mvc/models/validation).

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

* O formulário valida a entrada do usuário no campo `name` usando a validação definida no tipo de `ExampleModel`. O modelo é criado no bloco de `@code` do componente e mantido em um campo privado (`exampleModel`). O campo é atribuído ao atributo `Model` do elemento `<EditForm>`.
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

O `EditForm` cria um `EditContext` como um [valor em cascata](xref:blazor/components#cascading-values-and-parameters) que controla os metadados sobre o processo de edição, incluindo quais campos foram modificados e as mensagens de validação atuais. O `EditForm` também fornece eventos convenientes para envios válidos e inválidos (`OnValidSubmit`, `OnInvalidSubmit`). Como alternativa, use `OnSubmit` para disparar a validação e verificar os valores do campo com o código de validação personalizado.

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

## <a name="validation-support"></a>Suporte à validação

O componente `DataAnnotationsValidator` anexa o suporte à validação usando anotações de dados para o `EditContext`em cascata. Habilitar o suporte para validação usando anotações de dados requer esse gesto explícito. Para usar um sistema de validação diferente de anotações de dados, substitua o `DataAnnotationsValidator` por uma implementação personalizada. A implementação de ASP.NET Core está disponível para inspeção na fonte de referência: [DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).

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
<ValidationSummary Model="@starship" />
```

O componente `ValidationMessage` exibe mensagens de validação para um campo específico, que é semelhante ao [auxiliar de marca de mensagem de validação](xref:mvc/views/working-with-forms#the-validation-message-tag-helper). Especifique o campo para validação com o atributo `For` e uma expressão lambda nomeando a propriedade do modelo:

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
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

::: moniker range=">= aspnetcore-3.1"

### <a name="opno-locblazor-data-annotations-validation-package"></a>Blazor pacote de validação de anotações de dados

O [Microsoft. AspNetCore.Blazor. Annotations. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) é um pacote que preenche as lacunas da experiência de validação usando o componente `DataAnnotationsValidator`. O pacote está *experimental*no momento.

### <a name="compareproperty-attribute"></a>Atributo [compareproperty]

O <xref:System.ComponentModel.DataAnnotations.CompareAttribute> não funciona bem com o componente `DataAnnotationsValidator`. O [Microsoft. AspNetCore.Blazor. Annotations.](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) o pacote *experimental* de validação introduz um atributo de validação adicional, `ComparePropertyAttribute`, que funciona em relação a essas limitações. Em um aplicativo Blazor, `[CompareProperty]` é uma substituição direta para o atributo `[Compare]`. Para obter mais informações, consulte [compareAttribute ignorado com OnValidSubmit EditForm (ASPNET/AspNetCore #10643)](https://github.com/aspnet/AspNetCore/issues/10643#issuecomment-543909748).

### <a name="nested-models-collection-types-and-complex-types"></a>Modelos aninhados, tipos de coleção e tipos complexos

Blazor fornece suporte para validar a entrada de formulário usando anotações de dados com o `DataAnnotationsValidator`interno. No entanto, a `DataAnnotationsValidator` só valida as propriedades de nível superior do modelo associado ao formulário que não são propriedades de tipo de coleção ou complexas.

Para validar o gráfico de objeto inteiro do modelo associado, incluindo propriedades de tipo de coleção e complexas, use o `ObjectGraphDataAnnotationsValidator` fornecido pelo [Microsoft. AspNetCore.Blazorexperimental. Pacote Annotations. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) :

```razor
<EditForm Model="@model" OnValidSubmit="@HandleValidSubmit">
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

::: moniker-end

::: moniker range="< aspnetcore-3.1"

### <a name="validation-of-complex-or-collection-type-properties"></a>Validação de propriedades de tipo de coleção ou complexas

Os atributos de validação aplicados às propriedades de um modelo são validados quando o formulário é enviado. No entanto, as propriedades de coleções ou tipos de dados complexos de um modelo não são validadas no envio de formulários pelo componente `DataAnnotationsValidator`. Para honrar os atributos de validação aninhados nesse cenário, use um componente de validação personalizado. Para obter um exemplo, consulte o [exemplo de validação deBlazor (ASPNET/samples)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation).

::: moniker-end
