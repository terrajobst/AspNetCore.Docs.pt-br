---
title: Auxiliar de marca de componente no ASP.NET Core
author: guardrex
ms.author: riande
description: Saiba como usar o auxiliar de marca de componente ASP.NET Core para renderizar os componentes do Razor em páginas e exibições.
ms.custom: mvc
ms.date: 03/18/2020
no-loc:
- Blazor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: 801ceb73de5bb4ef7500624e1fbddbf96d1ab89c
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "80226392"
---
# <a name="component-tag-helper-in-aspnet-core"></a>Auxiliar de marca de componente no ASP.NET Core

Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)

Para renderizar um componente de uma página ou exibição, use o [auxiliar de marca do componente](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).

O auxiliar de marca de componente a seguir renderiza o componente `Counter` em uma página ou exibição:

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

O auxiliar de marca de componente também pode passar parâmetros para componentes. Considere o seguinte componente de `ColorfulCheckbox` que define a cor e o tamanho do rótulo da caixa de seleção:

```razor
<label style="font-size:@(Size)px;color:@Color">
    <input @bind="Value"
           id="survey" 
           name="blazor" 
           type="checkbox" />
    Enjoying Blazor?
</label>

@code {
    [Parameter]
    public bool Value { get; set; }

    [Parameter]
    public int Size { get; set; } = 8;

    [Parameter]
    public string Color { get; set; }

    protected override void OnInitialized()
    {
        Size += 10;
    }
}
```

Os [parâmetros de componente](xref:blazor/components#component-parameters) `Size` (`int`) e `Color` (`string`) podem ser definidos pelo auxiliar de marca do componente:

```cshtml
<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

O HTML a seguir é renderizado na página ou exibição:

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

A passagem de uma cadeia de caracteres entre aspas requer uma [expressão explícita do Razor](xref:mvc/views/razor#explicit-razor-expressions), como mostrado para `param-Color` no exemplo anterior. O comportamento de análise do Razor para um valor de tipo de `string` não se aplica a um atributo `param-*` porque o atributo é um tipo de `object`.

O tipo de parâmetro deve ser serializável em JSON, o que normalmente significa que o tipo deve ter propriedades de construtor e settable padrão. Por exemplo, você pode especificar um valor para `Size` e `Color` no exemplo anterior porque os tipos de `Size` e `Color` são tipos primitivos (`int` e `string`), que são suportados pelo serializador JSON.

<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configura se o componente:

* É renderizado na página.
* É renderizado como HTML estático na página ou se inclui as informações necessárias para inicializar um aplicativo mais incrivelmente do agente do usuário.

| Modo de renderização | Descrição |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Renderiza o componente em HTML estático e inclui um marcador para um aplicativo do Blazor Server. Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um aplicativo Blazor. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Renderiza um marcador para um aplicativo do Blazor Server. A saída do componente não está incluída. Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um aplicativo Blazor. |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Renderiza o componente em HTML estático. |

Embora as páginas e exibições possam usar componentes, o inverso não é verdadeiro. Os componentes não podem usar recursos de exibição e específicos de página, como exibições parciais e seções. Para usar a lógica de uma exibição parcial em um componente, desfatore a lógica de exibição parcial em um componente.

Não há suporte para a renderização de componentes de servidor de uma página HTML estática.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components>
