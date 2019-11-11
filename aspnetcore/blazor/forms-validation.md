---
title: Formulários e validação de mais e ASP.NET Core
author: guardrex
description: Saiba como usar os formulários e cenários de validação de campo no mais incrivelmente.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/04/2019
uid: blazor/forms-validation
ms.openlocfilehash: 6dcc36c5133367493b476655dbdf73b75db9d168
ms.sourcegitcommit: a7bbe3890befead19440075b05b9674351f98872
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73905741"
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

O `EditForm` cria um `EditContext` como um [valor em cascata](xref:blazor/components#cascading-values-and-parameters) que controla os metadados sobre o processo de edição, incluindo quais campos foram modificados e as mensagens de validação atuais. O `EditForm` também fornece eventos convenientes para envios válidos e inválidos (`OnValidSubmit`, `OnInvalidSubmit`). Como alternativa, use `OnSubmit` para disparar a validação e verificar os valores do campo com o código de validação personalizado.

## <a name="inputtext-based-on-the-input-event"></a>InputText com base no evento de entrada

Use o componente `InputText` para criar um componente personalizado que usa o evento `input` em vez do evento `change`.

Crie um componente com a marcação a seguir e use o componente da mesma forma que `InputText` é usado:

```cshtml
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

O componente `ValidationSummary` resume todas as mensagens de validação, que é semelhante ao [auxiliar de marca de Resumo de validação](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper).

O componente `ValidationMessage` exibe mensagens de validação para um campo específico, que é semelhante ao [auxiliar de marca de mensagem de validação](xref:mvc/views/working-with-forms#the-validation-message-tag-helper). Especifique o campo para validação com o atributo `For` e uma expressão lambda nomeando a propriedade do modelo:

```cshtml
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

Os componentes `ValidationMessage` e `ValidationSummary` dão suporte a atributos arbitrários. Qualquer atributo que não corresponda a um parâmetro de componente é adicionado ao `<div>` gerado ou ao elemento `<ul>`.

::: moniker range=">= aspnetcore-3.1"

**Pacote Microsoft. AspNetCore. mais. Annotations. revalidation**

O [Microsoft. AspNetCore. mais experiente. Annotations. Validation](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.DataAnnotations.Validation) é um pacote que preenche as lacunas da experiência de validação usando o componente `DataAnnotationsValidator`. O pacote está *experimental*no momento e planejamos adicionar esses cenários à estrutura de ASP.NET Core em uma versão futura.

O componente `DataAnnotationsValidator` não valida as subpropriedades de propriedades complexas em um modelo de validação. Itens de propriedades de tipo de coleção não são validados. Para validar esses tipos, o pacote de `Microsoft.AspNetCore.Blazor.DataAnnotations.Validation` apresenta o `ValidateComplexType` atributo de validação que funciona em conjunto com o componente de `ObjectGraphDataAnnotationsValidator`. Para obter um exemplo desses tipos em uso, consulte o [exemplo de validação mais simples no repositório GitHub ASPNET/Samples ](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation).

O <xref:System.ComponentModel.DataAnnotations.CompareAttribute> não funciona bem com o componente `DataAnnotationsValidator`. O pacote de `Microsoft.AspNetCore.Blazor.DataAnnotations.Validation` introduz um atributo de validação adicional, `ComparePropertyAttribute`, que funciona em relação a essas limitações. Em um aplicativo mais incrivelmente, `ComparePropertyAttribute` é uma substituição direta para o `CompareAttribute`. Para obter mais informações, consulte [compareAttribute ignorado com OnValidSubmit EditForm (ASPNET/AspNetCore \#10643)](https://github.com/aspnet/AspNetCore/issues/10643#issuecomment-543909748).

::: moniker-end

::: moniker range="< aspnetcore-3.1"

### <a name="validation-of-complex-or-collection-type-properties"></a>Validação de propriedades de tipo de coleção ou complexas

Os atributos de validação aplicados às propriedades de um modelo são validados quando o formulário é enviado. No entanto, as propriedades de coleções ou tipos de dados complexos de um modelo não são validadas no envio de formulários pelo componente `DataAnnotationsValidator`. Para honrar os atributos de validação aninhados nesse cenário, use um componente de validação personalizado. Para obter um exemplo, consulte o [exemplo de validação mais simples no repositório do GitHub ASPNET/Samples](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Validation).

::: moniker-end
