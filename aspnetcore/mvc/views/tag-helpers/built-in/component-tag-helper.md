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
# <a name="component-tag-helper-in-aspnet-core"></a><span data-ttu-id="70a30-103">Auxiliar de marca de componente no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="70a30-103">Component Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="70a30-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="70a30-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="70a30-105">Para renderizar um componente de uma página ou exibição, use o [auxiliar de marca do componente](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).</span><span class="sxs-lookup"><span data-stu-id="70a30-105">To render a component from a page or view, use the [Component Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper).</span></span>

<span data-ttu-id="70a30-106">O auxiliar de marca de componente a seguir renderiza o componente `Counter` em uma página ou exibição:</span><span class="sxs-lookup"><span data-stu-id="70a30-106">The following Component Tag Helper renders the `Counter` component in a page or view:</span></span>

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="70a30-107">O auxiliar de marca de componente também pode passar parâmetros para componentes.</span><span class="sxs-lookup"><span data-stu-id="70a30-107">The Component Tag Helper can also pass parameters to components.</span></span> <span data-ttu-id="70a30-108">Considere o seguinte componente de `ColorfulCheckbox` que define a cor e o tamanho do rótulo da caixa de seleção:</span><span class="sxs-lookup"><span data-stu-id="70a30-108">Consider the following `ColorfulCheckbox` component that sets the check box label's color and size:</span></span>

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

<span data-ttu-id="70a30-109">Os [parâmetros de componente](xref:blazor/components#component-parameters) `Size` (`int`) e `Color` (`string`) podem ser definidos pelo auxiliar de marca do componente:</span><span class="sxs-lookup"><span data-stu-id="70a30-109">The `Size` (`int`) and `Color` (`string`) [component parameters](xref:blazor/components#component-parameters) can be set by the Component Tag Helper:</span></span>

```cshtml
<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

<span data-ttu-id="70a30-110">O HTML a seguir é renderizado na página ou exibição:</span><span class="sxs-lookup"><span data-stu-id="70a30-110">The following HTML is rendered in the page or view:</span></span>

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

<span data-ttu-id="70a30-111">A passagem de uma cadeia de caracteres entre aspas requer uma [expressão explícita do Razor](xref:mvc/views/razor#explicit-razor-expressions), como mostrado para `param-Color` no exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="70a30-111">Passing a quoted string requires an [explicit Razor expression](xref:mvc/views/razor#explicit-razor-expressions), as shown for `param-Color` in the preceding example.</span></span> <span data-ttu-id="70a30-112">O comportamento de análise do Razor para um valor de tipo de `string` não se aplica a um atributo `param-*` porque o atributo é um tipo de `object`.</span><span class="sxs-lookup"><span data-stu-id="70a30-112">The Razor parsing behavior for a `string` type value doesn't apply to a `param-*` attribute because the attribute is an `object` type.</span></span>

<span data-ttu-id="70a30-113">O tipo de parâmetro deve ser serializável em JSON, o que normalmente significa que o tipo deve ter propriedades de construtor e settable padrão.</span><span class="sxs-lookup"><span data-stu-id="70a30-113">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="70a30-114">Por exemplo, você pode especificar um valor para `Size` e `Color` no exemplo anterior porque os tipos de `Size` e `Color` são tipos primitivos (`int` e `string`), que são suportados pelo serializador JSON.</span><span class="sxs-lookup"><span data-stu-id="70a30-114">For example, you can specify a value for `Size` and `Color` in the preceding example because the types of `Size` and `Color` are primitive types (`int` and `string`), which are supported by the JSON serializer.</span></span>

<span data-ttu-id="70a30-115"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configura se o componente:</span><span class="sxs-lookup"><span data-stu-id="70a30-115"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="70a30-116">É renderizado na página.</span><span class="sxs-lookup"><span data-stu-id="70a30-116">Is prerendered into the page.</span></span>
* <span data-ttu-id="70a30-117">É renderizado como HTML estático na página ou se inclui as informações necessárias para inicializar um aplicativo mais incrivelmente do agente do usuário.</span><span class="sxs-lookup"><span data-stu-id="70a30-117">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="70a30-118">Modo de renderização</span><span class="sxs-lookup"><span data-stu-id="70a30-118">Render Mode</span></span> | <span data-ttu-id="70a30-119">Descrição</span><span class="sxs-lookup"><span data-stu-id="70a30-119">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="70a30-120">Renderiza o componente em HTML estático e inclui um marcador para um aplicativo do Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="70a30-120">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="70a30-121">Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um aplicativo Blazor.</span><span class="sxs-lookup"><span data-stu-id="70a30-121">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="70a30-122">Renderiza um marcador para um aplicativo do Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="70a30-122">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="70a30-123">A saída do componente não está incluída.</span><span class="sxs-lookup"><span data-stu-id="70a30-123">Output from the component isn't included.</span></span> <span data-ttu-id="70a30-124">Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um aplicativo Blazor.</span><span class="sxs-lookup"><span data-stu-id="70a30-124">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="70a30-125">Renderiza o componente em HTML estático.</span><span class="sxs-lookup"><span data-stu-id="70a30-125">Renders the component into static HTML.</span></span> |

<span data-ttu-id="70a30-126">Embora as páginas e exibições possam usar componentes, o inverso não é verdadeiro.</span><span class="sxs-lookup"><span data-stu-id="70a30-126">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="70a30-127">Os componentes não podem usar recursos de exibição e específicos de página, como exibições parciais e seções.</span><span class="sxs-lookup"><span data-stu-id="70a30-127">Components can't use view- and page-specific features, such as partial views and sections.</span></span> <span data-ttu-id="70a30-128">Para usar a lógica de uma exibição parcial em um componente, desfatore a lógica de exibição parcial em um componente.</span><span class="sxs-lookup"><span data-stu-id="70a30-128">To use logic from a partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="70a30-129">Não há suporte para a renderização de componentes de servidor de uma página HTML estática.</span><span class="sxs-lookup"><span data-stu-id="70a30-129">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="70a30-130">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="70a30-130">Additional resources</span></span>

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components>
