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
# <a name="aspnet-core-blazor-forms-and-validation"></a>Formulários e validação de mais e ASP.NET Core

Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

Há suporte para formulários e validação com mais de um e mais, usando as [anotações de dados](xref:mvc/models/validation).

> [!NOTE]
> Os cenários de validação e formulários provavelmente serão alterados com cada versão de visualização.

O tipo `ExampleModel` a seguir define a lógica de validação usando as anotações de dados:

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

Um formulário é definido usando o `EditForm` componente. O formulário a seguir demonstra elementos típicos, componentes e código do Razor:

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

* O formulário valida a entrada do usuário no `name` campo usando a validação definida `ExampleModel` no tipo. O modelo é criado no bloco do `@code` componente e mantido em um campo privado (`exampleModel`). O campo é atribuído ao `Model` atributo `<EditForm>` do elemento.
* O `DataAnnotationsValidator` componente anexa o suporte à validação usando anotações de dados.
* O `ValidationSummary` componente resume as mensagens de validação.
* `HandleValidSubmit`é disparado quando o formulário é enviado com êxito (aprovado na validação).

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

Os componentes de entrada fornecem o comportamento padrão para validar ao editar e alterar sua classe CSS para refletir o estado do campo. Alguns componentes incluem lógica de análise útil. Por exemplo, `InputDate` e `InputNumber` manipule valores não analisáveis normalmente registrando-os como erros de validação. Os tipos que podem aceitar valores nulos também dão suporte à nulidade do campo de destino ( `int?`por exemplo,).

O tipo `Starship` a seguir define a lógica de validação usando um conjunto maior de propriedades e anotações de dados `ExampleModel`do que as anteriores:

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

O formulário a seguir valida a entrada do usuário usando a validação definida `Starship` no modelo:

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

O `DataAnnotationsValidator` componente anexa o suporte à validação usando anotações de dados para o `EditContext`em cascata. Habilitar o suporte para validação usando anotações de dados atualmente requer esse gesto explícito, mas estamos considerando tornar esse comportamento padrão que você pode então substituir. Para usar um sistema de validação diferente de anotações de dados, substitua `DataAnnotationsValidator` -o por uma implementação personalizada. A implementação de ASP.NET Core está disponível para inspeção na fonte de referência: [DataAnnotationsValidator](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/DataAnnotationsValidator.cs)/[AddDataAnnotationsValidation](https://github.com/aspnet/AspNetCore/blob/master/src/Components/Components/src/Forms/EditContextDataAnnotationsExtensions.cs). *A implementação de ASP.NET Core está sujeita a atualizações rápidas durante o período de versão de visualização.*

O `ValidationSummary` componente resume todas as mensagens de validação, que é semelhante ao [auxiliar de marca de Resumo de validação](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper).

O `ValidationMessage` componente exibe mensagens de validação para um campo específico, que é semelhante ao [auxiliar de marca de mensagem de validação](xref:mvc/views/working-with-forms#the-validation-message-tag-helper). Especifique o campo para validação com o `For` atributo e uma expressão lambda nomeando a propriedade do modelo:

```cshtml
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

Os `ValidationMessage` componentes `ValidationSummary` e oferecem suporte a atributos arbitrários. Qualquer atributo que não corresponda a um parâmetro de componente é adicionado ao `<div>` elemento `<ul>` gerado ou.
