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
# <a name="razor-components-forms-and-validation"></a>Validação e formulários de componentes do razor

Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

Formulários e validação têm suporte nos componentes do Razor usando [anotações de dados](xref:mvc/models/validation).

> [!NOTE]
> Formulários e cenários de validação são provavelmente mudarão a cada versão de visualização.

O seguinte `ExampleModel` tipo define a lógica de validação usando anotações de dados:

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

Um formulário é definido usando o `<EditForm>` componente. O formulário a seguir demonstra o código do Razor, componentes e elementos típicos:

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

* O formulário valida a entrada do usuário na `name` campo usando a validação definida no `ExampleModel` tipo. O modelo é criado no componente de `@functions` bloquear e mantidos em um campo particular (`exampleModel`). O campo é atribuído para o `Model` atributo do `<EditForm>`.
* O componente validador de anotações de dados (`<DataAnnotationsValidator>`) anexa o suporte de validação usando anotações de dados.
* O componente de resumo de validação (`<ValidationSummary>`) resume as mensagens de validação.
* `HandleValidSubmit` é acionado quando o formulário com êxito envia (foi aprovado na validação).

Um conjunto de componentes internos de entrada estão disponíveis para receber e validar a entrada do usuário. Entradas são validadas quando eles são alterados e quando um formulário é enviado. Componentes de entrada disponíveis são mostrados na tabela a seguir.

| Componente de entrada   | Renderizado como&hellip;       |
| ----------------- | ------------------------- |
| `<InputText>`     | `<input>`                 |
| `<InputTextArea>` | `<textarea>`              |
| `<InputSelect>`   | `<select>`                |
| `<InputNumber>`   | `<input type="number">`   |
| `<InputCheckbox>` | `<input type="checkbox">` |
| `<InputDate>`     | `<input type="date">`     |

Componentes de entrada fornecem o comportamento padrão para validar em Editar e alterar sua classe CSS para refletir o estado do campo. Alguns componentes incluem a lógica de análise úteis. Por exemplo, `<InputDate>` e `<InputNumber>` tratar valores não analisável normalmente, registrando-os como erros de validação. Tipos que podem aceitar valores nulos também dão suporte a nulidade do campo de destino (por exemplo, `int?`).

O seguinte `Starship` tipo define a lógica de validação usando um conjunto maior de propriedades e [anotações de dados](xref:mvc/models/validation) que o anterior `ExampleModel`:

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

O formulário a seguir valida a entrada do usuário usando a validação definida no `Starship` modelo:

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

O `<EditForm>` cria um `EditContext` como um [valor em cascata](xref:razor-components/components#cascading-values-and-parameters) que acompanha metadados sobre o processo de edição, incluindo quais campos foram modificados e as mensagens de validação atual. O `<EditForm>` também fornece eventos convenientes para válidas e inválidas envia (`OnValidSubmit`, `OnInvalidSubmit`). Como alternativa, use `OnSubmit` para acionar a validação e verificar os valores de campo com o código de validação personalizado.

O componente validador de anotações de dados (`<DataAnnotationsValidator>`) anexa o suporte de validação usando anotações de dados a cascata `EditContext`. Habilitando o suporte para a validação usando data annotations atualmente requer esse gesto explícito, mas estamos considerando fazer isso o comportamento padrão que, em seguida, você pode substituir. Para usar um sistema de validação diferente do que as anotações de dados, substitua o validador de anotações de dados com uma implementação personalizada. A implementação do ASP.NET Core está disponível para inspeção na fonte de referência: [DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/EditContextDataAnnotationsExtensions.cs). *A implementação do ASP.NET Core está sujeito a atualizações rápidas durante o período de versão de visualização.*

O componente de resumo de validação (`<ValidationSummary>`) resume todas as mensagens de validação, que é semelhante ao [auxiliar de marca de resumo de validação](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper).

O componente de mensagem de validação (`<ValidationMessage>`) exibe mensagens de validação para um campo específico, que é semelhante ao [auxiliar de marca de mensagem de validação](xref:mvc/views/working-with-forms#the-validation-message-tag-helper). Especificar o campo de validação com o `For` atributo e uma expressão lambda que nomeia a propriedade de modelo:

```cshtml
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

> [!NOTE]
> Componentes internos de entrada têm limitações que esperamos resolver em futuras versões. Por exemplo, você não pode especificar atributos arbitrários no gerado `<input>` marcas. Crie suas próprias subclasses de componente para lidar com cenários não está disponíveis.
