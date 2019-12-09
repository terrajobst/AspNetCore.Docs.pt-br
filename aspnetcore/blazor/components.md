---
title: Criar e usar ASP.NET Core componentes do Razor
author: guardrex
description: Saiba como criar e usar componentes do Razor, incluindo como associar dados, manipular eventos e gerenciar ciclos de vida do componente.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
uid: blazor/components
ms.openlocfilehash: a79202565f45b4d26e280427892ea16b33f3f853
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74943856"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="42e4a-103">Criar e usar ASP.NET Core componentes do Razor</span><span class="sxs-lookup"><span data-stu-id="42e4a-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="42e4a-104">De [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="42e4a-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="42e4a-105">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="42e4a-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

Blazor<span data-ttu-id="42e4a-106"> aplicativos são criados usando *componentes*.</span><span class="sxs-lookup"><span data-stu-id="42e4a-106"> apps are built using *components*.</span></span> <span data-ttu-id="42e4a-107">Um componente é uma parte independente da interface do usuário (IU), como uma página, uma caixa de diálogo ou um formulário.</span><span class="sxs-lookup"><span data-stu-id="42e4a-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="42e4a-108">Um componente inclui marcação HTML e a lógica de processamento necessária para injetar dados ou responder a eventos da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="42e4a-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="42e4a-109">Os componentes são flexíveis e leves.</span><span class="sxs-lookup"><span data-stu-id="42e4a-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="42e4a-110">Eles podem ser aninhados, reutilizados e compartilhados entre projetos.</span><span class="sxs-lookup"><span data-stu-id="42e4a-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="42e4a-111">Classes de componente</span><span class="sxs-lookup"><span data-stu-id="42e4a-111">Component classes</span></span>

<span data-ttu-id="42e4a-112">Os componentes são implementados em arquivos de componente do [Razor](xref:mvc/views/razor) ( *. Razor*) C# usando uma combinação de marcação HTML e.</span><span class="sxs-lookup"><span data-stu-id="42e4a-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="42e4a-113">Um componente no Blazor é formalmente referido como um *componente Razor*.</span><span class="sxs-lookup"><span data-stu-id="42e4a-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

<span data-ttu-id="42e4a-114">O nome de um componente deve começar com um caractere maiúsculo.</span><span class="sxs-lookup"><span data-stu-id="42e4a-114">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="42e4a-115">Por exemplo, *MyCoolComponent. Razor* é válido e *MyCoolComponent. Razor* é inválido.</span><span class="sxs-lookup"><span data-stu-id="42e4a-115">For example, *MyCoolComponent.razor* is valid, and *myCoolComponent.razor* is invalid.</span></span>

<span data-ttu-id="42e4a-116">A interface do usuário para um componente é definida usando HTML.</span><span class="sxs-lookup"><span data-stu-id="42e4a-116">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="42e4a-117">A lógica de renderização dinâmica (por exemplo, loops, condicionais, expressões) é adicionada usando uma sintaxe de C# inserida chamada [Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="42e4a-117">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="42e4a-118">Quando um aplicativo é compilado, a marcação HTML e C# a lógica de renderização são convertidas em uma classe de componente.</span><span class="sxs-lookup"><span data-stu-id="42e4a-118">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="42e4a-119">O nome da classe gerada corresponde ao nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="42e4a-119">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="42e4a-120">Os membros da classe de componente são definidos em um bloco `@code`.</span><span class="sxs-lookup"><span data-stu-id="42e4a-120">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="42e4a-121">No bloco de `@code`, estado do componente (Propriedades, campos) é especificado com métodos para manipulação de eventos ou para definir outra lógica de componente.</span><span class="sxs-lookup"><span data-stu-id="42e4a-121">In the `@code` block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="42e4a-122">Mais de um bloco de `@code` é permitido.</span><span class="sxs-lookup"><span data-stu-id="42e4a-122">More than one `@code` block is permissible.</span></span>

> [!NOTE]
> <span data-ttu-id="42e4a-123">Nas visualizações anteriores do ASP.NET Core 3,0, os blocos de `@functions` foram usados para a mesma finalidade que os blocos de `@code` nos componentes do Razor.</span><span class="sxs-lookup"><span data-stu-id="42e4a-123">In prior previews of ASP.NET Core 3.0, `@functions` blocks were used for the same purpose as `@code` blocks in Razor components.</span></span> <span data-ttu-id="42e4a-124">os blocos de `@functions` continuam a funcionar em componentes do Razor, mas é recomendável usar o bloco de `@code` no ASP.NET Core 3,0 Preview 6 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="42e4a-124">`@functions` blocks continue to function in Razor components, but we recommend using the `@code` block in ASP.NET Core 3.0 Preview 6 or later.</span></span>

<span data-ttu-id="42e4a-125">Os membros do componente podem ser usados como parte da lógica de renderização do C# componente usando expressões que começam com `@`.</span><span class="sxs-lookup"><span data-stu-id="42e4a-125">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="42e4a-126">Por exemplo, um C# campo é renderizado pela prefixação `@` ao nome do campo.</span><span class="sxs-lookup"><span data-stu-id="42e4a-126">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="42e4a-127">O exemplo a seguir avalia e renderiza:</span><span class="sxs-lookup"><span data-stu-id="42e4a-127">The following example evaluates and renders:</span></span>

* <span data-ttu-id="42e4a-128">`_headingFontStyle` ao valor da propriedade CSS para `font-style`.</span><span class="sxs-lookup"><span data-stu-id="42e4a-128">`_headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="42e4a-129">`_headingText` o conteúdo do elemento `<h1>`.</span><span class="sxs-lookup"><span data-stu-id="42e4a-129">`_headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="42e4a-130">Depois que o componente é processado inicialmente, o componente regenera sua árvore de renderização em resposta a eventos.</span><span class="sxs-lookup"><span data-stu-id="42e4a-130">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> <span data-ttu-id="42e4a-131">em seguida, Blazor compara a nova árvore de renderização com a anterior e aplica quaisquer modificações à Modelo de Objeto do Documento do navegador (DOM).</span><span class="sxs-lookup"><span data-stu-id="42e4a-131">Blazor then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="42e4a-132">Os componentes são C# classes comuns e podem ser colocados em qualquer lugar dentro de um projeto.</span><span class="sxs-lookup"><span data-stu-id="42e4a-132">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="42e4a-133">Os componentes que produzem páginas da Web geralmente residem na pasta *páginas* .</span><span class="sxs-lookup"><span data-stu-id="42e4a-133">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="42e4a-134">Os componentes que não são de página são frequentemente colocados na pasta *compartilhada* ou em uma pasta personalizada adicionada ao projeto.</span><span class="sxs-lookup"><span data-stu-id="42e4a-134">Non-page components are frequently placed in the *Shared* folder or a custom folder added to the project.</span></span> <span data-ttu-id="42e4a-135">Para usar uma pasta personalizada, adicione o namespace da pasta personalizada ao componente pai ou ao arquivo *_Imports. Razor* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="42e4a-135">To use a custom folder, add the custom folder's namespace to either the parent component or to the app's *_Imports.razor* file.</span></span> <span data-ttu-id="42e4a-136">Por exemplo, o namespace a seguir torna os componentes em uma pasta *componentes* disponíveis quando o namespace raiz do aplicativo é `WebApplication`:</span><span class="sxs-lookup"><span data-stu-id="42e4a-136">For example, the following namespace makes components in a *Components* folder available when the app's root namespace is `WebApplication`:</span></span>

```razor
@using WebApplication.Components
```

## <a name="integrate-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="42e4a-137">Integrar componentes em aplicativos Razor Pages e MVC</span><span class="sxs-lookup"><span data-stu-id="42e4a-137">Integrate components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="42e4a-138">Use componentes com os aplicativos Razor Pages e MVC existentes.</span><span class="sxs-lookup"><span data-stu-id="42e4a-138">Use components with existing Razor Pages and MVC apps.</span></span> <span data-ttu-id="42e4a-139">Não é necessário reescrever páginas ou exibições existentes para usar os componentes do Razor.</span><span class="sxs-lookup"><span data-stu-id="42e4a-139">There's no need to rewrite existing pages or views to use Razor components.</span></span> <span data-ttu-id="42e4a-140">Quando a página ou a exibição é renderizada, os componentes são renderizados ao mesmo tempo.</span><span class="sxs-lookup"><span data-stu-id="42e4a-140">When the page or view is rendered, components are prerendered at the same time.</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="42e4a-141">Para renderizar um componente de uma página ou exibição, use o `Component` o auxiliar de marca:</span><span class="sxs-lookup"><span data-stu-id="42e4a-141">To render a component from a page or view, use the `Component` Tag Helper:</span></span>

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

<span data-ttu-id="42e4a-142">Há suporte para a passagem de parâmetros (por exemplo, `IncrementAmount` no exemplo anterior).</span><span class="sxs-lookup"><span data-stu-id="42e4a-142">Passing parameters (for example, `IncrementAmount` in the preceding example) is supported.</span></span>

<span data-ttu-id="42e4a-143">`RenderMode` configura se o componente:</span><span class="sxs-lookup"><span data-stu-id="42e4a-143">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="42e4a-144">É renderizado na página.</span><span class="sxs-lookup"><span data-stu-id="42e4a-144">Is prerendered into the page.</span></span>
* <span data-ttu-id="42e4a-145">É renderizado como HTML estático na página ou se inclui as informações necessárias para inicializar um aplicativo Blazor do agente do usuário.</span><span class="sxs-lookup"><span data-stu-id="42e4a-145">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="42e4a-146">Descrição</span><span class="sxs-lookup"><span data-stu-id="42e4a-146">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="42e4a-147">Renderiza o componente em HTML estático e inclui um marcador para um aplicativo do Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="42e4a-147">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="42e4a-148">Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um aplicativo Blazor.</span><span class="sxs-lookup"><span data-stu-id="42e4a-148">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="42e4a-149">Renderiza um marcador para um aplicativo do Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="42e4a-149">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="42e4a-150">A saída do componente não está incluída.</span><span class="sxs-lookup"><span data-stu-id="42e4a-150">Output from the component isn't included.</span></span> <span data-ttu-id="42e4a-151">Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um aplicativo Blazor.</span><span class="sxs-lookup"><span data-stu-id="42e4a-151">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="42e4a-152">Renderiza o componente em HTML estático.</span><span class="sxs-lookup"><span data-stu-id="42e4a-152">Renders the component into static HTML.</span></span> |

<span data-ttu-id="42e4a-153">Embora as páginas e exibições possam usar componentes, o inverso não é verdadeiro.</span><span class="sxs-lookup"><span data-stu-id="42e4a-153">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="42e4a-154">Os componentes não podem usar cenários específicos de exibição e de página, como exibições parciais e seções.</span><span class="sxs-lookup"><span data-stu-id="42e4a-154">Components can't use view- and page-specific scenarios, such as partial views and sections.</span></span> <span data-ttu-id="42e4a-155">Para usar a lógica da exibição parcial em um componente, desfatore a lógica de exibição parcial em um componente.</span><span class="sxs-lookup"><span data-stu-id="42e4a-155">To use logic from partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="42e4a-156">Não há suporte para a renderização de componentes de servidor de uma página HTML estática.</span><span class="sxs-lookup"><span data-stu-id="42e4a-156">Rendering server components from a static HTML page isn't supported.</span></span>

<span data-ttu-id="42e4a-157">Para obter mais informações sobre como os componentes são renderizados, estado do componente e o auxiliar de marca de `Component`, consulte <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="42e4a-157">For more information on how components are rendered, component state, and the `Component` Tag Helper, see <xref:blazor/hosting-models>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.1"

<span data-ttu-id="42e4a-158">Para renderizar um componente de uma página ou exibição, use o `RenderComponentAsync<TComponent>` método auxiliar HTML:</span><span class="sxs-lookup"><span data-stu-id="42e4a-158">To render a component from a page or view, use the `RenderComponentAsync<TComponent>` HTML helper method:</span></span>

```cshtml
@(await Html.RenderComponentAsync<MyComponent>(RenderMode.ServerPrerendered))
```

<span data-ttu-id="42e4a-159">`RenderMode` configura se o componente:</span><span class="sxs-lookup"><span data-stu-id="42e4a-159">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="42e4a-160">É renderizado na página.</span><span class="sxs-lookup"><span data-stu-id="42e4a-160">Is prerendered into the page.</span></span>
* <span data-ttu-id="42e4a-161">É renderizado como HTML estático na página ou se inclui as informações necessárias para inicializar um aplicativo Blazor do agente do usuário.</span><span class="sxs-lookup"><span data-stu-id="42e4a-161">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="42e4a-162">Descrição</span><span class="sxs-lookup"><span data-stu-id="42e4a-162">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="42e4a-163">Renderiza o componente em HTML estático e inclui um marcador para um aplicativo do Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="42e4a-163">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="42e4a-164">Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um aplicativo Blazor.</span><span class="sxs-lookup"><span data-stu-id="42e4a-164">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="42e4a-165">Não há suporte para parâmetros.</span><span class="sxs-lookup"><span data-stu-id="42e4a-165">Parameters aren't supported.</span></span> |
| `Server`            | <span data-ttu-id="42e4a-166">Renderiza um marcador para um aplicativo do Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="42e4a-166">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="42e4a-167">A saída do componente não está incluída.</span><span class="sxs-lookup"><span data-stu-id="42e4a-167">Output from the component isn't included.</span></span> <span data-ttu-id="42e4a-168">Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um aplicativo Blazor.</span><span class="sxs-lookup"><span data-stu-id="42e4a-168">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="42e4a-169">Não há suporte para parâmetros.</span><span class="sxs-lookup"><span data-stu-id="42e4a-169">Parameters aren't supported.</span></span> |
| `Static`            | <span data-ttu-id="42e4a-170">Renderiza o componente em HTML estático.</span><span class="sxs-lookup"><span data-stu-id="42e4a-170">Renders the component into static HTML.</span></span> <span data-ttu-id="42e4a-171">Há suporte para os parâmetros.</span><span class="sxs-lookup"><span data-stu-id="42e4a-171">Parameters are supported.</span></span> |

<span data-ttu-id="42e4a-172">Embora as páginas e exibições possam usar componentes, o inverso não é verdadeiro.</span><span class="sxs-lookup"><span data-stu-id="42e4a-172">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="42e4a-173">Os componentes não podem usar cenários específicos de exibição e de página, como exibições parciais e seções.</span><span class="sxs-lookup"><span data-stu-id="42e4a-173">Components can't use view- and page-specific scenarios, such as partial views and sections.</span></span> <span data-ttu-id="42e4a-174">Para usar a lógica da exibição parcial em um componente, desfatore a lógica de exibição parcial em um componente.</span><span class="sxs-lookup"><span data-stu-id="42e4a-174">To use logic from partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="42e4a-175">Não há suporte para a renderização de componentes de servidor de uma página HTML estática.</span><span class="sxs-lookup"><span data-stu-id="42e4a-175">Rendering server components from a static HTML page isn't supported.</span></span>

<span data-ttu-id="42e4a-176">Para obter mais informações sobre como os componentes são renderizados, o estado do componente e o `RenderComponentAsync` auxiliar HTML, consulte <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="42e4a-176">For more information on how components are rendered, component state, and the `RenderComponentAsync` HTML Helper, see <xref:blazor/hosting-models>.</span></span>

::: moniker-end

## <a name="use-components"></a><span data-ttu-id="42e4a-177">Usar componentes</span><span class="sxs-lookup"><span data-stu-id="42e4a-177">Use components</span></span>

<span data-ttu-id="42e4a-178">Os componentes podem incluir outros componentes, declarando-os usando a sintaxe do elemento HTML.</span><span class="sxs-lookup"><span data-stu-id="42e4a-178">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="42e4a-179">A marcação para uso de um componente é semelhante a uma marca HTML, em que o nome da marca é o tipo de componente.</span><span class="sxs-lookup"><span data-stu-id="42e4a-179">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="42e4a-180">A associação de atributo diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="42e4a-180">Attribute binding is case sensitive.</span></span> <span data-ttu-id="42e4a-181">Por exemplo, `@bind` é válido e `@Bind` é inválido.</span><span class="sxs-lookup"><span data-stu-id="42e4a-181">For example, `@bind` is valid, and `@Bind` is invalid.</span></span>

<span data-ttu-id="42e4a-182">A marcação a seguir no *index. Razor* renderiza uma instância de `HeadingComponent`:</span><span class="sxs-lookup"><span data-stu-id="42e4a-182">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="42e4a-183">*Componentes/HeadingComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="42e4a-183">*Components/HeadingComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

<span data-ttu-id="42e4a-184">Se um componente contiver um elemento HTML com uma letra maiúscula ou minúscula que não corresponda a um nome de componente, um aviso será emitido indicando que o elemento tem um nome inesperado.</span><span class="sxs-lookup"><span data-stu-id="42e4a-184">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="42e4a-185">Adicionar uma instrução `@using` para o namespace do componente disponibiliza o componente, o que remove o aviso.</span><span class="sxs-lookup"><span data-stu-id="42e4a-185">Adding an `@using` statement for the component's namespace makes the component available, which removes the warning.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="42e4a-186">Parâmetros do componente</span><span class="sxs-lookup"><span data-stu-id="42e4a-186">Component parameters</span></span>

<span data-ttu-id="42e4a-187">Os componentes podem ter *parâmetros de componente*, que são definidos usando propriedades públicas na classe de componente com o atributo `[Parameter]`.</span><span class="sxs-lookup"><span data-stu-id="42e4a-187">Components can have *component parameters*, which are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="42e4a-188">Use atributos para especificar argumentos para um componente na marcação.</span><span class="sxs-lookup"><span data-stu-id="42e4a-188">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="42e4a-189">*Componentes/ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="42e4a-189">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=11-12)]

<span data-ttu-id="42e4a-190">No exemplo a seguir do aplicativo de exemplo, o `ParentComponent` define o valor da propriedade `Title` do `ChildComponent`.</span><span class="sxs-lookup"><span data-stu-id="42e4a-190">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="42e4a-191">*Páginas/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="42e4a-191">*Pages/ParentComponent.razor*:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClick="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

...
```

## <a name="child-content"></a><span data-ttu-id="42e4a-192">Conteúdo filho</span><span class="sxs-lookup"><span data-stu-id="42e4a-192">Child content</span></span>

<span data-ttu-id="42e4a-193">Os componentes podem definir o conteúdo de outro componente.</span><span class="sxs-lookup"><span data-stu-id="42e4a-193">Components can set the content of another component.</span></span> <span data-ttu-id="42e4a-194">O componente de atribuição fornece o conteúdo entre as marcas que especificam o componente de recebimento.</span><span class="sxs-lookup"><span data-stu-id="42e4a-194">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="42e4a-195">No exemplo a seguir, o `ChildComponent` tem uma propriedade `ChildContent` que representa um `RenderFragment`, que representa um segmento de interface do usuário a ser renderizado.</span><span class="sxs-lookup"><span data-stu-id="42e4a-195">In the following example, the `ChildComponent` has a `ChildContent` property that represents a `RenderFragment`, which represents a segment of UI to render.</span></span> <span data-ttu-id="42e4a-196">O valor de `ChildContent` é posicionado na marcação do componente onde o conteúdo deve ser renderizado.</span><span class="sxs-lookup"><span data-stu-id="42e4a-196">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="42e4a-197">O valor de `ChildContent` é recebido do componente pai e renderizado dentro do `panel-body`do painel de inicialização.</span><span class="sxs-lookup"><span data-stu-id="42e4a-197">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="42e4a-198">*Componentes/ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="42e4a-198">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="42e4a-199">A propriedade que recebe o conteúdo de `RenderFragment` deve ser nomeada `ChildContent` por convenção.</span><span class="sxs-lookup"><span data-stu-id="42e4a-199">The property receiving the `RenderFragment` content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="42e4a-200">O `ParentComponent` no aplicativo de exemplo pode fornecer conteúdo para renderizar o `ChildComponent` colocando o conteúdo dentro das marcas de `<ChildComponent>`.</span><span class="sxs-lookup"><span data-stu-id="42e4a-200">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="42e4a-201">*Páginas/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="42e4a-201">*Pages/ParentComponent.razor*:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClick="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

...
```

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="42e4a-202">Atributo nivelamento e parâmetros arbitrários</span><span class="sxs-lookup"><span data-stu-id="42e4a-202">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="42e4a-203">Os componentes podem capturar e renderizar atributos adicionais além dos parâmetros declarados do componente.</span><span class="sxs-lookup"><span data-stu-id="42e4a-203">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="42e4a-204">Atributos adicionais podem ser capturados em um dicionário e, em seguida, *splatted* em um elemento quando o componente é renderizado usando a diretiva [`@attributes`](xref:mvc/views/razor#attributes) Razor.</span><span class="sxs-lookup"><span data-stu-id="42e4a-204">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`](xref:mvc/views/razor#attributes) Razor directive.</span></span> <span data-ttu-id="42e4a-205">Esse cenário é útil ao definir um componente que produz um elemento de marcação que dá suporte a uma variedade de personalizações.</span><span class="sxs-lookup"><span data-stu-id="42e4a-205">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="42e4a-206">Por exemplo, pode ser entediante definir atributos separadamente para um `<input>` que ofereça suporte a muitos parâmetros.</span><span class="sxs-lookup"><span data-stu-id="42e4a-206">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="42e4a-207">No exemplo a seguir, o primeiro elemento `<input>` (`id="useIndividualParams"`) usa parâmetros de componente individuais, enquanto o segundo elemento `<input>` (`id="useAttributesDict"`) usa o atributo nivelamento:</span><span class="sxs-lookup"><span data-stu-id="42e4a-207">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

```razor
<input id="useIndividualParams"
       maxlength="@Maxlength"
       placeholder="@Placeholder"
       required="@Required"
       size="@Size" />

<input id="useAttributesDict"
       @attributes="InputAttributes" />

@code {
    [Parameter]
    public string Maxlength { get; set; } = "10";

    [Parameter]
    public string Placeholder { get; set; } = "Input placeholder text";

    [Parameter]
    public string Required { get; set; } = "required";

    [Parameter]
    public string Size { get; set; } = "50";

    [Parameter]
    public Dictionary<string, object> InputAttributes { get; set; } =
        new Dictionary<string, object>()
        {
            { "maxlength", "10" },
            { "placeholder", "Input placeholder text" },
            { "required", "required" },
            { "size", "50" }
        };
}
```

<span data-ttu-id="42e4a-208">O tipo do parâmetro deve implementar `IEnumerable<KeyValuePair<string, object>>` com chaves de cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="42e4a-208">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="42e4a-209">Usar `IReadOnlyDictionary<string, object>` também é uma opção nesse cenário.</span><span class="sxs-lookup"><span data-stu-id="42e4a-209">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="42e4a-210">Os elementos `<input>` renderizados usando ambas as abordagens são idênticos:</span><span class="sxs-lookup"><span data-stu-id="42e4a-210">The rendered `<input>` elements using both approaches is identical:</span></span>

```html
<input id="useIndividualParams"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">

<input id="useAttributesDict"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">
```

<span data-ttu-id="42e4a-211">Para aceitar atributos arbitrários, defina um parâmetro de componente usando o atributo `[Parameter]` com a propriedade `CaptureUnmatchedValues` definida como `true`:</span><span class="sxs-lookup"><span data-stu-id="42e4a-211">To accept arbitrary attributes, define a component parameter using the `[Parameter]` attribute with the `CaptureUnmatchedValues` property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="42e4a-212">A propriedade `CaptureUnmatchedValues` em `[Parameter]` permite que o parâmetro corresponda a todos os atributos que não correspondem a nenhum outro parâmetro.</span><span class="sxs-lookup"><span data-stu-id="42e4a-212">The `CaptureUnmatchedValues` property on `[Parameter]` allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="42e4a-213">Um componente só pode definir um único parâmetro com `CaptureUnmatchedValues`.</span><span class="sxs-lookup"><span data-stu-id="42e4a-213">A component can only define a single parameter with `CaptureUnmatchedValues`.</span></span> <span data-ttu-id="42e4a-214">O tipo de propriedade usado com `CaptureUnmatchedValues` deve ser atribuível de `Dictionary<string, object>` com chaves de cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="42e4a-214">The property type used with `CaptureUnmatchedValues` must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="42e4a-215">`IEnumerable<KeyValuePair<string, object>>` ou `IReadOnlyDictionary<string, object>` também são opções neste cenário.</span><span class="sxs-lookup"><span data-stu-id="42e4a-215">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="42e4a-216">A posição de `@attributes` em relação à posição dos atributos do elemento é importante.</span><span class="sxs-lookup"><span data-stu-id="42e4a-216">The position of `@attributes` relative to the position of element attributes is important.</span></span> <span data-ttu-id="42e4a-217">Quando `@attributes` são splatted no elemento, os atributos são processados da direita para a esquerda (último a primeiro).</span><span class="sxs-lookup"><span data-stu-id="42e4a-217">When `@attributes` are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="42e4a-218">Considere o exemplo a seguir de um componente que consome um componente `Child`:</span><span class="sxs-lookup"><span data-stu-id="42e4a-218">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="42e4a-219">*ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="42e4a-219">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="42e4a-220">*ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="42e4a-220">*ChildComponent.razor*:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="42e4a-221">O atributo `extra` do componente de `Child` é definido à direita de `@attributes`.</span><span class="sxs-lookup"><span data-stu-id="42e4a-221">The `Child` component's `extra` attribute is set to the right of `@attributes`.</span></span> <span data-ttu-id="42e4a-222">O `<div>` renderizado do componente `Parent` contém `extra="5"` quando passa pelo atributo adicional porque os atributos são processados da direita para a esquerda (último a primeiro):</span><span class="sxs-lookup"><span data-stu-id="42e4a-222">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="42e4a-223">No exemplo a seguir, a ordem de `extra` e `@attributes` é revertida na `<div>`do componente `Child`:</span><span class="sxs-lookup"><span data-stu-id="42e4a-223">In the following example, the order of `extra` and `@attributes` is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="42e4a-224">*ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="42e4a-224">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="42e4a-225">*ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="42e4a-225">*ChildComponent.razor*:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="42e4a-226">O `<div>` renderizado no componente `Parent` contém `extra="10"` quando passado pelo atributo adicional:</span><span class="sxs-lookup"><span data-stu-id="42e4a-226">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="data-binding"></a><span data-ttu-id="42e4a-227">Associação de dados</span><span class="sxs-lookup"><span data-stu-id="42e4a-227">Data binding</span></span>

<span data-ttu-id="42e4a-228">A vinculação de dados para os componentes e os elementos DOM é realizada com o atributo [`@bind`](xref:mvc/views/razor#bind) .</span><span class="sxs-lookup"><span data-stu-id="42e4a-228">Data binding to both components and DOM elements is accomplished with the [`@bind`](xref:mvc/views/razor#bind) attribute.</span></span> <span data-ttu-id="42e4a-229">O exemplo a seguir associa uma propriedade `CurrentValue` ao valor da caixa de texto:</span><span class="sxs-lookup"><span data-stu-id="42e4a-229">The following example binds a `CurrentValue` property to the text box's value:</span></span>

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="42e4a-230">Quando a caixa de texto perde o foco, o valor da propriedade é atualizado.</span><span class="sxs-lookup"><span data-stu-id="42e4a-230">When the text box loses focus, the property's value is updated.</span></span>

<span data-ttu-id="42e4a-231">A caixa de texto é atualizada na interface do usuário somente quando o componente é renderizado, não em resposta à alteração do valor da propriedade.</span><span class="sxs-lookup"><span data-stu-id="42e4a-231">The text box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="42e4a-232">Como os componentes são renderizados após a execução do código do manipulador de eventos, as atualizações de propriedade *geralmente* são refletidas na interface do usuário imediatamente após um manipulador de eventos ser disparado.</span><span class="sxs-lookup"><span data-stu-id="42e4a-232">Since components render themselves after event handler code executes, property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="42e4a-233">O uso de `@bind` com a propriedade `CurrentValue` (`<input @bind="CurrentValue" />`) é essencialmente equivalente ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="42e4a-233">Using `@bind` with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="42e4a-234">Quando o componente é renderizado, a `value` do elemento input é proveniente da propriedade `CurrentValue`.</span><span class="sxs-lookup"><span data-stu-id="42e4a-234">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="42e4a-235">Quando o usuário digita na caixa de texto e altera o foco do elemento, o evento `onchange` é acionado e a propriedade `CurrentValue` é definida como o valor alterado.</span><span class="sxs-lookup"><span data-stu-id="42e4a-235">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="42e4a-236">Na realidade, a geração de código é mais complexa porque `@bind` trata casos em que conversões de tipo são executadas.</span><span class="sxs-lookup"><span data-stu-id="42e4a-236">In reality, the code generation is more complex because `@bind` handles cases where type conversions are performed.</span></span> <span data-ttu-id="42e4a-237">Em princípio, `@bind` associa o valor atual de uma expressão a um atributo `value` e manipula as alterações usando o manipulador registrado.</span><span class="sxs-lookup"><span data-stu-id="42e4a-237">In principle, `@bind` associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="42e4a-238">Além de manipular `onchange` eventos com a sintaxe `@bind`, uma propriedade ou um campo pode ser associado usando outros eventos, especificando um atributo [`@bind-value`](xref:mvc/views/razor#bind) com um parâmetro `event` ([`@bind-value:event`](xref:mvc/views/razor#bind)).</span><span class="sxs-lookup"><span data-stu-id="42e4a-238">In addition to handling `onchange` events with `@bind` syntax, a property or field can be bound using other events by specifying an [`@bind-value`](xref:mvc/views/razor#bind) attribute with an `event` parameter ([`@bind-value:event`](xref:mvc/views/razor#bind)).</span></span> <span data-ttu-id="42e4a-239">O exemplo a seguir associa a propriedade `CurrentValue` para o evento `oninput`:</span><span class="sxs-lookup"><span data-stu-id="42e4a-239">The following example binds the `CurrentValue` property for the `oninput` event:</span></span>

```razor
<input @bind-value="CurrentValue" @bind-value:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="42e4a-240">Ao contrário de `onchange`, que é disparado quando o elemento perde o foco, `oninput` é acionado quando o valor da caixa de texto é alterado.</span><span class="sxs-lookup"><span data-stu-id="42e4a-240">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="42e4a-241">**Valores não analisáveis**</span><span class="sxs-lookup"><span data-stu-id="42e4a-241">**Unparsable values**</span></span>

<span data-ttu-id="42e4a-242">Quando um usuário fornece um valor não analisável para um elemento de ligação de valores, o valor não analisável é revertido automaticamente para seu valor anterior quando o evento de ligação é disparado.</span><span class="sxs-lookup"><span data-stu-id="42e4a-242">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="42e4a-243">Considere o seguinte cenário:</span><span class="sxs-lookup"><span data-stu-id="42e4a-243">Consider the following scenario:</span></span>

* <span data-ttu-id="42e4a-244">Um elemento `<input>` está associado a um tipo de `int` com um valor inicial de `123`:</span><span class="sxs-lookup"><span data-stu-id="42e4a-244">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* <span data-ttu-id="42e4a-245">O usuário atualiza o valor do elemento para `123.45` na página e altera o foco do elemento.</span><span class="sxs-lookup"><span data-stu-id="42e4a-245">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="42e4a-246">No cenário anterior, o valor do elemento é revertido para `123`.</span><span class="sxs-lookup"><span data-stu-id="42e4a-246">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="42e4a-247">Quando o valor `123.45` é rejeitado em favor do valor original de `123`, o usuário entende que seu valor não foi aceito.</span><span class="sxs-lookup"><span data-stu-id="42e4a-247">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="42e4a-248">Por padrão, a associação aplica-se ao evento de `onchange` do elemento (`@bind="{PROPERTY OR FIELD}"`).</span><span class="sxs-lookup"><span data-stu-id="42e4a-248">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="42e4a-249">Use `@bind-value="{PROPERTY OR FIELD}" @bind-value:event={EVENT}` para definir um evento diferente.</span><span class="sxs-lookup"><span data-stu-id="42e4a-249">Use `@bind-value="{PROPERTY OR FIELD}" @bind-value:event={EVENT}` to set a different event.</span></span> <span data-ttu-id="42e4a-250">Para o evento de `oninput` (`@bind-value:event="oninput"`), a reversão ocorre após qualquer pressionamento de tecla que introduz um valor não analisável.</span><span class="sxs-lookup"><span data-stu-id="42e4a-250">For the `oninput` event (`@bind-value:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="42e4a-251">Ao direcionar o evento de `oninput` com um tipo de associação de `int`, um usuário é impedido de digitar um caractere `.`.</span><span class="sxs-lookup"><span data-stu-id="42e4a-251">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="42e4a-252">Um caractere `.` é removido imediatamente e, portanto, o usuário recebe comentários imediatos de que apenas números inteiros são permitidos.</span><span class="sxs-lookup"><span data-stu-id="42e4a-252">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="42e4a-253">Há cenários em que a reversão do valor no evento `oninput` não é ideal, por exemplo, quando o usuário deve ter permissão para limpar um valor de `<input>` não analisável.</span><span class="sxs-lookup"><span data-stu-id="42e4a-253">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="42e4a-254">As alternativas incluem:</span><span class="sxs-lookup"><span data-stu-id="42e4a-254">Alternatives include:</span></span>

* <span data-ttu-id="42e4a-255">Não use o evento `oninput`.</span><span class="sxs-lookup"><span data-stu-id="42e4a-255">Don't use the `oninput` event.</span></span> <span data-ttu-id="42e4a-256">Use o evento de `onchange` padrão (`@bind="{PROPERTY OR FIELD}"`), em que um valor inválido não é revertido até que o elemento perca o foco.</span><span class="sxs-lookup"><span data-stu-id="42e4a-256">Use the default `onchange` event (`@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="42e4a-257">Associar a um tipo anulável, como `int?` ou `string`, e fornecer uma lógica personalizada para manipular entradas inválidas.</span><span class="sxs-lookup"><span data-stu-id="42e4a-257">Bind to a nullable type, such as `int?` or `string`, and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="42e4a-258">Use um [componente de validação de formulário](xref:blazor/forms-validation), como `InputNumber` ou `InputDate`.</span><span class="sxs-lookup"><span data-stu-id="42e4a-258">Use a [form validation component](xref:blazor/forms-validation), such as `InputNumber` or `InputDate`.</span></span> <span data-ttu-id="42e4a-259">Os componentes de validação de formulário têm suporte interno para gerenciar entradas inválidas.</span><span class="sxs-lookup"><span data-stu-id="42e4a-259">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="42e4a-260">Componentes de validação de formulário:</span><span class="sxs-lookup"><span data-stu-id="42e4a-260">Form validation components:</span></span>
  * <span data-ttu-id="42e4a-261">Permitir que o usuário forneça erros de entrada e de validação inválidos no `EditContext`associado.</span><span class="sxs-lookup"><span data-stu-id="42e4a-261">Permit the user to provide invalid input and receive validation errors on the associated `EditContext`.</span></span>
  * <span data-ttu-id="42e4a-262">Exibir erros de validação na interface de usuário sem interferir no usuário inserindo dados adicionais do WebForms.</span><span class="sxs-lookup"><span data-stu-id="42e4a-262">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

<span data-ttu-id="42e4a-263">**Globalização**</span><span class="sxs-lookup"><span data-stu-id="42e4a-263">**Globalization**</span></span>

<span data-ttu-id="42e4a-264">`@bind` valores são formatados para exibição e analisados usando as regras da cultura atual.</span><span class="sxs-lookup"><span data-stu-id="42e4a-264">`@bind` values are formatted for display and parsed using the current culture's rules.</span></span>

<span data-ttu-id="42e4a-265">A cultura atual pode ser acessada a partir da propriedade <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="42e4a-265">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="42e4a-266">[CultureInfo. InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) é usado para os seguintes tipos de campo (`<input type="{TYPE}" />`):</span><span class="sxs-lookup"><span data-stu-id="42e4a-266">[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="42e4a-267">Os tipos de campo anteriores:</span><span class="sxs-lookup"><span data-stu-id="42e4a-267">The preceding field types:</span></span>

* <span data-ttu-id="42e4a-268">São exibidos usando suas regras de formatação baseadas em navegador apropriadas.</span><span class="sxs-lookup"><span data-stu-id="42e4a-268">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="42e4a-269">Não pode conter texto de forma livre.</span><span class="sxs-lookup"><span data-stu-id="42e4a-269">Can't contain free-form text.</span></span>
* <span data-ttu-id="42e4a-270">Fornecer características de interação do usuário com base na implementação do navegador.</span><span class="sxs-lookup"><span data-stu-id="42e4a-270">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="42e4a-271">Os seguintes tipos de campo têm requisitos de formatação específicos e atualmente não têm suporte do Blazor porque eles não têm suporte em todos os principais navegadores:</span><span class="sxs-lookup"><span data-stu-id="42e4a-271">The following field types have specific formatting requirements and aren't currently supported by Blazor because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="42e4a-272">`@bind` dá suporte ao parâmetro `@bind:culture` para fornecer um <xref:System.Globalization.CultureInfo?displayProperty=fullName> para análise e formatação de um valor.</span><span class="sxs-lookup"><span data-stu-id="42e4a-272">`@bind` supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="42e4a-273">Não é recomendável especificar uma cultura ao usar os tipos de campo `date` e `number`.</span><span class="sxs-lookup"><span data-stu-id="42e4a-273">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="42e4a-274">`date` e `number` têm suporte interno a Blazor que fornece a cultura necessária.</span><span class="sxs-lookup"><span data-stu-id="42e4a-274">`date` and `number` have built-in Blazor support that provides the required culture.</span></span>

<span data-ttu-id="42e4a-275">Para obter informações sobre como definir a cultura do usuário, consulte a seção [localização](#localization) .</span><span class="sxs-lookup"><span data-stu-id="42e4a-275">For information on how to set the user's culture, see the [Localization](#localization) section.</span></span>

<span data-ttu-id="42e4a-276">**Formatar cadeias de caracteres**</span><span class="sxs-lookup"><span data-stu-id="42e4a-276">**Format strings**</span></span>

<span data-ttu-id="42e4a-277">A vinculação de dados funciona com <xref:System.DateTime> cadeias de caracteres de formato usando [`@bind:format`](xref:mvc/views/razor#bind).</span><span class="sxs-lookup"><span data-stu-id="42e4a-277">Data binding works with <xref:System.DateTime> format strings using [`@bind:format`](xref:mvc/views/razor#bind).</span></span> <span data-ttu-id="42e4a-278">Outras expressões de formato, como formatos de moeda ou número, não estão disponíveis no momento.</span><span class="sxs-lookup"><span data-stu-id="42e4a-278">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="42e4a-279">No código anterior, o tipo de campo (`type`) do elemento `<input>` tem como padrão `text`.</span><span class="sxs-lookup"><span data-stu-id="42e4a-279">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="42e4a-280">`@bind:format` tem suporte para ligar os seguintes tipos .NET:</span><span class="sxs-lookup"><span data-stu-id="42e4a-280">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="42e4a-281"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="42e4a-281"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="42e4a-282"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="42e4a-282"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="42e4a-283">O atributo `@bind:format` especifica o formato de data a ser aplicado ao `value` do elemento `<input>`.</span><span class="sxs-lookup"><span data-stu-id="42e4a-283">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="42e4a-284">O formato também é usado para analisar o valor quando ocorre um evento `onchange`.</span><span class="sxs-lookup"><span data-stu-id="42e4a-284">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="42e4a-285">Não é recomendável especificar um formato para o tipo de campo `date` porque Blazor tem suporte interno para formatar datas.</span><span class="sxs-lookup"><span data-stu-id="42e4a-285">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="42e4a-286">Apesar da recomendação, use apenas o formato de data `yyyy-MM-dd` para que a associação funcione corretamente se um formato for fornecido com o tipo de campo `date`:</span><span class="sxs-lookup"><span data-stu-id="42e4a-286">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to work correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

<span data-ttu-id="42e4a-287">**Parâmetros do componente**</span><span class="sxs-lookup"><span data-stu-id="42e4a-287">**Component parameters**</span></span>

<span data-ttu-id="42e4a-288">A associação reconhece os parâmetros do componente, em que `@bind-{property}` pode associar um valor de propriedade entre componentes.</span><span class="sxs-lookup"><span data-stu-id="42e4a-288">Binding recognizes component parameters, where `@bind-{property}` can bind a property value across components.</span></span>

<span data-ttu-id="42e4a-289">O componente filho a seguir (`ChildComponent`) tem um parâmetro de componente `Year` e `YearChanged` retorno de chamada:</span><span class="sxs-lookup"><span data-stu-id="42e4a-289">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

```razor
<h2>Child Component</h2>

<p>Year: @Year</p>

@code {
    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }
}
```

<span data-ttu-id="42e4a-290">`EventCallback<T>` é explicado na seção [EventCallback](#eventcallback) .</span><span class="sxs-lookup"><span data-stu-id="42e4a-290">`EventCallback<T>` is explained in the [EventCallback](#eventcallback) section.</span></span>

<span data-ttu-id="42e4a-291">O componente pai a seguir usa `ChildComponent` e associa o parâmetro `ParentYear` do pai ao parâmetro `Year` no componente filho:</span><span class="sxs-lookup"><span data-stu-id="42e4a-291">The following parent component uses `ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<p>ParentYear: @ParentYear</p>

<ChildComponent @bind-Year="ParentYear" />

<button class="btn btn-primary" @onclick="ChangeTheYear">
    Change Year to 1986
</button>

@code {
    [Parameter]
    public int ParentYear { get; set; } = 1978;

    private void ChangeTheYear()
    {
        ParentYear = 1986;
    }
}
```

<span data-ttu-id="42e4a-292">Carregar o `ParentComponent` produz a seguinte marcação:</span><span class="sxs-lookup"><span data-stu-id="42e4a-292">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="42e4a-293">Se o valor da propriedade `ParentYear` for alterado selecionando o botão na `ParentComponent`, a propriedade `Year` do `ChildComponent` será atualizada.</span><span class="sxs-lookup"><span data-stu-id="42e4a-293">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="42e4a-294">O novo valor de `Year` é renderizado na interface do usuário quando a `ParentComponent` é reprocessada:</span><span class="sxs-lookup"><span data-stu-id="42e4a-294">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="42e4a-295">O parâmetro `Year` é acoplável porque tem um evento complementar `YearChanged` que corresponde ao tipo do parâmetro `Year`.</span><span class="sxs-lookup"><span data-stu-id="42e4a-295">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="42e4a-296">Por convenção, `<ChildComponent @bind-Year="ParentYear" />` é essencialmente equivalente a escrever:</span><span class="sxs-lookup"><span data-stu-id="42e4a-296">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="42e4a-297">Em geral, uma propriedade pode ser associada a um manipulador de eventos correspondente usando `@bind-property:event` atributo.</span><span class="sxs-lookup"><span data-stu-id="42e4a-297">In general, a property can be bound to a corresponding event handler using `@bind-property:event` attribute.</span></span> <span data-ttu-id="42e4a-298">Por exemplo, a propriedade `MyProp` pode ser associada a `MyEventHandler` usando os dois atributos a seguir:</span><span class="sxs-lookup"><span data-stu-id="42e4a-298">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="event-handling"></a><span data-ttu-id="42e4a-299">Manipulação de eventos</span><span class="sxs-lookup"><span data-stu-id="42e4a-299">Event handling</span></span>

<span data-ttu-id="42e4a-300">Os componentes do Razor fornecem recursos de manipulação de eventos.</span><span class="sxs-lookup"><span data-stu-id="42e4a-300">Razor components provide event handling features.</span></span> <span data-ttu-id="42e4a-301">Para um atributo de elemento HTML chamado `on{EVENT}` (por exemplo, `onclick` e `onsubmit`) com um valor de tipo delegado, os componentes do Razor tratam o valor do atributo como um manipulador de eventos.</span><span class="sxs-lookup"><span data-stu-id="42e4a-301">For an HTML element attribute named `on{EVENT}` (for example, `onclick` and `onsubmit`) with a delegate-typed value, Razor components treats the attribute's value as an event handler.</span></span> <span data-ttu-id="42e4a-302">O nome do atributo é sempre formatado [`@on{EVENT}`](xref:mvc/views/razor#onevent).</span><span class="sxs-lookup"><span data-stu-id="42e4a-302">The attribute's name is always formatted [`@on{EVENT}`](xref:mvc/views/razor#onevent).</span></span>

<span data-ttu-id="42e4a-303">O código a seguir chama o método `UpdateHeading` quando o botão é selecionado na interface do usuário:</span><span class="sxs-lookup"><span data-stu-id="42e4a-303">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private void UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

<span data-ttu-id="42e4a-304">O código a seguir chama o método `CheckChanged` quando a caixa de seleção é alterada na interface do usuário:</span><span class="sxs-lookup"><span data-stu-id="42e4a-304">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="42e4a-305">Os manipuladores de eventos também podem ser assíncronos e retornar um <xref:System.Threading.Tasks.Task>.</span><span class="sxs-lookup"><span data-stu-id="42e4a-305">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="42e4a-306">Não é necessário chamar [StateHasChanged](xref:blazor/lifecycle#state-changes)manualmente.</span><span class="sxs-lookup"><span data-stu-id="42e4a-306">There's no need to manually call [StateHasChanged](xref:blazor/lifecycle#state-changes).</span></span> <span data-ttu-id="42e4a-307">As exceções são registradas quando ocorrem.</span><span class="sxs-lookup"><span data-stu-id="42e4a-307">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="42e4a-308">No exemplo a seguir, `UpdateHeading` é chamado de forma assíncrona quando o botão é selecionado:</span><span class="sxs-lookup"><span data-stu-id="42e4a-308">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

```razor
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

### <a name="event-argument-types"></a><span data-ttu-id="42e4a-309">Tipos de argumento de evento</span><span class="sxs-lookup"><span data-stu-id="42e4a-309">Event argument types</span></span>

<span data-ttu-id="42e4a-310">Para alguns eventos, são permitidos tipos de argumento de evento.</span><span class="sxs-lookup"><span data-stu-id="42e4a-310">For some events, event argument types are permitted.</span></span> <span data-ttu-id="42e4a-311">Se o acesso a um desses tipos de evento não for necessário, ele não será necessário na chamada do método.</span><span class="sxs-lookup"><span data-stu-id="42e4a-311">If access to one of these event types isn't necessary, it isn't required in the method call.</span></span>

<span data-ttu-id="42e4a-312">Os `EventArgs` com suporte são mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="42e4a-312">Supported `EventArgs` are shown in the following table.</span></span>

| <span data-ttu-id="42e4a-313">Event</span><span class="sxs-lookup"><span data-stu-id="42e4a-313">Event</span></span>            | <span data-ttu-id="42e4a-314">Classe</span><span class="sxs-lookup"><span data-stu-id="42e4a-314">Class</span></span>                | <span data-ttu-id="42e4a-315">Eventos e observações do DOM</span><span class="sxs-lookup"><span data-stu-id="42e4a-315">DOM events and notes</span></span> |
| ---------------- | -------------------- | -------------------- |
| <span data-ttu-id="42e4a-316">Área de transferência</span><span class="sxs-lookup"><span data-stu-id="42e4a-316">Clipboard</span></span>        | `ClipboardEventArgs` | <span data-ttu-id="42e4a-317">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="42e4a-317">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="42e4a-318">Arrastar</span><span class="sxs-lookup"><span data-stu-id="42e4a-318">Drag</span></span>             | `DragEventArgs`      | <span data-ttu-id="42e4a-319">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="42e4a-319">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="42e4a-320">`DataTransfer` e `DataTransferItem` manter os dados do item arrastados.</span><span class="sxs-lookup"><span data-stu-id="42e4a-320">`DataTransfer` and `DataTransferItem` hold dragged item data.</span></span> |
| <span data-ttu-id="42e4a-321">Erro do</span><span class="sxs-lookup"><span data-stu-id="42e4a-321">Error</span></span>            | `ErrorEventArgs`     | `onerror` |
| <span data-ttu-id="42e4a-322">Event</span><span class="sxs-lookup"><span data-stu-id="42e4a-322">Event</span></span>            | `EventArgs`          | <span data-ttu-id="42e4a-323">*Geral*</span><span class="sxs-lookup"><span data-stu-id="42e4a-323">*General*</span></span><br><span data-ttu-id="42e4a-324">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="42e4a-324">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="42e4a-325">*Área de transferência*</span><span class="sxs-lookup"><span data-stu-id="42e4a-325">*Clipboard*</span></span><br><span data-ttu-id="42e4a-326">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="42e4a-326">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="42e4a-327">*Entrada*</span><span class="sxs-lookup"><span data-stu-id="42e4a-327">*Input*</span></span><br><span data-ttu-id="42e4a-328">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="42e4a-328">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="42e4a-329">*Mídia*</span><span class="sxs-lookup"><span data-stu-id="42e4a-329">*Media*</span></span><br><span data-ttu-id="42e4a-330">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="42e4a-330">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span> |
| <span data-ttu-id="42e4a-331">Foco</span><span class="sxs-lookup"><span data-stu-id="42e4a-331">Focus</span></span>            | `FocusEventArgs`     | <span data-ttu-id="42e4a-332">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="42e4a-332">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="42e4a-333">Não inclui suporte para `relatedTarget`.</span><span class="sxs-lookup"><span data-stu-id="42e4a-333">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="42e4a-334">Entrada</span><span class="sxs-lookup"><span data-stu-id="42e4a-334">Input</span></span>            | `ChangeEventArgs`    | <span data-ttu-id="42e4a-335">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="42e4a-335">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="42e4a-336">Teclado</span><span class="sxs-lookup"><span data-stu-id="42e4a-336">Keyboard</span></span>         | `KeyboardEventArgs`  | <span data-ttu-id="42e4a-337">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="42e4a-337">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="42e4a-338">Mouse</span><span class="sxs-lookup"><span data-stu-id="42e4a-338">Mouse</span></span>            | `MouseEventArgs`     | <span data-ttu-id="42e4a-339">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="42e4a-339">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="42e4a-340">Ponteiro do mouse</span><span class="sxs-lookup"><span data-stu-id="42e4a-340">Mouse pointer</span></span>    | `PointerEventArgs`   | <span data-ttu-id="42e4a-341">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="42e4a-341">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="42e4a-342">Botão de rolagem do mouse</span><span class="sxs-lookup"><span data-stu-id="42e4a-342">Mouse wheel</span></span>      | `WheelEventArgs`     | <span data-ttu-id="42e4a-343">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="42e4a-343">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="42e4a-344">Progresso</span><span class="sxs-lookup"><span data-stu-id="42e4a-344">Progress</span></span>         | `ProgressEventArgs`  | <span data-ttu-id="42e4a-345">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="42e4a-345">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="42e4a-346">Toque</span><span class="sxs-lookup"><span data-stu-id="42e4a-346">Touch</span></span>            | `TouchEventArgs`     | <span data-ttu-id="42e4a-347">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="42e4a-347">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="42e4a-348">`TouchPoint` representa um único ponto de contato em um dispositivo sensível ao toque.</span><span class="sxs-lookup"><span data-stu-id="42e4a-348">`TouchPoint` represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="42e4a-349">Para obter informações sobre as propriedades e o comportamento de manipulação de eventos dos eventos na tabela anterior, consulte [classes EventArgs na fonte de referência (ramificação ASPNET/AspNetCore Release/3.0)](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="42e4a-349">For information on the properties and event handling behavior of the events in the preceding table, see [EventArgs classes in the reference source (aspnet/AspNetCore release/3.0 branch)](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Components/Web/src/Web).</span></span>

### <a name="lambda-expressions"></a><span data-ttu-id="42e4a-350">Expressões lambda</span><span class="sxs-lookup"><span data-stu-id="42e4a-350">Lambda expressions</span></span>

<span data-ttu-id="42e4a-351">As expressões lambda também podem ser usadas:</span><span class="sxs-lookup"><span data-stu-id="42e4a-351">Lambda expressions can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="42e4a-352">Geralmente, é conveniente fechar valores adicionais, como ao iterar em um conjunto de elementos.</span><span class="sxs-lookup"><span data-stu-id="42e4a-352">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="42e4a-353">O exemplo a seguir cria três botões, cada um dos quais chamadas `UpdateHeading` passando um argumento de evento (`MouseEventArgs`) e seu número de botão (`buttonNumber`) quando selecionado na interface do usuário:</span><span class="sxs-lookup"><span data-stu-id="42e4a-353">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (`MouseEventArgs`) and its button number (`buttonNumber`) when selected in the UI:</span></span>

```razor
<h2>@message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string message = "Select a button to learn its position.";

    private void UpdateHeading(MouseEventArgs e, int buttonNumber)
    {
        message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> <span data-ttu-id="42e4a-354">**Não** use a variável de loop (`i`) em um loop de `for` diretamente em uma expressão lambda.</span><span class="sxs-lookup"><span data-stu-id="42e4a-354">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="42e4a-355">Caso contrário, a mesma variável é usada por todas as expressões lambda, fazendo com que `i`valor seja o mesmo em todos os lambdas.</span><span class="sxs-lookup"><span data-stu-id="42e4a-355">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="42e4a-356">Sempre Capture seu valor em uma variável local (`buttonNumber` no exemplo anterior) e, em seguida, use-o.</span><span class="sxs-lookup"><span data-stu-id="42e4a-356">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

### <a name="eventcallback"></a><span data-ttu-id="42e4a-357">EventCallback</span><span class="sxs-lookup"><span data-stu-id="42e4a-357">EventCallback</span></span>

<span data-ttu-id="42e4a-358">Um cenário comum com componentes aninhados é o desejo de executar o método de um componente pai quando ocorre um evento de componente filho&mdash;por exemplo, quando um evento de `onclick` ocorre no filho.</span><span class="sxs-lookup"><span data-stu-id="42e4a-358">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs&mdash;for example, when an `onclick` event occurs in the child.</span></span> <span data-ttu-id="42e4a-359">Para expor eventos entre componentes, use um `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="42e4a-359">To expose events across components, use an `EventCallback`.</span></span> <span data-ttu-id="42e4a-360">Um componente pai pode atribuir um método de retorno de chamada para o `EventCallback`de um componente filho.</span><span class="sxs-lookup"><span data-stu-id="42e4a-360">A parent component can assign a callback method to a child component's `EventCallback`.</span></span>

<span data-ttu-id="42e4a-361">O `ChildComponent` no aplicativo de exemplo (*Components/ChildComponent. Razor*) demonstra como o manipulador de `onclick` de um botão é configurado para receber um delegado de `EventCallback` da `ParentComponent`de exemplo.</span><span class="sxs-lookup"><span data-stu-id="42e4a-361">The `ChildComponent` in the sample app (*Components/ChildComponent.razor*) demonstrates how a button's `onclick` handler is set up to receive an `EventCallback` delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="42e4a-362">O `EventCallback` é digitado com `MouseEventArgs`, que é apropriado para um evento de `onclick` de um dispositivo periférico:</span><span class="sxs-lookup"><span data-stu-id="42e4a-362">The `EventCallback` is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="42e4a-363">O `ParentComponent` define o `EventCallback<T>` do filho como seu método `ShowMessage`.</span><span class="sxs-lookup"><span data-stu-id="42e4a-363">The `ParentComponent` sets the child's `EventCallback<T>` to its `ShowMessage` method.</span></span>

<span data-ttu-id="42e4a-364">*Páginas/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="42e4a-364">*Pages/ParentComponent.razor*:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClick="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

<p><b>@messageText</b></p>

@code {
    private string messageText;

    private void ShowMessage(MouseEventArgs e)
    {
        messageText = $"Blaze a new trail with Blazor! ({e.ScreenX}, {e.ScreenY})";
    }
}
```

<span data-ttu-id="42e4a-365">Quando o botão estiver selecionado na `ChildComponent`:</span><span class="sxs-lookup"><span data-stu-id="42e4a-365">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="42e4a-366">O método `ShowMessage` do `ParentComponent`é chamado.</span><span class="sxs-lookup"><span data-stu-id="42e4a-366">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="42e4a-367">`messageText` é atualizado e exibido no `ParentComponent`.</span><span class="sxs-lookup"><span data-stu-id="42e4a-367">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="42e4a-368">Uma chamada para [StateHasChanged](xref:blazor/lifecycle#state-changes) não é necessária no método do retorno de chamada (`ShowMessage`).</span><span class="sxs-lookup"><span data-stu-id="42e4a-368">A call to [StateHasChanged](xref:blazor/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="42e4a-369">`StateHasChanged` é chamado automaticamente para renderizar novamente o `ParentComponent`, assim como os eventos filho disparam o reprocessamento de componentes em manipuladores de eventos que são executados dentro do filho.</span><span class="sxs-lookup"><span data-stu-id="42e4a-369">`StateHasChanged` is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="42e4a-370">`EventCallback` e `EventCallback<T>` permitir delegados assíncronos.</span><span class="sxs-lookup"><span data-stu-id="42e4a-370">`EventCallback` and `EventCallback<T>` permit asynchronous delegates.</span></span> <span data-ttu-id="42e4a-371">`EventCallback<T>` é fortemente tipado e requer um tipo de argumento específico.</span><span class="sxs-lookup"><span data-stu-id="42e4a-371">`EventCallback<T>` is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="42e4a-372">`EventCallback` é digitado de forma fraca e permite qualquer tipo de argumento.</span><span class="sxs-lookup"><span data-stu-id="42e4a-372">`EventCallback` is weakly typed and allows any argument type.</span></span>

```razor
<p><b>@messageText</b></p>

@{ var message = "Default Text"; }

<ChildComponent 
    OnClick="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />

@code {
    private string messageText;
}
```

<span data-ttu-id="42e4a-373">Invoque um `EventCallback` ou `EventCallback<T>` com `InvokeAsync` e aguardar a <xref:System.Threading.Tasks.Task>:</span><span class="sxs-lookup"><span data-stu-id="42e4a-373">Invoke an `EventCallback` or `EventCallback<T>` with `InvokeAsync` and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="42e4a-374">Use `EventCallback` e `EventCallback<T>` para manipulação de eventos e parâmetros de componente de associação.</span><span class="sxs-lookup"><span data-stu-id="42e4a-374">Use `EventCallback` and `EventCallback<T>` for event handling and binding component parameters.</span></span>

<span data-ttu-id="42e4a-375">Prefira a `EventCallback<T>` com rigidez de tipos sobre `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="42e4a-375">Prefer the strongly typed `EventCallback<T>` over `EventCallback`.</span></span> <span data-ttu-id="42e4a-376">`EventCallback<T>` fornece melhores comentários de erro para os usuários do componente.</span><span class="sxs-lookup"><span data-stu-id="42e4a-376">`EventCallback<T>` provides better error feedback to users of the component.</span></span> <span data-ttu-id="42e4a-377">Semelhante a outros manipuladores de eventos de interface do usuário, especificar o parâmetro de evento é opcional.</span><span class="sxs-lookup"><span data-stu-id="42e4a-377">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="42e4a-378">Use `EventCallback` quando não houver valor passado para o retorno de chamada.</span><span class="sxs-lookup"><span data-stu-id="42e4a-378">Use `EventCallback` when there's no value passed to the callback.</span></span>

::: moniker range=">= aspnetcore-3.1"

### <a name="prevent-default-actions"></a><span data-ttu-id="42e4a-379">Impedir ações padrão</span><span class="sxs-lookup"><span data-stu-id="42e4a-379">Prevent default actions</span></span>

<span data-ttu-id="42e4a-380">Use o atributo de diretiva [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) para evitar a ação padrão para um evento.</span><span class="sxs-lookup"><span data-stu-id="42e4a-380">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="42e4a-381">Quando uma chave é selecionada em um dispositivo de entrada e o foco do elemento está em uma caixa de texto, um navegador normalmente exibe o caractere da chave na caixa de texto.</span><span class="sxs-lookup"><span data-stu-id="42e4a-381">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="42e4a-382">No exemplo a seguir, o comportamento padrão é impedido pela especificação do atributo de diretiva `@onkeypress:preventDefault`.</span><span class="sxs-lookup"><span data-stu-id="42e4a-382">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="42e4a-383">O contador é incrementado e a chave de **+** não é capturada no valor do elemento de `<input>`:</span><span class="sxs-lookup"><span data-stu-id="42e4a-383">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

```razor
<input value="@_count" @onkeypress="KeyHandler" @onkeypress:preventDefault />

@code {
    private int _count = 0;

    private void KeyHandler(KeyboardEventArgs e)
    {
        if (e.Key == "+")
        {
            _count++;
        }
    }
}
```

<span data-ttu-id="42e4a-384">Especificar o atributo `@on{EVENT}:preventDefault` sem um valor é equivalente a `@on{EVENT}:preventDefault="true"`.</span><span class="sxs-lookup"><span data-stu-id="42e4a-384">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="42e4a-385">O valor do atributo também pode ser uma expressão.</span><span class="sxs-lookup"><span data-stu-id="42e4a-385">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="42e4a-386">No exemplo a seguir, `_shouldPreventDefault` é um campo de `bool` definido como `true` ou `false`:</span><span class="sxs-lookup"><span data-stu-id="42e4a-386">In the following example, `_shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="_shouldPreventDefault" />
```

<span data-ttu-id="42e4a-387">Um manipulador de eventos não é necessário para impedir a ação padrão.</span><span class="sxs-lookup"><span data-stu-id="42e4a-387">An event handler isn't required to prevent the default action.</span></span> <span data-ttu-id="42e4a-388">O manipulador de eventos e a prevenção de cenários de ação padrão podem ser usados de forma independente.</span><span class="sxs-lookup"><span data-stu-id="42e4a-388">The event handler and prevent default action scenarios can be used independently.</span></span>

### <a name="stop-event-propagation"></a><span data-ttu-id="42e4a-389">Parar a propagação do evento</span><span class="sxs-lookup"><span data-stu-id="42e4a-389">Stop event propagation</span></span>

<span data-ttu-id="42e4a-390">Use o atributo de diretiva [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) para parar a propagação do evento.</span><span class="sxs-lookup"><span data-stu-id="42e4a-390">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="42e4a-391">No exemplo a seguir, marcar a caixa de seleção impede que eventos de clique do segundo filho `<div>` sejam propagados para o `<div>`pai:</span><span class="sxs-lookup"><span data-stu-id="42e4a-391">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

```razor
<label>
    <input @bind="_stopPropagation" type="checkbox" />
    Stop Propagation
</label>

<div @onclick="OnSelectParentDiv">
    <h3>Parent div</h3>

    <div @onclick="OnSelectChildDiv">
        Child div that doesn't stop propagation when selected.
    </div>

    <div @onclick="OnSelectChildDiv" @onclick:stopPropagation="_stopPropagation">
        Child div that stops propagation when selected.
    </div>
</div>

@code {
    private bool _stopPropagation = false;

    private void OnSelectParentDiv() => 
        Console.WriteLine($"The parent div was selected. {DateTime.Now}");
    private void OnSelectChildDiv() => 
        Console.WriteLine($"A child div was selected. {DateTime.Now}");
}
```

::: moniker-end

## <a name="chained-bind"></a><span data-ttu-id="42e4a-392">Associação encadeada</span><span class="sxs-lookup"><span data-stu-id="42e4a-392">Chained bind</span></span>

<span data-ttu-id="42e4a-393">Um cenário comum é encadear um parâmetro de associação de dados a um elemento de página na saída do componente.</span><span class="sxs-lookup"><span data-stu-id="42e4a-393">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="42e4a-394">Esse cenário é chamado de *Associação encadeada* porque vários níveis de associação ocorrem simultaneamente.</span><span class="sxs-lookup"><span data-stu-id="42e4a-394">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="42e4a-395">Uma associação encadeada não pode ser implementada com `@bind` sintaxe no elemento da página.</span><span class="sxs-lookup"><span data-stu-id="42e4a-395">A chained bind can't be implemented with `@bind` syntax in the page's element.</span></span> <span data-ttu-id="42e4a-396">O manipulador de eventos e o valor devem ser especificados separadamente.</span><span class="sxs-lookup"><span data-stu-id="42e4a-396">The event handler and value must be specified separately.</span></span> <span data-ttu-id="42e4a-397">Um componente pai, no entanto, pode usar `@bind` sintaxe com o parâmetro do componente.</span><span class="sxs-lookup"><span data-stu-id="42e4a-397">A parent component, however, can use `@bind` syntax with the component's parameter.</span></span>

<span data-ttu-id="42e4a-398">O componente `PasswordField` a seguir (*passwordField. Razor*):</span><span class="sxs-lookup"><span data-stu-id="42e4a-398">The following `PasswordField` component (*PasswordField.razor*):</span></span>

* <span data-ttu-id="42e4a-399">Define um valor de elemento de `<input>` para uma propriedade `Password`.</span><span class="sxs-lookup"><span data-stu-id="42e4a-399">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="42e4a-400">Expõe as alterações da propriedade `Password` para um componente pai com um [EventCallback](#eventcallback).</span><span class="sxs-lookup"><span data-stu-id="42e4a-400">Exposes changes of the `Password` property to a parent component with an [EventCallback](#eventcallback).</span></span>

```razor
Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool showPassword;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

<span data-ttu-id="42e4a-401">O componente `PasswordField` é usado em outro componente:</span><span class="sxs-lookup"><span data-stu-id="42e4a-401">The `PasswordField` component is used in another component:</span></span>

```razor
<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

<span data-ttu-id="42e4a-402">Para executar verificações ou interceptar erros na senha no exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="42e4a-402">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="42e4a-403">Crie um campo de backup para `Password` (`password` no código de exemplo a seguir).</span><span class="sxs-lookup"><span data-stu-id="42e4a-403">Create a backing field for `Password` (`password` in the following example code).</span></span>
* <span data-ttu-id="42e4a-404">Execute os erros de verificação ou interceptação no setter de `Password`.</span><span class="sxs-lookup"><span data-stu-id="42e4a-404">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="42e4a-405">O exemplo a seguir fornecerá comentários imediatos para o usuário se um espaço for usado no valor da senha:</span><span class="sxs-lookup"><span data-stu-id="42e4a-405">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

```razor
Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@validationMessage</span>

@code {
    private bool showPassword;
    private string password;
    private string validationMessage;

    [Parameter]
    public string Password
    {
        get { return password ?? string.Empty; }
        set
        {
            if (password != value)
            {
                if (value.Contains(' '))
                {
                    validationMessage = "Spaces not allowed!";
                }
                else
                {
                    password = value;
                    validationMessage = string.Empty;
                }
            }
        }
    }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

## <a name="capture-references-to-components"></a><span data-ttu-id="42e4a-406">Capturar referências a componentes</span><span class="sxs-lookup"><span data-stu-id="42e4a-406">Capture references to components</span></span>

<span data-ttu-id="42e4a-407">As referências de componente fornecem uma maneira de fazer referência a uma instância de componente para que você possa emitir comandos para essa instância, como `Show` ou `Reset`.</span><span class="sxs-lookup"><span data-stu-id="42e4a-407">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="42e4a-408">Para capturar uma referência de componente:</span><span class="sxs-lookup"><span data-stu-id="42e4a-408">To capture a component reference:</span></span>

* <span data-ttu-id="42e4a-409">Adicione um atributo [`@ref`](xref:mvc/views/razor#ref) ao componente filho.</span><span class="sxs-lookup"><span data-stu-id="42e4a-409">Add an [`@ref`](xref:mvc/views/razor#ref) attribute to the child component.</span></span>
* <span data-ttu-id="42e4a-410">Defina um campo com o mesmo tipo do componente filho.</span><span class="sxs-lookup"><span data-stu-id="42e4a-410">Define a field with the same type as the child component.</span></span>

```razor
<MyLoginDialog @ref="loginDialog" ... />

@code {
    private MyLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

<span data-ttu-id="42e4a-411">Quando o componente é renderizado, o campo `loginDialog` é populado com a instância de componente filho `MyLoginDialog`.</span><span class="sxs-lookup"><span data-stu-id="42e4a-411">When the component is rendered, the `loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="42e4a-412">Em seguida, você pode invocar os métodos .NET na instância do componente.</span><span class="sxs-lookup"><span data-stu-id="42e4a-412">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="42e4a-413">A variável `loginDialog` é populada apenas depois que o componente é renderizado e sua saída inclui o elemento `MyLoginDialog`.</span><span class="sxs-lookup"><span data-stu-id="42e4a-413">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="42e4a-414">Até esse ponto, não há nada a fazer referência.</span><span class="sxs-lookup"><span data-stu-id="42e4a-414">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="42e4a-415">Para manipular referências de componentes após a conclusão da renderização do componente, use os [métodos OnAfterRenderAsync ou OnAfterRender](xref:blazor/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="42e4a-415">To manipulate components references after the component has finished rendering, use the [OnAfterRenderAsync or OnAfterRender methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="42e4a-416">Embora a captura de referências de componente use uma sintaxe semelhante à [captura de referências de elemento](xref:blazor/javascript-interop#capture-references-to-elements), ela não é um recurso de [interoperabilidade do JavaScript](xref:blazor/javascript-interop) .</span><span class="sxs-lookup"><span data-stu-id="42e4a-416">While capturing component references use a similar syntax to [capturing element references](xref:blazor/javascript-interop#capture-references-to-elements), it isn't a [JavaScript interop](xref:blazor/javascript-interop) feature.</span></span> <span data-ttu-id="42e4a-417">As referências de componente não são passadas para o código JavaScript&mdash;elas são usadas apenas no código .NET.</span><span class="sxs-lookup"><span data-stu-id="42e4a-417">Component references aren't passed to JavaScript code&mdash;they're only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="42e4a-418">Não **use referências** de componente para converter o estado dos componentes filho.</span><span class="sxs-lookup"><span data-stu-id="42e4a-418">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="42e4a-419">Em vez disso, use parâmetros declarativos normais para passar dados para componentes filho.</span><span class="sxs-lookup"><span data-stu-id="42e4a-419">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="42e4a-420">O uso de parâmetros declarativos normais resulta em componentes filho que são reprocessados nos horários corretos automaticamente.</span><span class="sxs-lookup"><span data-stu-id="42e4a-420">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="42e4a-421">Invocar métodos de componente externamente para atualizar o estado</span><span class="sxs-lookup"><span data-stu-id="42e4a-421">Invoke component methods externally to update state</span></span>

Blazor<span data-ttu-id="42e4a-422"> usa uma `SynchronizationContext` para impor um único thread lógico de execução.</span><span class="sxs-lookup"><span data-stu-id="42e4a-422"> uses a `SynchronizationContext` to enforce a single logical thread of execution.</span></span> <span data-ttu-id="42e4a-423">Os métodos de [ciclo de vida](xref:blazor/lifecycle) de um componente e quaisquer retornos de chamada de evento gerados por Blazor são executados nesse `SynchronizationContext`.</span><span class="sxs-lookup"><span data-stu-id="42e4a-423">A component's [lifecycle methods](xref:blazor/lifecycle) and any event callbacks that are raised by Blazor are executed on this `SynchronizationContext`.</span></span> <span data-ttu-id="42e4a-424">No caso de um componente precisar ser atualizado com base em um evento externo, como um temporizador ou outras notificações, use o método `InvokeAsync`, que será expedido para o `SynchronizationContext`de Blazor.</span><span class="sxs-lookup"><span data-stu-id="42e4a-424">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which will dispatch to Blazor's `SynchronizationContext`.</span></span>

<span data-ttu-id="42e4a-425">Por exemplo, considere um *serviço de notificação* que pode notificar qualquer componente de escuta do estado atualizado:</span><span class="sxs-lookup"><span data-stu-id="42e4a-425">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

```csharp
public class NotifierService
{
    // Can be called from anywhere
    public async Task Update(string key, int value)
    {
        if (Notify != null)
        {
            await Notify.Invoke(key, value);
        }
    }

    public event Func<string, int, Task> Notify;
}
```

<span data-ttu-id="42e4a-426">Uso do `NotifierService` para atualizar um componente:</span><span class="sxs-lookup"><span data-stu-id="42e4a-426">Usage of the `NotifierService` to update a component:</span></span>

```razor
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @lastNotification.key = @lastNotification.value</p>

@code {
    private (string key, int value) lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

<span data-ttu-id="42e4a-427">No exemplo anterior, `NotifierService` invoca o método `OnNotify` do componente fora da `SynchronizationContext`do Blazor.</span><span class="sxs-lookup"><span data-stu-id="42e4a-427">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's `SynchronizationContext`.</span></span> <span data-ttu-id="42e4a-428">`InvokeAsync` é usado para alternar para o contexto correto e enfileirar uma renderização.</span><span class="sxs-lookup"><span data-stu-id="42e4a-428">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="42e4a-429">Use \@chave para controlar a preservação de elementos e componentes</span><span class="sxs-lookup"><span data-stu-id="42e4a-429">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="42e4a-430">Ao renderizar uma lista de elementos ou componentes e os elementos ou componentes subsequentemente mudam, o algoritmo de diferenciação do Blazordeve decidir qual dos elementos ou componentes anteriores podem ser mantidos e como os objetos de modelo devem ser mapeados para eles.</span><span class="sxs-lookup"><span data-stu-id="42e4a-430">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="42e4a-431">Normalmente, esse processo é automático e pode ser ignorado, mas há casos em que você talvez queira controlar o processo.</span><span class="sxs-lookup"><span data-stu-id="42e4a-431">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="42e4a-432">Considere o exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="42e4a-432">Consider the following example:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="42e4a-433">O conteúdo da coleção de `People` pode ser alterado com entradas inseridas, excluídas ou reordenadas.</span><span class="sxs-lookup"><span data-stu-id="42e4a-433">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="42e4a-434">Quando o componente é rerenderizado, o componente `<DetailsEditor>` pode ser alterado para receber diferentes valores de parâmetro `Details`.</span><span class="sxs-lookup"><span data-stu-id="42e4a-434">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="42e4a-435">Isso pode causar um reprocessamento mais complexo do que o esperado.</span><span class="sxs-lookup"><span data-stu-id="42e4a-435">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="42e4a-436">Em alguns casos, a rerenderização pode levar a diferenças de comportamento visíveis, como o foco de elemento perdido.</span><span class="sxs-lookup"><span data-stu-id="42e4a-436">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="42e4a-437">O processo de mapeamento pode ser controlado com o atributo de diretiva `@key`.</span><span class="sxs-lookup"><span data-stu-id="42e4a-437">The mapping process can be controlled with the `@key` directive attribute.</span></span> <span data-ttu-id="42e4a-438">`@key` faz com que o algoritmo diff garanta a preservação de elementos ou componentes com base no valor da chave:</span><span class="sxs-lookup"><span data-stu-id="42e4a-438">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="person" Details="person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="42e4a-439">Quando a coleção de `People` é alterada, o algoritmo diff mantém a associação entre instâncias de `<DetailsEditor>` e `person` instâncias:</span><span class="sxs-lookup"><span data-stu-id="42e4a-439">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="42e4a-440">Se uma `Person` for excluída da lista de `People`, somente a instância `<DetailsEditor>` correspondente será removida da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="42e4a-440">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="42e4a-441">Outras instâncias permanecem inalteradas.</span><span class="sxs-lookup"><span data-stu-id="42e4a-441">Other instances are left unchanged.</span></span>
* <span data-ttu-id="42e4a-442">Se uma `Person` for inserida em alguma posição na lista, uma nova instância de `<DetailsEditor>` será inserida na posição correspondente.</span><span class="sxs-lookup"><span data-stu-id="42e4a-442">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="42e4a-443">Outras instâncias permanecem inalteradas.</span><span class="sxs-lookup"><span data-stu-id="42e4a-443">Other instances are left unchanged.</span></span>
* <span data-ttu-id="42e4a-444">Se `Person` entradas forem reordenadas, as instâncias de `<DetailsEditor>` correspondentes serão preservadas e reordenadas na interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="42e4a-444">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="42e4a-445">Em alguns cenários, o uso de `@key` minimiza a complexidade de rerenderização e evita possíveis problemas com partes com estado da alteração do DOM, como a posição do foco.</span><span class="sxs-lookup"><span data-stu-id="42e4a-445">In some scenarios, use of `@key` minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="42e4a-446">As chaves são locais para cada elemento ou componente de contêiner.</span><span class="sxs-lookup"><span data-stu-id="42e4a-446">Keys are local to each container element or component.</span></span> <span data-ttu-id="42e4a-447">As chaves não são comparadas globalmente ao longo do documento.</span><span class="sxs-lookup"><span data-stu-id="42e4a-447">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="42e4a-448">Quando usar \@chave</span><span class="sxs-lookup"><span data-stu-id="42e4a-448">When to use \@key</span></span>

<span data-ttu-id="42e4a-449">Normalmente, faz sentido usar `@key` sempre que uma lista é renderizada (por exemplo, em um bloco de `@foreach`) e um valor adequado existe para definir o `@key`.</span><span class="sxs-lookup"><span data-stu-id="42e4a-449">Typically, it makes sense to use `@key` whenever a list is rendered (for example, in a `@foreach` block) and a suitable value exists to define the `@key`.</span></span>

<span data-ttu-id="42e4a-450">Você também pode usar `@key` para impedir Blazor de preservar uma subárvore de elemento ou componente quando um objeto for alterado:</span><span class="sxs-lookup"><span data-stu-id="42e4a-450">You can also use `@key` to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="42e4a-451">Se `@currentPerson` for alterado, a diretiva de atributo `@key` forçará Blazor a descartar toda a `<div>` e seus descendentes e recriará a subárvore dentro da interface do usuário com novos elementos e componentes.</span><span class="sxs-lookup"><span data-stu-id="42e4a-451">If `@currentPerson` changes, the `@key` attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="42e4a-452">Isso pode ser útil se você precisar garantir que nenhum estado da interface do usuário seja preservado quando `@currentPerson` for alterado.</span><span class="sxs-lookup"><span data-stu-id="42e4a-452">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="42e4a-453">Quando não usar \@chave</span><span class="sxs-lookup"><span data-stu-id="42e4a-453">When not to use \@key</span></span>

<span data-ttu-id="42e4a-454">Há um custo de desempenho ao comparar com `@key`.</span><span class="sxs-lookup"><span data-stu-id="42e4a-454">There's a performance cost when diffing with `@key`.</span></span> <span data-ttu-id="42e4a-455">O custo de desempenho não é grande, mas só especifique `@key` se o controle das regras de preservação de elemento ou componente beneficiar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="42e4a-455">The performance cost isn't large, but only specify `@key` if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="42e4a-456">Mesmo que `@key` não seja usado, Blazor preserva as instâncias de elemento filho e de componente o máximo possível.</span><span class="sxs-lookup"><span data-stu-id="42e4a-456">Even if `@key` isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="42e4a-457">A única vantagem de usar `@key` é o controle sobre *como* as instâncias de modelo são mapeadas para as instâncias de componente preservadas, em vez do algoritmo diff, selecionando o mapeamento.</span><span class="sxs-lookup"><span data-stu-id="42e4a-457">The only advantage to using `@key` is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="42e4a-458">Quais valores usar para \@chave</span><span class="sxs-lookup"><span data-stu-id="42e4a-458">What values to use for \@key</span></span>

<span data-ttu-id="42e4a-459">Geralmente, faz sentido fornecer um dos seguintes tipos de valor para `@key`:</span><span class="sxs-lookup"><span data-stu-id="42e4a-459">Generally, it makes sense to supply one of the following kinds of value for `@key`:</span></span>

* <span data-ttu-id="42e4a-460">Instâncias de objeto de modelo (por exemplo, uma instância de `Person` como no exemplo anterior).</span><span class="sxs-lookup"><span data-stu-id="42e4a-460">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="42e4a-461">Isso garante a preservação com base na igualdade de referência de objeto.</span><span class="sxs-lookup"><span data-stu-id="42e4a-461">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="42e4a-462">Identificadores exclusivos (por exemplo, valores de chave primária do tipo `int`, `string`ou `Guid`).</span><span class="sxs-lookup"><span data-stu-id="42e4a-462">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="42e4a-463">Verifique se os valores usados para `@key` não estão em conflito.</span><span class="sxs-lookup"><span data-stu-id="42e4a-463">Ensure that values used for `@key` don't clash.</span></span> <span data-ttu-id="42e4a-464">Se valores conflitantes forem detectados dentro do mesmo elemento pai, Blazor lançará uma exceção porque ele não pode mapear de forma determinística elementos ou componentes antigos para novos elementos ou componentes.</span><span class="sxs-lookup"><span data-stu-id="42e4a-464">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="42e4a-465">Use apenas valores distintos, como instâncias de objeto ou valores de chave primária.</span><span class="sxs-lookup"><span data-stu-id="42e4a-465">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="routing"></a><span data-ttu-id="42e4a-466">Roteamento</span><span class="sxs-lookup"><span data-stu-id="42e4a-466">Routing</span></span>

<span data-ttu-id="42e4a-467">O roteamento no Blazor é obtido fornecendo um modelo de rota para cada componente acessível no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="42e4a-467">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="42e4a-468">Quando um arquivo Razor com uma diretiva `@page` é compilado, a classe gerada recebe um <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> especificando o modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="42e4a-468">When a Razor file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="42e4a-469">Em tempo de execução, o roteador procura classes de componentes com um `RouteAttribute` e renderiza qualquer componente que tenha um modelo de rota que corresponda à URL solicitada.</span><span class="sxs-lookup"><span data-stu-id="42e4a-469">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

<span data-ttu-id="42e4a-470">Vários modelos de rota podem ser aplicados a um componente.</span><span class="sxs-lookup"><span data-stu-id="42e4a-470">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="42e4a-471">O componente a seguir responde às solicitações de `/BlazorRoute` e `/DifferentBlazorRoute`.</span><span class="sxs-lookup"><span data-stu-id="42e4a-471">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`.</span></span>

<span data-ttu-id="42e4a-472">*Páginas/BlazorRoute. Razor*:</span><span class="sxs-lookup"><span data-stu-id="42e4a-472">*Pages/BlazorRoute.razor*:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

## <a name="route-parameters"></a><span data-ttu-id="42e4a-473">Parâmetros de rota</span><span class="sxs-lookup"><span data-stu-id="42e4a-473">Route parameters</span></span>

<span data-ttu-id="42e4a-474">Os componentes podem receber parâmetros de rota do modelo de rota fornecido na diretiva `@page`.</span><span class="sxs-lookup"><span data-stu-id="42e4a-474">Components can receive route parameters from the route template provided in the `@page` directive.</span></span> <span data-ttu-id="42e4a-475">O roteador usa parâmetros de rota para preencher os parâmetros de componente correspondentes.</span><span class="sxs-lookup"><span data-stu-id="42e4a-475">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="42e4a-476">*Páginas/RouteParameter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="42e4a-476">*Pages/RouteParameter.razor*:</span></span>

```razor
@page "/RouteParameter"
@page "/RouteParameter/{text}"

<h1>Blazor is @Text!</h1>

@code {
    [Parameter]
    public string Text { get; set; }

    protected override void OnInitialized()
    {
        Text = Text ?? "fantastic";
    }
}
```

<span data-ttu-id="42e4a-477">Não há suporte para parâmetros opcionais, portanto, duas diretivas `@page` são aplicadas no exemplo acima.</span><span class="sxs-lookup"><span data-stu-id="42e4a-477">Optional parameters aren't supported, so two `@page` directives are applied in the example above.</span></span> <span data-ttu-id="42e4a-478">O primeiro permite a navegação para o componente sem um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="42e4a-478">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="42e4a-479">A segunda diretiva de `@page` usa o parâmetro `{text}` Route e atribui o valor à propriedade `Text`.</span><span class="sxs-lookup"><span data-stu-id="42e4a-479">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

<span data-ttu-id="42e4a-480">A sintaxe de parâmetro *catch-all* (`*`/`**`), que captura o caminho entre vários limites de pasta, **não** tem suporte em componentes do Razor ( *. Razor*).</span><span class="sxs-lookup"><span data-stu-id="42e4a-480">*Catch-all* parameter syntax (`*`/`**`), which captures the path across multiple folder boundaries, is **not** supported in Razor components (*.razor*).</span></span>

::: moniker range=">= aspnetcore-3.1"

## <a name="partial-class-support"></a><span data-ttu-id="42e4a-481">Suporte de classe parcial</span><span class="sxs-lookup"><span data-stu-id="42e4a-481">Partial class support</span></span>

<span data-ttu-id="42e4a-482">Os componentes do Razor são gerados como classes parciais.</span><span class="sxs-lookup"><span data-stu-id="42e4a-482">Razor components are generated as partial classes.</span></span> <span data-ttu-id="42e4a-483">Os componentes do Razor são criados usando uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="42e4a-483">Razor components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="42e4a-484">C#o código é definido em um bloco de [`@code`](xref:mvc/views/razor#code) com marcação HTML e código do Razor em um único arquivo.</span><span class="sxs-lookup"><span data-stu-id="42e4a-484">C# code is defined in an [`@code`](xref:mvc/views/razor#code) block with HTML markup and Razor code in a single file.</span></span> Blazor<span data-ttu-id="42e4a-485"> modelos definem seus componentes do Razor usando essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="42e4a-485"> templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="42e4a-486">C#o código é colocado em um arquivo code-behind definido como uma classe parcial.</span><span class="sxs-lookup"><span data-stu-id="42e4a-486">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="42e4a-487">O exemplo a seguir mostra o componente de `Counter` padrão com um bloco de `@code` em um aplicativo gerado por meio de um modelo de Blazor.</span><span class="sxs-lookup"><span data-stu-id="42e4a-487">The following example shows the default `Counter` component with an `@code` block in an app generated from a Blazor template.</span></span> <span data-ttu-id="42e4a-488">Marcação HTML, código do Razor e C# código estão no mesmo arquivo:</span><span class="sxs-lookup"><span data-stu-id="42e4a-488">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="42e4a-489">*Counter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="42e4a-489">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    int currentCount = 0;

    void IncrementCount()
    {
        currentCount++;
    }
}
```

<span data-ttu-id="42e4a-490">O componente `Counter` também pode ser criado usando um arquivo code-behind com uma classe parcial:</span><span class="sxs-lookup"><span data-stu-id="42e4a-490">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="42e4a-491">*Counter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="42e4a-491">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="42e4a-492">*Counter.Razor.cs*:</span><span class="sxs-lookup"><span data-stu-id="42e4a-492">*Counter.razor.cs*:</span></span>

```csharp
namespace BlazorApp.Pages
{
    public partial class Counter
    {
        int currentCount = 0;

        void IncrementCount()
        {
            currentCount++;
        }
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.1"

## <a name="specify-a-component-base-class"></a><span data-ttu-id="42e4a-493">Especificar uma classe base de componente</span><span class="sxs-lookup"><span data-stu-id="42e4a-493">Specify a component base class</span></span>

<span data-ttu-id="42e4a-494">A diretiva `@inherits` pode ser usada para especificar uma classe base para um componente.</span><span class="sxs-lookup"><span data-stu-id="42e4a-494">The `@inherits` directive can be used to specify a base class for a component.</span></span>

<span data-ttu-id="42e4a-495">O [aplicativo de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) mostra como um componente pode herdar uma classe base, `BlazorRocksBase`, para fornecer as propriedades e os métodos do componente.</span><span class="sxs-lookup"><span data-stu-id="42e4a-495">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span>

<span data-ttu-id="42e4a-496">*Páginas/BlazorRocks. Razor*:</span><span class="sxs-lookup"><span data-stu-id="42e4a-496">*Pages/BlazorRocks.razor*:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="42e4a-497">*BlazorRocksBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="42e4a-497">*BlazorRocksBase.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Components;

namespace BlazorSample
{
    public class BlazorRocksBase : ComponentBase
    {
        public string BlazorRocksText { get; set; } = 
            "Blazor rocks the browser!";
    }
}
```

<span data-ttu-id="42e4a-498">A classe base deve derivar de `ComponentBase`.</span><span class="sxs-lookup"><span data-stu-id="42e4a-498">The base class should derive from `ComponentBase`.</span></span>

::: moniker-end

## <a name="import-components"></a><span data-ttu-id="42e4a-499">Importar componentes</span><span class="sxs-lookup"><span data-stu-id="42e4a-499">Import components</span></span>

<span data-ttu-id="42e4a-500">O namespace de um componente criado com o Razor baseia-se em (em ordem de prioridade):</span><span class="sxs-lookup"><span data-stu-id="42e4a-500">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="42e4a-501">[`@namespace`](xref:mvc/views/razor#namespace) designação na marcação de arquivo Razor ( *. Razor*) (`@namespace BlazorSample.MyNamespace`).</span><span class="sxs-lookup"><span data-stu-id="42e4a-501">[`@namespace`](xref:mvc/views/razor#namespace) designation in Razor file (*.razor*) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="42e4a-502">O `RootNamespace` do projeto no arquivo de projeto (`<RootNamespace>BlazorSample</RootNamespace>`).</span><span class="sxs-lookup"><span data-stu-id="42e4a-502">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="42e4a-503">O nome do projeto, obtido do nome do arquivo do projeto ( *. csproj*) e o caminho da raiz do projeto para o componente.</span><span class="sxs-lookup"><span data-stu-id="42e4a-503">The project name, taken from the project file's file name (*.csproj*), and the path from the project root to the component.</span></span> <span data-ttu-id="42e4a-504">Por exemplo, a estrutura resolve *{raiz do projeto}/pages/index.Razor* (*BlazorSample. csproj*) para o namespace `BlazorSample.Pages`.</span><span class="sxs-lookup"><span data-stu-id="42e4a-504">For example, the framework resolves *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample.csproj*) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="42e4a-505">Os componentes C# seguem regras de associação de nome.</span><span class="sxs-lookup"><span data-stu-id="42e4a-505">Components follow C# name binding rules.</span></span> <span data-ttu-id="42e4a-506">Para o componente `Index` neste exemplo, os componentes no escopo são todos os componentes:</span><span class="sxs-lookup"><span data-stu-id="42e4a-506">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="42e4a-507">Na mesma pasta, *páginas*.</span><span class="sxs-lookup"><span data-stu-id="42e4a-507">In the same folder, *Pages*.</span></span>
  * <span data-ttu-id="42e4a-508">Os componentes na raiz do projeto que não especificam explicitamente um namespace diferente.</span><span class="sxs-lookup"><span data-stu-id="42e4a-508">The components in the project's root that don't explicitly specify a different namespace.</span></span>

<span data-ttu-id="42e4a-509">Os componentes definidos em um namespace diferente são trazidos para o escopo usando a diretiva [`@using`](xref:mvc/views/razor#using) do Razor.</span><span class="sxs-lookup"><span data-stu-id="42e4a-509">Components defined in a different namespace are brought into scope using Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span>

<span data-ttu-id="42e4a-510">Se outro componente, `NavMenu.razor`, existir na pasta *BlazorSample/Shared/* , o componente poderá ser usado em `Index.razor` com a seguinte instrução `@using`:</span><span class="sxs-lookup"><span data-stu-id="42e4a-510">If another component, `NavMenu.razor`, exists in the *BlazorSample/Shared/* folder, the component can be used in `Index.razor` with the following `@using` statement:</span></span>

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

<span data-ttu-id="42e4a-511">Os componentes também podem ser referenciados usando seus nomes totalmente qualificados, o que não requer a diretiva [`@using`](xref:mvc/views/razor#using) :</span><span class="sxs-lookup"><span data-stu-id="42e4a-511">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`](xref:mvc/views/razor#using) directive:</span></span>

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> <span data-ttu-id="42e4a-512">Não há suporte para a qualificação de `global::`.</span><span class="sxs-lookup"><span data-stu-id="42e4a-512">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="42e4a-513">Não há suporte para a importação de componentes com instruções de `using` com alias (por exemplo, `@using Foo = Bar`).</span><span class="sxs-lookup"><span data-stu-id="42e4a-513">Importing components with aliased `using` statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="42e4a-514">Não há suporte para nomes parcialmente qualificados.</span><span class="sxs-lookup"><span data-stu-id="42e4a-514">Partially qualified names aren't supported.</span></span> <span data-ttu-id="42e4a-515">Por exemplo, não há suporte para a adição de `@using BlazorSample` e referência `NavMenu.razor` com `<Shared.NavMenu></Shared.NavMenu>`.</span><span class="sxs-lookup"><span data-stu-id="42e4a-515">For example, adding `@using BlazorSample` and referencing `NavMenu.razor` with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="42e4a-516">Atributos de elemento HTML condicional</span><span class="sxs-lookup"><span data-stu-id="42e4a-516">Conditional HTML element attributes</span></span>

<span data-ttu-id="42e4a-517">Os atributos de elemento HTML são processados condicionalmente com base no valor do .NET.</span><span class="sxs-lookup"><span data-stu-id="42e4a-517">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="42e4a-518">Se o valor for `false` ou `null`, o atributo não será renderizado.</span><span class="sxs-lookup"><span data-stu-id="42e4a-518">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="42e4a-519">Se o valor é `true`, o atributo é processado minimizado.</span><span class="sxs-lookup"><span data-stu-id="42e4a-519">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="42e4a-520">No exemplo a seguir, `IsCompleted` determina se `checked` é renderizado na marcação do elemento:</span><span class="sxs-lookup"><span data-stu-id="42e4a-520">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="42e4a-521">Se `IsCompleted` for `true`, a caixa de seleção será renderizada como:</span><span class="sxs-lookup"><span data-stu-id="42e4a-521">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="42e4a-522">Se `IsCompleted` for `false`, a caixa de seleção será renderizada como:</span><span class="sxs-lookup"><span data-stu-id="42e4a-522">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="42e4a-523">Para obter mais informações, consulte <xref:mvc/views/razor>.</span><span class="sxs-lookup"><span data-stu-id="42e4a-523">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="42e4a-524">Alguns atributos HTML, como [pressionados pelo Aria](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), não funcionam corretamente quando o tipo .net é um `bool`.</span><span class="sxs-lookup"><span data-stu-id="42e4a-524">Some HTML attributes, such as [aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="42e4a-525">Nesses casos, use um tipo de `string` em vez de um `bool`.</span><span class="sxs-lookup"><span data-stu-id="42e4a-525">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="42e4a-526">HTML bruto</span><span class="sxs-lookup"><span data-stu-id="42e4a-526">Raw HTML</span></span>

<span data-ttu-id="42e4a-527">Normalmente, as cadeias de caracteres são renderizadas usando nós de texto DOM, o que significa que qualquer marcação que ela possa conter será ignorada e tratada como texto literal.</span><span class="sxs-lookup"><span data-stu-id="42e4a-527">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="42e4a-528">Para renderizar HTML bruto, empacote o conteúdo HTML em um valor `MarkupString`.</span><span class="sxs-lookup"><span data-stu-id="42e4a-528">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="42e4a-529">O valor é analisado como HTML ou SVG e inserido no DOM.</span><span class="sxs-lookup"><span data-stu-id="42e4a-529">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="42e4a-530">O processamento de HTML bruto construído a partir de qualquer fonte não confiável é um **risco à segurança** e deve ser evitado!</span><span class="sxs-lookup"><span data-stu-id="42e4a-530">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="42e4a-531">O exemplo a seguir mostra como usar o tipo de `MarkupString` para adicionar um bloco de conteúdo HTML estático à saída renderizada de um componente:</span><span class="sxs-lookup"><span data-stu-id="42e4a-531">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="templated-components"></a><span data-ttu-id="42e4a-532">Componentes de modelo</span><span class="sxs-lookup"><span data-stu-id="42e4a-532">Templated components</span></span>

<span data-ttu-id="42e4a-533">Componentes modelo são componentes que aceitam um ou mais modelos de interface do usuário como parâmetros, que podem ser usados como parte da lógica de renderização do componente.</span><span class="sxs-lookup"><span data-stu-id="42e4a-533">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="42e4a-534">Os componentes de modelo permitem que você crie componentes de nível superior que são mais reutilizáveis do que os componentes normais.</span><span class="sxs-lookup"><span data-stu-id="42e4a-534">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="42e4a-535">Alguns exemplos incluem:</span><span class="sxs-lookup"><span data-stu-id="42e4a-535">A couple of examples include:</span></span>

* <span data-ttu-id="42e4a-536">Um componente de tabela que permite que um usuário especifique modelos para o cabeçalho, as linhas e o rodapé da tabela.</span><span class="sxs-lookup"><span data-stu-id="42e4a-536">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="42e4a-537">Um componente de lista que permite que um usuário especifique um modelo para renderizar itens em uma lista.</span><span class="sxs-lookup"><span data-stu-id="42e4a-537">A list component that allows a user to specify a template for rendering items in a list.</span></span>

### <a name="template-parameters"></a><span data-ttu-id="42e4a-538">Parâmetros do Modelo</span><span class="sxs-lookup"><span data-stu-id="42e4a-538">Template parameters</span></span>

<span data-ttu-id="42e4a-539">Um componente modelo é definido especificando um ou mais parâmetros de componente do tipo `RenderFragment` ou `RenderFragment<T>`.</span><span class="sxs-lookup"><span data-stu-id="42e4a-539">A templated component is defined by specifying one or more component parameters of type `RenderFragment` or `RenderFragment<T>`.</span></span> <span data-ttu-id="42e4a-540">Um fragmento de renderização representa um segmento de interface do usuário a ser renderizado.</span><span class="sxs-lookup"><span data-stu-id="42e4a-540">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="42e4a-541">`RenderFragment<T>` usa um parâmetro de tipo que pode ser especificado quando o fragmento de renderização é invocado.</span><span class="sxs-lookup"><span data-stu-id="42e4a-541">`RenderFragment<T>` takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="42e4a-542">`TableTemplate` componente:</span><span class="sxs-lookup"><span data-stu-id="42e4a-542">`TableTemplate` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

<span data-ttu-id="42e4a-543">Ao usar um componente modelo, os parâmetros do modelo podem ser especificados usando elementos filho que correspondem aos nomes dos parâmetros (`TableHeader` e `RowTemplate` no exemplo a seguir):</span><span class="sxs-lookup"><span data-stu-id="42e4a-543">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

```razor
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@context.PetId</td>
        <td>@context.Name</td>
    </RowTemplate>
</TableTemplate>
```

### <a name="template-context-parameters"></a><span data-ttu-id="42e4a-544">Parâmetros de contexto de modelo</span><span class="sxs-lookup"><span data-stu-id="42e4a-544">Template context parameters</span></span>

<span data-ttu-id="42e4a-545">Os argumentos de componente do tipo `RenderFragment<T>` passados como elementos têm um parâmetro implícito denominado `context` (por exemplo, do exemplo de código anterior, `@context.PetId`), mas você pode alterar o nome do parâmetro usando o atributo `Context` no elemento filho.</span><span class="sxs-lookup"><span data-stu-id="42e4a-545">Component arguments of type `RenderFragment<T>` passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="42e4a-546">No exemplo a seguir, o atributo `Context` do elemento `RowTemplate` especifica o parâmetro `pet`:</span><span class="sxs-lookup"><span data-stu-id="42e4a-546">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

```razor
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate Context="pet">
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

<span data-ttu-id="42e4a-547">Como alternativa, você pode especificar o atributo `Context` no elemento Component.</span><span class="sxs-lookup"><span data-stu-id="42e4a-547">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="42e4a-548">O atributo `Context` especificado se aplica a todos os parâmetros de modelo especificados.</span><span class="sxs-lookup"><span data-stu-id="42e4a-548">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="42e4a-549">Isso pode ser útil quando você deseja especificar o nome do parâmetro de conteúdo para conteúdo filho implícito (sem qualquer elemento filho de disposição).</span><span class="sxs-lookup"><span data-stu-id="42e4a-549">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="42e4a-550">No exemplo a seguir, o atributo `Context` aparece no elemento `TableTemplate` e se aplica a todos os parâmetros de modelo:</span><span class="sxs-lookup"><span data-stu-id="42e4a-550">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

```razor
<TableTemplate Items="pets" Context="pet">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

### <a name="generic-typed-components"></a><span data-ttu-id="42e4a-551">Componentes de tipo genérico</span><span class="sxs-lookup"><span data-stu-id="42e4a-551">Generic-typed components</span></span>

<span data-ttu-id="42e4a-552">Os componentes modelo são geralmente digitados genericamente.</span><span class="sxs-lookup"><span data-stu-id="42e4a-552">Templated components are often generically typed.</span></span> <span data-ttu-id="42e4a-553">Por exemplo, um componente de `ListViewTemplate` genérico pode ser usado para processar `IEnumerable<T>` valores.</span><span class="sxs-lookup"><span data-stu-id="42e4a-553">For example, a generic `ListViewTemplate` component can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="42e4a-554">Para definir um componente genérico, use a diretiva [`@typeparam`](xref:mvc/views/razor#typeparam) para especificar parâmetros de tipo:</span><span class="sxs-lookup"><span data-stu-id="42e4a-554">To define a generic component, use the [`@typeparam`](xref:mvc/views/razor#typeparam) directive to specify type parameters:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

<span data-ttu-id="42e4a-555">Ao usar componentes de tipos genéricos, o parâmetro de tipo é inferido, se possível:</span><span class="sxs-lookup"><span data-stu-id="42e4a-555">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

<span data-ttu-id="42e4a-556">Caso contrário, o parâmetro de tipo deve ser especificado explicitamente usando um atributo que corresponda ao nome do parâmetro de tipo.</span><span class="sxs-lookup"><span data-stu-id="42e4a-556">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="42e4a-557">No exemplo a seguir, `TItem="Pet"` especifica o tipo:</span><span class="sxs-lookup"><span data-stu-id="42e4a-557">In the following example, `TItem="Pet"` specifies the type:</span></span>

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="42e4a-558">Valores e parâmetros em cascata</span><span class="sxs-lookup"><span data-stu-id="42e4a-558">Cascading values and parameters</span></span>

<span data-ttu-id="42e4a-559">Em alguns cenários, é inconveniente fluir dados de um componente ancestral para um componente descendente usando [parâmetros de componente](#component-parameters), especialmente quando há várias camadas de componente.</span><span class="sxs-lookup"><span data-stu-id="42e4a-559">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="42e4a-560">Valores e parâmetros em cascata resolvem esse problema fornecendo uma maneira conveniente para um componente ancestral fornecer um valor para todos os seus componentes descendentes.</span><span class="sxs-lookup"><span data-stu-id="42e4a-560">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="42e4a-561">Valores e parâmetros em cascata também fornecem uma abordagem para que os componentes sejam coordenados.</span><span class="sxs-lookup"><span data-stu-id="42e4a-561">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="42e4a-562">Exemplo de tema</span><span class="sxs-lookup"><span data-stu-id="42e4a-562">Theme example</span></span>

<span data-ttu-id="42e4a-563">No exemplo a seguir do aplicativo de exemplo, a classe `ThemeInfo` especifica as informações do tema para fluir para baixo na hierarquia do componente para que todos os botões de uma determinada parte do aplicativo compartilhem o mesmo estilo.</span><span class="sxs-lookup"><span data-stu-id="42e4a-563">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="42e4a-564">*UIThemeClasses/ThemeInfo.cs*:</span><span class="sxs-lookup"><span data-stu-id="42e4a-564">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="42e4a-565">Um componente ancestral pode fornecer um valor em cascata usando o componente de valor em cascata.</span><span class="sxs-lookup"><span data-stu-id="42e4a-565">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="42e4a-566">O componente `CascadingValue` encapsula uma subárvore da hierarquia de componentes e fornece um único valor para todos os componentes dentro dessa subárvore.</span><span class="sxs-lookup"><span data-stu-id="42e4a-566">The `CascadingValue` component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="42e4a-567">Por exemplo, o aplicativo de exemplo especifica informações de tema (`ThemeInfo`) em um dos layouts do aplicativo como um parâmetro em cascata para todos os componentes que compõem o corpo do layout da propriedade `@Body`.</span><span class="sxs-lookup"><span data-stu-id="42e4a-567">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="42e4a-568">`ButtonClass` é atribuído um valor de `btn-success` no componente de layout.</span><span class="sxs-lookup"><span data-stu-id="42e4a-568">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="42e4a-569">Qualquer componente descendente pode consumir essa propriedade por meio do `ThemeInfo` objeto em cascata.</span><span class="sxs-lookup"><span data-stu-id="42e4a-569">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="42e4a-570">`CascadingValuesParametersLayout` componente:</span><span class="sxs-lookup"><span data-stu-id="42e4a-570">`CascadingValuesParametersLayout` component:</span></span>

```razor
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

<span data-ttu-id="42e4a-571">Para fazer uso de valores em cascata, os componentes declaram parâmetros em cascata usando o atributo `[CascadingParameter]`.</span><span class="sxs-lookup"><span data-stu-id="42e4a-571">To make use of cascading values, components declare cascading parameters using the `[CascadingParameter]` attribute.</span></span> <span data-ttu-id="42e4a-572">Os valores em cascata são associados a parâmetros em cascata por tipo.</span><span class="sxs-lookup"><span data-stu-id="42e4a-572">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="42e4a-573">No aplicativo de exemplo, o componente `CascadingValuesParametersTheme` associa o valor de `ThemeInfo` em cascata a um parâmetro em cascata.</span><span class="sxs-lookup"><span data-stu-id="42e4a-573">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="42e4a-574">O parâmetro é usado para definir a classe CSS para um dos botões exibidos pelo componente.</span><span class="sxs-lookup"><span data-stu-id="42e4a-574">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="42e4a-575">`CascadingValuesParametersTheme` componente:</span><span class="sxs-lookup"><span data-stu-id="42e4a-575">`CascadingValuesParametersTheme` component:</span></span>

```razor
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @currentCount</p>

<p>
    <button class="btn" @onclick="IncrementCount">
        Increment Counter (Unthemed)
    </button>
</p>

<p>
    <button class="btn @ThemeInfo.ButtonClass" @onclick="IncrementCount">
        Increment Counter (Themed)
    </button>
</p>

@code {
    private int currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

<span data-ttu-id="42e4a-576">Para propagar vários valores do mesmo tipo dentro da mesma subárvore, forneça uma cadeia de caracteres `Name` exclusiva para cada componente `CascadingValue` e sua `CascadingParameter`correspondente.</span><span class="sxs-lookup"><span data-stu-id="42e4a-576">To cascade multiple values of the same type within the same subtree, provide a unique `Name` string to each `CascadingValue` component and its corresponding `CascadingParameter`.</span></span> <span data-ttu-id="42e4a-577">No exemplo a seguir, dois componentes `CascadingValue` em cascata diferentes instâncias de `MyCascadingType` por nome:</span><span class="sxs-lookup"><span data-stu-id="42e4a-577">In the following example, two `CascadingValue` components cascade different instances of `MyCascadingType` by name:</span></span>

```razor
<CascadingValue Value=@ParentCascadeParameter1 Name="CascadeParam1">
    <CascadingValue Value=@ParentCascadeParameter2 Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType ParentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

<span data-ttu-id="42e4a-578">Em um componente descendente, os parâmetros em cascata recebem seus valores dos valores em cascata correspondentes no componente ancestral por nome:</span><span class="sxs-lookup"><span data-stu-id="42e4a-578">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="42e4a-579">Exemplo de TabSet</span><span class="sxs-lookup"><span data-stu-id="42e4a-579">TabSet example</span></span>

<span data-ttu-id="42e4a-580">Os parâmetros em cascata também permitem que os componentes colaborem na hierarquia do componente.</span><span class="sxs-lookup"><span data-stu-id="42e4a-580">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="42e4a-581">Por exemplo, considere o exemplo de *TabSet* a seguir no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="42e4a-581">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="42e4a-582">O aplicativo de exemplo tem uma interface `ITab` que as guias implementam:</span><span class="sxs-lookup"><span data-stu-id="42e4a-582">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

<span data-ttu-id="42e4a-583">O componente `CascadingValuesParametersTabSet` usa o componente `TabSet`, que contém vários componentes `Tab`:</span><span class="sxs-lookup"><span data-stu-id="42e4a-583">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

```razor
<TabSet>
    <Tab Title="First tab">
        <h4>Greetings from the first tab!</h4>

        <label>
            <input type="checkbox" @bind="showThirdTab" />
            Toggle third tab
        </label>
    </Tab>
    <Tab Title="Second tab">
        <h4>The second tab says Hello World!</h4>
    </Tab>

    @if (showThirdTab)
    {
        <Tab Title="Third tab">
            <h4>Welcome to the disappearing third tab!</h4>
            <p>Toggle this tab from the first tab.</p>
        </Tab>
    }
</TabSet>
```

<span data-ttu-id="42e4a-584">Os componentes de `Tab` filhos não são passados explicitamente como parâmetros para o `TabSet`.</span><span class="sxs-lookup"><span data-stu-id="42e4a-584">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="42e4a-585">Em vez disso, os componentes de `Tab` filhos fazem parte do conteúdo filho do `TabSet`.</span><span class="sxs-lookup"><span data-stu-id="42e4a-585">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="42e4a-586">No entanto, o `TabSet` ainda precisa saber sobre cada componente `Tab` para que ele possa renderizar os cabeçalhos e a guia ativa. Para habilitar essa coordenação sem a necessidade de código adicional, o componente `TabSet` *pode fornecer a si mesmo como um valor em cascata* que é então coletado pelos componentes do `Tab` descendente.</span><span class="sxs-lookup"><span data-stu-id="42e4a-586">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="42e4a-587">`TabSet` componente:</span><span class="sxs-lookup"><span data-stu-id="42e4a-587">`TabSet` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

<span data-ttu-id="42e4a-588">Os componentes `Tab` descendentes capturam o `TabSet` recipiente como um parâmetro em cascata, de modo que os componentes `Tab` se adicionam à `TabSet` e coordenam em qual guia está ativa.</span><span class="sxs-lookup"><span data-stu-id="42e4a-588">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="42e4a-589">`Tab` componente:</span><span class="sxs-lookup"><span data-stu-id="42e4a-589">`Tab` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a><span data-ttu-id="42e4a-590">Modelos do Razor</span><span class="sxs-lookup"><span data-stu-id="42e4a-590">Razor templates</span></span>

<span data-ttu-id="42e4a-591">Os fragmentos de renderização podem ser definidos usando a sintaxe de modelo Razor.</span><span class="sxs-lookup"><span data-stu-id="42e4a-591">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="42e4a-592">Os modelos Razor são uma maneira de definir um trecho de interface do usuário e assumir o seguinte formato:</span><span class="sxs-lookup"><span data-stu-id="42e4a-592">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="42e4a-593">O exemplo a seguir ilustra como especificar `RenderFragment` e valores de `RenderFragment<T>` e renderizar modelos diretamente em um componente.</span><span class="sxs-lookup"><span data-stu-id="42e4a-593">The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>` values and render templates directly in a component.</span></span> <span data-ttu-id="42e4a-594">Os fragmentos de renderização também podem ser passados como argumentos para [componentes de modelo](#templated-components).</span><span class="sxs-lookup"><span data-stu-id="42e4a-594">Render fragments can also be passed as arguments to [templated components](#templated-components).</span></span>

```razor
@timeTemplate

@petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> petTemplate = 
        (pet) => @<p>Your pet's name is @pet.Name.</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

<span data-ttu-id="42e4a-595">Saída renderizada do código anterior:</span><span class="sxs-lookup"><span data-stu-id="42e4a-595">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Your pet's name is Rex.</p>
```

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="42e4a-596">Lógica RenderTreeBuilder manual</span><span class="sxs-lookup"><span data-stu-id="42e4a-596">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="42e4a-597">`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` fornece métodos para manipular componentes e elementos, incluindo a criação manual de componentes C# no código.</span><span class="sxs-lookup"><span data-stu-id="42e4a-597">`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="42e4a-598">O uso de `RenderTreeBuilder` para criar componentes é um cenário avançado.</span><span class="sxs-lookup"><span data-stu-id="42e4a-598">Use of `RenderTreeBuilder` to create components is an advanced scenario.</span></span> <span data-ttu-id="42e4a-599">Um componente malformado (por exemplo, uma marca de marcação não fechada) pode resultar em um comportamento indefinido.</span><span class="sxs-lookup"><span data-stu-id="42e4a-599">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="42e4a-600">Considere o seguinte componente de `PetDetails`, que pode ser compilado manualmente em outro componente:</span><span class="sxs-lookup"><span data-stu-id="42e4a-600">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="42e4a-601">No exemplo a seguir, o loop no método `CreateComponent` gera três componentes `PetDetails`.</span><span class="sxs-lookup"><span data-stu-id="42e4a-601">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="42e4a-602">Ao chamar `RenderTreeBuilder` métodos para criar os componentes (`OpenComponent` e `AddAttribute`), os números de sequência são números de linha de código-fonte.</span><span class="sxs-lookup"><span data-stu-id="42e4a-602">When calling `RenderTreeBuilder` methods to create the components (`OpenComponent` and `AddAttribute`), sequence numbers are source code line numbers.</span></span> <span data-ttu-id="42e4a-603">O algoritmo de diferença de Blazor depende dos números de sequência correspondentes a linhas distintas de código, não a invocações de chamada distintas.</span><span class="sxs-lookup"><span data-stu-id="42e4a-603">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="42e4a-604">Ao criar um componente com métodos `RenderTreeBuilder`, codifique os argumentos para números de sequência.</span><span class="sxs-lookup"><span data-stu-id="42e4a-604">When creating a component with `RenderTreeBuilder` methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="42e4a-605">**O uso de um cálculo ou contador para gerar o número de sequência pode levar a um desempenho insatisfatório.**</span><span class="sxs-lookup"><span data-stu-id="42e4a-605">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="42e4a-606">Para obter mais informações, consulte os [números de sequência relacionados à seção números de linha de código e não ordem de execução](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) .</span><span class="sxs-lookup"><span data-stu-id="42e4a-606">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="42e4a-607">`BuiltContent` componente:</span><span class="sxs-lookup"><span data-stu-id="42e4a-607">`BuiltContent` component:</span></span>

```razor
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" @onclick="RenderComponent">
    Create three Pet Details components
</button>

@code {
    private RenderFragment CustomRender { get; set; }
    
    private RenderFragment CreateComponent() => builder =>
    {
        for (var i = 0; i < 3; i++) 
        {
            builder.OpenComponent(0, typeof(PetDetails));
            builder.AddAttribute(1, "PetDetailsQuote", "Someone's best friend!");
            builder.CloseComponent();
        }
    };    
    
    private void RenderComponent()
    {
        CustomRender = CreateComponent();
    }
}
```

> <span data-ttu-id="42e4a-608">! ALERTA Os tipos no `Microsoft.AspNetCore.Components.RenderTree` permitem o processamento dos *resultados* de operações de renderização.</span><span class="sxs-lookup"><span data-stu-id="42e4a-608">![WARNING] The types in `Microsoft.AspNetCore.Components.RenderTree` allow processing of the *results* of rendering operations.</span></span> <span data-ttu-id="42e4a-609">Estes são detalhes internos da implementação do Blazor Framework.</span><span class="sxs-lookup"><span data-stu-id="42e4a-609">These are internal details of the Blazor framework implementation.</span></span> <span data-ttu-id="42e4a-610">Esses tipos devem ser considerados *instáveis* e sujeitos a alterações em versões futuras.</span><span class="sxs-lookup"><span data-stu-id="42e4a-610">These types should be considered *unstable* and subject to change in future releases.</span></span>

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="42e4a-611">Números de sequência se relacionam a números de linha de código e não a ordem de execução</span><span class="sxs-lookup"><span data-stu-id="42e4a-611">Sequence numbers relate to code line numbers and not execution order</span></span>

Blazor<span data-ttu-id="42e4a-612"> `.razor` arquivos são sempre compilados.</span><span class="sxs-lookup"><span data-stu-id="42e4a-612"> `.razor` files are always compiled.</span></span> <span data-ttu-id="42e4a-613">Isso é potencialmente uma grande vantagem para `.razor` porque a etapa de compilação pode ser usada para injetar informações que melhoram o desempenho do aplicativo em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="42e4a-613">This is potentially a great advantage for `.razor` because the compile step can be used to inject information that improve app performance at runtime.</span></span>

<span data-ttu-id="42e4a-614">Um exemplo importante desses aprimoramentos envolve *números de sequência*.</span><span class="sxs-lookup"><span data-stu-id="42e4a-614">A key example of these improvements involve *sequence numbers*.</span></span> <span data-ttu-id="42e4a-615">Os números de sequência indicam ao tempo de execução que as saídas vieram de quais linhas de código distintas e ordenadas.</span><span class="sxs-lookup"><span data-stu-id="42e4a-615">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="42e4a-616">O tempo de execução usa essas informações para gerar comparações de árvore eficientes em tempo linear, o que é muito mais rápido do que normalmente é possível para um algoritmo de comparação de árvore geral.</span><span class="sxs-lookup"><span data-stu-id="42e4a-616">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="42e4a-617">Considere o seguinte arquivo de componente Razor ( *. Razor*):</span><span class="sxs-lookup"><span data-stu-id="42e4a-617">Consider the following Razor component (*.razor*) file:</span></span>

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="42e4a-618">O código anterior é compilado para algo semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="42e4a-618">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="42e4a-619">Quando o código é executado pela primeira vez, se `someFlag` for `true`, o Construtor receberá:</span><span class="sxs-lookup"><span data-stu-id="42e4a-619">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="42e4a-620">Sequence</span><span class="sxs-lookup"><span data-stu-id="42e4a-620">Sequence</span></span> | <span data-ttu-id="42e4a-621">{1&gt;Tipo&lt;1}</span><span class="sxs-lookup"><span data-stu-id="42e4a-621">Type</span></span>      | <span data-ttu-id="42e4a-622">Dados</span><span class="sxs-lookup"><span data-stu-id="42e4a-622">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="42e4a-623">0</span><span class="sxs-lookup"><span data-stu-id="42e4a-623">0</span></span>        | <span data-ttu-id="42e4a-624">Nó de texto</span><span class="sxs-lookup"><span data-stu-id="42e4a-624">Text node</span></span> | <span data-ttu-id="42e4a-625">First</span><span class="sxs-lookup"><span data-stu-id="42e4a-625">First</span></span>  |
| <span data-ttu-id="42e4a-626">1</span><span class="sxs-lookup"><span data-stu-id="42e4a-626">1</span></span>        | <span data-ttu-id="42e4a-627">Nó de texto</span><span class="sxs-lookup"><span data-stu-id="42e4a-627">Text node</span></span> | <span data-ttu-id="42e4a-628">Segundo</span><span class="sxs-lookup"><span data-stu-id="42e4a-628">Second</span></span> |

<span data-ttu-id="42e4a-629">Imagine que `someFlag` se torna `false`e a marcação é renderizada novamente.</span><span class="sxs-lookup"><span data-stu-id="42e4a-629">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="42e4a-630">Desta vez, o Construtor recebe:</span><span class="sxs-lookup"><span data-stu-id="42e4a-630">This time, the builder receives:</span></span>

| <span data-ttu-id="42e4a-631">Sequence</span><span class="sxs-lookup"><span data-stu-id="42e4a-631">Sequence</span></span> | <span data-ttu-id="42e4a-632">{1&gt;Tipo&lt;1}</span><span class="sxs-lookup"><span data-stu-id="42e4a-632">Type</span></span>       | <span data-ttu-id="42e4a-633">Dados</span><span class="sxs-lookup"><span data-stu-id="42e4a-633">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="42e4a-634">1</span><span class="sxs-lookup"><span data-stu-id="42e4a-634">1</span></span>        | <span data-ttu-id="42e4a-635">Nó de texto</span><span class="sxs-lookup"><span data-stu-id="42e4a-635">Text node</span></span>  | <span data-ttu-id="42e4a-636">Segundo</span><span class="sxs-lookup"><span data-stu-id="42e4a-636">Second</span></span> |

<span data-ttu-id="42e4a-637">Quando o tempo de execução executa uma comparação, ele vê que o item na sequência `0` foi removido e, portanto, gera o seguinte *script de edição*trivial:</span><span class="sxs-lookup"><span data-stu-id="42e4a-637">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script*:</span></span>

* <span data-ttu-id="42e4a-638">Remova o primeiro nó de texto.</span><span class="sxs-lookup"><span data-stu-id="42e4a-638">Remove the first text node.</span></span>

#### <a name="what-goes-wrong-if-you-generate-sequence-numbers-programmatically"></a><span data-ttu-id="42e4a-639">O que vai errado se você gerar números de sequência programaticamente</span><span class="sxs-lookup"><span data-stu-id="42e4a-639">What goes wrong if you generate sequence numbers programmatically</span></span>

<span data-ttu-id="42e4a-640">Imagine, em vez disso, que você escreveu a seguinte lógica do construtor de árvore de renderização:</span><span class="sxs-lookup"><span data-stu-id="42e4a-640">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="42e4a-641">Agora, a primeira saída é:</span><span class="sxs-lookup"><span data-stu-id="42e4a-641">Now, the first output is:</span></span>

| <span data-ttu-id="42e4a-642">Sequence</span><span class="sxs-lookup"><span data-stu-id="42e4a-642">Sequence</span></span> | <span data-ttu-id="42e4a-643">{1&gt;Tipo&lt;1}</span><span class="sxs-lookup"><span data-stu-id="42e4a-643">Type</span></span>      | <span data-ttu-id="42e4a-644">Dados</span><span class="sxs-lookup"><span data-stu-id="42e4a-644">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="42e4a-645">0</span><span class="sxs-lookup"><span data-stu-id="42e4a-645">0</span></span>        | <span data-ttu-id="42e4a-646">Nó de texto</span><span class="sxs-lookup"><span data-stu-id="42e4a-646">Text node</span></span> | <span data-ttu-id="42e4a-647">First</span><span class="sxs-lookup"><span data-stu-id="42e4a-647">First</span></span>  |
| <span data-ttu-id="42e4a-648">1</span><span class="sxs-lookup"><span data-stu-id="42e4a-648">1</span></span>        | <span data-ttu-id="42e4a-649">Nó de texto</span><span class="sxs-lookup"><span data-stu-id="42e4a-649">Text node</span></span> | <span data-ttu-id="42e4a-650">Segundo</span><span class="sxs-lookup"><span data-stu-id="42e4a-650">Second</span></span> |

<span data-ttu-id="42e4a-651">Esse resultado é idêntico ao caso anterior, portanto, não existem problemas negativos.</span><span class="sxs-lookup"><span data-stu-id="42e4a-651">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="42e4a-652">`someFlag` é `false` no segundo processamento e a saída é:</span><span class="sxs-lookup"><span data-stu-id="42e4a-652">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="42e4a-653">Sequence</span><span class="sxs-lookup"><span data-stu-id="42e4a-653">Sequence</span></span> | <span data-ttu-id="42e4a-654">{1&gt;Tipo&lt;1}</span><span class="sxs-lookup"><span data-stu-id="42e4a-654">Type</span></span>      | <span data-ttu-id="42e4a-655">Dados</span><span class="sxs-lookup"><span data-stu-id="42e4a-655">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="42e4a-656">0</span><span class="sxs-lookup"><span data-stu-id="42e4a-656">0</span></span>        | <span data-ttu-id="42e4a-657">Nó de texto</span><span class="sxs-lookup"><span data-stu-id="42e4a-657">Text node</span></span> | <span data-ttu-id="42e4a-658">Segundo</span><span class="sxs-lookup"><span data-stu-id="42e4a-658">Second</span></span> |

<span data-ttu-id="42e4a-659">Desta vez, o algoritmo diff vê que *duas* alterações ocorreram e o algoritmo gera o seguinte script de edição:</span><span class="sxs-lookup"><span data-stu-id="42e4a-659">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="42e4a-660">Altere o valor do primeiro nó de texto para `Second`.</span><span class="sxs-lookup"><span data-stu-id="42e4a-660">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="42e4a-661">Remova o segundo nó de texto.</span><span class="sxs-lookup"><span data-stu-id="42e4a-661">Remove the second text node.</span></span>

<span data-ttu-id="42e4a-662">A geração dos números de sequência perdeu todas as informações úteis sobre onde os `if/else` branches e loops estavam presentes no código original.</span><span class="sxs-lookup"><span data-stu-id="42e4a-662">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="42e4a-663">Isso resulta em uma comparação **duas vezes mais longa** do que antes.</span><span class="sxs-lookup"><span data-stu-id="42e4a-663">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="42e4a-664">Esse é um exemplo trivial.</span><span class="sxs-lookup"><span data-stu-id="42e4a-664">This is a trivial example.</span></span> <span data-ttu-id="42e4a-665">Em casos mais realistas com estruturas complexas e profundamente aninhadas, e especialmente com loops, o custo de desempenho é mais grave.</span><span class="sxs-lookup"><span data-stu-id="42e4a-665">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is more severe.</span></span> <span data-ttu-id="42e4a-666">Em vez de identificar imediatamente quais blocos de loop ou ramificações foram inseridos ou removidos, o algoritmo diff precisa recorrer profundamente nas árvores de renderização e geralmente criar scripts de edição muito mais, pois ele é informado indiretamente sobre como as estruturas antigas e novas relacionar entre si.</span><span class="sxs-lookup"><span data-stu-id="42e4a-666">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees and usually build far longer edit scripts because it is misinformed about how the old and new structures relate to each other.</span></span>

#### <a name="guidance-and-conclusions"></a><span data-ttu-id="42e4a-667">Diretrizes e conclusões</span><span class="sxs-lookup"><span data-stu-id="42e4a-667">Guidance and conclusions</span></span>

* <span data-ttu-id="42e4a-668">O desempenho do aplicativo será afetado se os números de sequência forem gerados dinamicamente.</span><span class="sxs-lookup"><span data-stu-id="42e4a-668">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="42e4a-669">A estrutura não pode criar seus próprios números de sequência automaticamente em tempo de execução porque as informações necessárias não existem, a menos que sejam capturadas no momento da compilação.</span><span class="sxs-lookup"><span data-stu-id="42e4a-669">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="42e4a-670">Não grave blocos longos de lógica de `RenderTreeBuilder` implementada manualmente.</span><span class="sxs-lookup"><span data-stu-id="42e4a-670">Don't write long blocks of manually-implemented `RenderTreeBuilder` logic.</span></span> <span data-ttu-id="42e4a-671">Prefira `.razor` arquivos e permitir que o compilador lide com os números de sequência.</span><span class="sxs-lookup"><span data-stu-id="42e4a-671">Prefer `.razor` files and allow the compiler to deal with the sequence numbers.</span></span> <span data-ttu-id="42e4a-672">Se você não conseguir evitar a lógica de `RenderTreeBuilder` manual, divida blocos longos de código em partes menores encapsuladas em chamadas `OpenRegion`/`CloseRegion`.</span><span class="sxs-lookup"><span data-stu-id="42e4a-672">If you're unable to avoid manual `RenderTreeBuilder` logic, split long blocks of code into smaller pieces wrapped in `OpenRegion`/`CloseRegion` calls.</span></span> <span data-ttu-id="42e4a-673">Cada região tem seu próprio espaço separado de números de sequência, para que você possa reiniciar de zero (ou qualquer outro número arbitrário) dentro de cada região.</span><span class="sxs-lookup"><span data-stu-id="42e4a-673">Each region has its own separate space of sequence numbers, so you can restart from zero (or any other arbitrary number) inside each region.</span></span>
* <span data-ttu-id="42e4a-674">Se os números de sequência forem codificados, o algoritmo diff só exigirá que os números de sequência aumentem de valor.</span><span class="sxs-lookup"><span data-stu-id="42e4a-674">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="42e4a-675">O valor inicial e as lacunas são irrelevantes.</span><span class="sxs-lookup"><span data-stu-id="42e4a-675">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="42e4a-676">Uma opção legítima é usar o número de linha de código como o número de sequência, ou começar de zero e aumentar por um ou centenas (ou qualquer intervalo preferencial).</span><span class="sxs-lookup"><span data-stu-id="42e4a-676">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* Blazor<span data-ttu-id="42e4a-677"> usa números de sequência, enquanto outras estruturas de interface do usuário de diferenciação de árvore não as usam.</span><span class="sxs-lookup"><span data-stu-id="42e4a-677"> uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="42e4a-678">A comparação é muito mais rápida quando números de sequência são usados e Blazor tem a vantagem de uma etapa de compilação que lida com números de sequência automaticamente para desenvolvedores que criam arquivos *. Razor* .</span><span class="sxs-lookup"><span data-stu-id="42e4a-678">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring *.razor* files.</span></span>

## <a name="localization"></a><span data-ttu-id="42e4a-679">Localização</span><span class="sxs-lookup"><span data-stu-id="42e4a-679">Localization</span></span>

<span data-ttu-id="42e4a-680">os aplicativos do Blazor Server são localizados usando o [middleware de localização](xref:fundamentals/localization#localization-middleware).</span><span class="sxs-lookup"><span data-stu-id="42e4a-680">Blazor Server apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="42e4a-681">O middleware seleciona a cultura apropriada para os usuários que solicitam recursos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="42e4a-681">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="42e4a-682">A cultura pode ser definida usando uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="42e4a-682">The culture can be set using one of the following approaches:</span></span>

* [<span data-ttu-id="42e4a-683">Cookies</span><span class="sxs-lookup"><span data-stu-id="42e4a-683">Cookies</span></span>](#cookies)
* [<span data-ttu-id="42e4a-684">Fornecer interface do usuário para escolher a cultura</span><span class="sxs-lookup"><span data-stu-id="42e4a-684">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="42e4a-685">Para obter mais informações e exemplos, consulte <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="42e4a-685">For more information and examples, see <xref:fundamentals/localization>.</span></span>

### <a name="configure-the-linker-for-internationalization-opno-locblazor-webassembly"></a><span data-ttu-id="42e4a-686">Configurar o vinculador para internacionalização (Blazor Webassembly)</span><span class="sxs-lookup"><span data-stu-id="42e4a-686">Configure the linker for internationalization (Blazor WebAssembly)</span></span>

<span data-ttu-id="42e4a-687">Por padrão, a configuração do vinculador Blazorpara aplicativos Webassembly Blazor retira informações de internacionalização, exceto as localidades explicitamente solicitadas.</span><span class="sxs-lookup"><span data-stu-id="42e4a-687">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="42e4a-688">Para obter mais informações e orientação sobre como controlar o comportamento do vinculador, consulte <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.</span><span class="sxs-lookup"><span data-stu-id="42e4a-688">For more information and guidance on controlling the linker's behavior, see <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.</span></span>

### <a name="cookies"></a><span data-ttu-id="42e4a-689">Cookies</span><span class="sxs-lookup"><span data-stu-id="42e4a-689">Cookies</span></span>

<span data-ttu-id="42e4a-690">Um cookie de cultura de localização pode persistir a cultura do usuário.</span><span class="sxs-lookup"><span data-stu-id="42e4a-690">A localization culture cookie can persist the user's culture.</span></span> <span data-ttu-id="42e4a-691">O cookie é criado pelo método `OnGet` da página host do aplicativo (*pages/host. cshtml. cs*).</span><span class="sxs-lookup"><span data-stu-id="42e4a-691">The cookie is created by the `OnGet` method of the app's host page (*Pages/Host.cshtml.cs*).</span></span> <span data-ttu-id="42e4a-692">O middleware de localização lê o cookie em solicitações subsequentes para definir a cultura do usuário.</span><span class="sxs-lookup"><span data-stu-id="42e4a-692">The Localization Middleware reads the cookie on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="42e4a-693">O uso de um cookie garante que a conexão WebSocket possa propagar corretamente a cultura.</span><span class="sxs-lookup"><span data-stu-id="42e4a-693">Use of a cookie ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="42e4a-694">Se os esquemas de localização forem baseados no caminho da URL ou na cadeia de caracteres de consulta, o esquema pode não ser capaz de trabalhar com WebSockets, portanto, falha ao persistir a cultura.</span><span class="sxs-lookup"><span data-stu-id="42e4a-694">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="42e4a-695">Portanto, o uso de um cookie de cultura de localização é a abordagem recomendada.</span><span class="sxs-lookup"><span data-stu-id="42e4a-695">Therefore, use of a localization culture cookie is the recommended approach.</span></span>

<span data-ttu-id="42e4a-696">Qualquer técnica pode ser usada para atribuir uma cultura se a cultura persistir em um cookie de localização.</span><span class="sxs-lookup"><span data-stu-id="42e4a-696">Any technique can be used to assign a culture if the culture is persisted in a localization cookie.</span></span> <span data-ttu-id="42e4a-697">Se o aplicativo já tiver um esquema de localização estabelecido para ASP.NET Core do lado do servidor, continue a usar a infraestrutura de localização existente do aplicativo e defina o cookie de cultura de localização no esquema do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="42e4a-697">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture cookie within the app's scheme.</span></span>

<span data-ttu-id="42e4a-698">O exemplo a seguir mostra como definir a cultura atual em um cookie que pode ser lido pelo middleware de localização.</span><span class="sxs-lookup"><span data-stu-id="42e4a-698">The following example shows how to set the current culture in a cookie that can be read by the Localization Middleware.</span></span> <span data-ttu-id="42e4a-699">Crie um arquivo *pages/host. cshtml. cs* com o seguinte conteúdo no aplicativo Blazor Server:</span><span class="sxs-lookup"><span data-stu-id="42e4a-699">Create a *Pages/Host.cshtml.cs* file with the following contents in the Blazor Server app:</span></span>

```csharp
public class HostModel : PageModel
{
    public void OnGet()
    {
        HttpContext.Response.Cookies.Append(
            CookieRequestCultureProvider.DefaultCookieName,
            CookieRequestCultureProvider.MakeCookieValue(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }
}
```

<span data-ttu-id="42e4a-700">A localização é manipulada no aplicativo:</span><span class="sxs-lookup"><span data-stu-id="42e4a-700">Localization is handled in the app:</span></span>

1. <span data-ttu-id="42e4a-701">O navegador envia uma solicitação HTTP inicial para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="42e4a-701">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="42e4a-702">A cultura é atribuída pelo middleware de localização.</span><span class="sxs-lookup"><span data-stu-id="42e4a-702">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="42e4a-703">O método `OnGet` em *_Host. cshtml. cs* persiste a cultura em um cookie como parte da resposta.</span><span class="sxs-lookup"><span data-stu-id="42e4a-703">The `OnGet` method in *_Host.cshtml.cs* persists the culture in a cookie as part of the response.</span></span>
1. <span data-ttu-id="42e4a-704">O navegador abre uma conexão WebSocket para criar uma sessão interativa do Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="42e4a-704">The browser opens a WebSocket connection to create an interactive Blazor Server session.</span></span>
1. <span data-ttu-id="42e4a-705">O middleware de localização lê o cookie e atribui a cultura.</span><span class="sxs-lookup"><span data-stu-id="42e4a-705">The Localization Middleware reads the cookie and assigns the culture.</span></span>
1. <span data-ttu-id="42e4a-706">A sessão do Blazor Server começa com a cultura correta.</span><span class="sxs-lookup"><span data-stu-id="42e4a-706">The Blazor Server session begins with the correct culture.</span></span>

### <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="42e4a-707">Fornecer interface do usuário para escolher a cultura</span><span class="sxs-lookup"><span data-stu-id="42e4a-707">Provide UI to choose the culture</span></span>

<span data-ttu-id="42e4a-708">Para fornecer à interface do usuário a fim de permitir a seleção de uma cultura, é recomendável uma *abordagem baseada em redirecionamento* .</span><span class="sxs-lookup"><span data-stu-id="42e4a-708">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="42e4a-709">O processo é semelhante ao que acontece em um aplicativo Web quando um usuário tenta acessar um recurso seguro&mdash;o usuário é redirecionado para uma página de entrada e, em seguida, Redirecionado de volta para o recurso original.</span><span class="sxs-lookup"><span data-stu-id="42e4a-709">The process is similar to what happens in a web app when a user attempts to access a secure resource&mdash;the user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="42e4a-710">O aplicativo persiste a cultura selecionada do usuário por meio de um redirecionamento para um controlador.</span><span class="sxs-lookup"><span data-stu-id="42e4a-710">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="42e4a-711">O controlador define a cultura selecionada do usuário em um cookie e redireciona o usuário de volta para o URI original.</span><span class="sxs-lookup"><span data-stu-id="42e4a-711">The controller sets the user's selected culture into a cookie and redirects the user back to the original URI.</span></span>

<span data-ttu-id="42e4a-712">Estabeleça um ponto de extremidade HTTP no servidor para definir a cultura selecionada do usuário em um cookie e execute o redirecionamento de volta para o URI original:</span><span class="sxs-lookup"><span data-stu-id="42e4a-712">Establish an HTTP endpoint on the server to set the user's selected culture in a cookie and perform the redirect back to the original URI:</span></span>

```csharp
[Route("[controller]/[action]")]
public class CultureController : Controller
{
    public IActionResult SetCulture(string culture, string redirectUri)
    {
        if (culture != null)
        {
            HttpContext.Response.Cookies.Append(
                CookieRequestCultureProvider.DefaultCookieName,
                CookieRequestCultureProvider.MakeCookieValue(
                    new RequestCulture(culture)));
        }

        return LocalRedirect(redirectUri);
    }
}
```

> [!WARNING]
> <span data-ttu-id="42e4a-713">Use o resultado da ação `LocalRedirect` para evitar ataques de redirecionamento abertos.</span><span class="sxs-lookup"><span data-stu-id="42e4a-713">Use the `LocalRedirect` action result to prevent open redirect attacks.</span></span> <span data-ttu-id="42e4a-714">Para obter mais informações, consulte <xref:security/preventing-open-redirects>.</span><span class="sxs-lookup"><span data-stu-id="42e4a-714">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="42e4a-715">O componente a seguir mostra um exemplo de como executar o redirecionamento inicial quando o usuário seleciona uma cultura:</span><span class="sxs-lookup"><span data-stu-id="42e4a-715">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

```razor
@inject NavigationManager NavigationManager

<h3>Select your language</h3>

<select @onchange="OnSelected">
    <option>Select...</option>
    <option value="en-US">English</option>
    <option value="fr-FR">Français</option>
</select>

@code {
    private double textNumber;

    private void OnSelected(ChangeEventArgs e)
    {
        var culture = (string)e.Value;
        var uri = new Uri(NavigationManager.Uri())
            .GetComponents(UriComponents.PathAndQuery, UriFormat.Unescaped);
        var query = $"?culture={Uri.EscapeDataString(culture)}&" +
            $"redirectUri={Uri.EscapeDataString(uri)}";

        NavigationManager.NavigateTo("/Culture/SetCulture" + query, forceLoad: true);
    }
}
```

### <a name="use-net-localization-scenarios-in-opno-locblazor-apps"></a><span data-ttu-id="42e4a-716">Usar cenários de localização do .NET em aplicativos Blazor</span><span class="sxs-lookup"><span data-stu-id="42e4a-716">Use .NET localization scenarios in Blazor apps</span></span>

<span data-ttu-id="42e4a-717">Dentro de aplicativos Blazor, os seguintes cenários de localização e globalização do .NET estão disponíveis:</span><span class="sxs-lookup"><span data-stu-id="42e4a-717">Inside Blazor apps, the following .NET localization and globalization scenarios are available:</span></span>

* <span data-ttu-id="42e4a-718">. Sistema de recursos da rede</span><span class="sxs-lookup"><span data-stu-id="42e4a-718">.NET's resources system</span></span>
* <span data-ttu-id="42e4a-719">Formatação de número e data específicos da cultura</span><span class="sxs-lookup"><span data-stu-id="42e4a-719">Culture-specific number and date formatting</span></span>

<span data-ttu-id="42e4a-720">a funcionalidade de `@bind` do Blazorexecuta a globalização com base na cultura atual do usuário.</span><span class="sxs-lookup"><span data-stu-id="42e4a-720">Blazor's `@bind` functionality performs globalization based on the user's current culture.</span></span> <span data-ttu-id="42e4a-721">Para obter mais informações, consulte a seção [ligação de dados](#data-binding) .</span><span class="sxs-lookup"><span data-stu-id="42e4a-721">For more information, see the [Data binding](#data-binding) section.</span></span>

<span data-ttu-id="42e4a-722">No momento, há suporte para um conjunto limitado de cenários de localização de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="42e4a-722">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="42e4a-723">`IStringLocalizer<>` tem *suporte* em aplicativos Blazor.</span><span class="sxs-lookup"><span data-stu-id="42e4a-723">`IStringLocalizer<>` *is supported* in Blazor apps.</span></span>
* <span data-ttu-id="42e4a-724">`IHtmlLocalizer<>`, `IViewLocalizer<>`e localização de anotações de dados são ASP.NET Core cenários MVC e **não têm suporte** em aplicativos Blazor.</span><span class="sxs-lookup"><span data-stu-id="42e4a-724">`IHtmlLocalizer<>`, `IViewLocalizer<>`, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in Blazor apps.</span></span>

<span data-ttu-id="42e4a-725">Para obter mais informações, consulte <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="42e4a-725">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="42e4a-726">Imagens SVG (gráficos vetoriais escaláveis)</span><span class="sxs-lookup"><span data-stu-id="42e4a-726">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="42e4a-727">Como Blazor renderiza imagens HTML, com suporte para navegador, incluindo imagens SVG (gráficos de vetor escalonáveis) ( *. svg*), há suporte por meio da marca `<img>`:</span><span class="sxs-lookup"><span data-stu-id="42e4a-727">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (*.svg*), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="42e4a-728">Da mesma forma, as imagens SVG têm suporte nas regras de CSS de um arquivo de folha de estilos ( *. css*):</span><span class="sxs-lookup"><span data-stu-id="42e4a-728">Similarly, SVG images are supported in the CSS rules of a stylesheet file (*.css*):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="42e4a-729">No entanto, a marcação SVG embutida não tem suporte em todos os cenários.</span><span class="sxs-lookup"><span data-stu-id="42e4a-729">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="42e4a-730">Se você posicionar uma marca de `<svg>` diretamente em um arquivo de componente ( *. Razor*), a renderização de imagem básica terá suporte, mas muitos cenários avançados ainda não têm suporte.</span><span class="sxs-lookup"><span data-stu-id="42e4a-730">If you place an `<svg>` tag directly into a component file (*.razor*), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="42e4a-731">Por exemplo, `<use>` marcas não são respeitadas atualmente e `@bind` não podem ser usadas com algumas marcas SVG.</span><span class="sxs-lookup"><span data-stu-id="42e4a-731">For example, `<use>` tags aren't currently respected, and `@bind` can't be used with some SVG tags.</span></span> <span data-ttu-id="42e4a-732">Esperamos abordar essas limitações em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="42e4a-732">We expect to address these limitations in a future release.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="42e4a-733">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="42e4a-733">Additional resources</span></span>

* <span data-ttu-id="42e4a-734"><xref:security/blazor/server> &ndash; inclui diretrizes sobre a criação de aplicativos do Blazor Server que devem combater o esgotamento de recursos.</span><span class="sxs-lookup"><span data-stu-id="42e4a-734"><xref:security/blazor/server> &ndash; Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>
