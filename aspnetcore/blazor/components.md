---
title: Criar e usar ASP.NET Core componentes do Razor
author: guardrex
description: Saiba como criar e usar componentes do Razor, incluindo como associar dados, manipular eventos e gerenciar ciclos de vida do componente.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/28/2019
no-loc:
- Blazor
- SignalR
uid: blazor/components
ms.openlocfilehash: e73667925c04dd1b2360138343c4a2dcef0ee310
ms.sourcegitcommit: 9ee99300a48c810ca6fd4f7700cd95c3ccb85972
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2020
ms.locfileid: "76160009"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="4c911-103">Criar e usar ASP.NET Core componentes do Razor</span><span class="sxs-lookup"><span data-stu-id="4c911-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="4c911-104">De [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="4c911-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="4c911-105">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4c911-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

Blazor<span data-ttu-id="4c911-106"> aplicativos são criados usando *componentes*.</span><span class="sxs-lookup"><span data-stu-id="4c911-106"> apps are built using *components*.</span></span> <span data-ttu-id="4c911-107">Um componente é uma parte independente da interface do usuário (IU), como uma página, uma caixa de diálogo ou um formulário.</span><span class="sxs-lookup"><span data-stu-id="4c911-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="4c911-108">Um componente inclui marcação HTML e a lógica de processamento necessária para injetar dados ou responder a eventos da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="4c911-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="4c911-109">Os componentes são flexíveis e leves.</span><span class="sxs-lookup"><span data-stu-id="4c911-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="4c911-110">Eles podem ser aninhados, reutilizados e compartilhados entre projetos.</span><span class="sxs-lookup"><span data-stu-id="4c911-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="4c911-111">Classes de componente</span><span class="sxs-lookup"><span data-stu-id="4c911-111">Component classes</span></span>

<span data-ttu-id="4c911-112">Os componentes são implementados em arquivos de componente do [Razor](xref:mvc/views/razor) ( *. Razor*) C# usando uma combinação de marcação HTML e.</span><span class="sxs-lookup"><span data-stu-id="4c911-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="4c911-113">Um componente no Blazor é formalmente referido como um *componente Razor*.</span><span class="sxs-lookup"><span data-stu-id="4c911-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

<span data-ttu-id="4c911-114">O nome de um componente deve começar com um caractere maiúsculo.</span><span class="sxs-lookup"><span data-stu-id="4c911-114">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="4c911-115">Por exemplo, *MyCoolComponent. Razor* é válido e *MyCoolComponent. Razor* é inválido.</span><span class="sxs-lookup"><span data-stu-id="4c911-115">For example, *MyCoolComponent.razor* is valid, and *myCoolComponent.razor* is invalid.</span></span>

<span data-ttu-id="4c911-116">A interface do usuário para um componente é definida usando HTML.</span><span class="sxs-lookup"><span data-stu-id="4c911-116">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="4c911-117">A lógica de renderização dinâmica (por exemplo, loops, condicionais, expressões) é adicionada usando uma sintaxe de C# inserida chamada [Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="4c911-117">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="4c911-118">Quando um aplicativo é compilado, a marcação HTML e C# a lógica de renderização são convertidas em uma classe de componente.</span><span class="sxs-lookup"><span data-stu-id="4c911-118">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="4c911-119">O nome da classe gerada corresponde ao nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="4c911-119">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="4c911-120">Os membros da classe de componente são definidos em um bloco `@code`.</span><span class="sxs-lookup"><span data-stu-id="4c911-120">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="4c911-121">No bloco de `@code`, estado do componente (Propriedades, campos) é especificado com métodos para manipulação de eventos ou para definir outra lógica de componente.</span><span class="sxs-lookup"><span data-stu-id="4c911-121">In the `@code` block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="4c911-122">Mais de um bloco de `@code` é permitido.</span><span class="sxs-lookup"><span data-stu-id="4c911-122">More than one `@code` block is permissible.</span></span>

<span data-ttu-id="4c911-123">Os membros do componente podem ser usados como parte da lógica de renderização do C# componente usando expressões que começam com `@`.</span><span class="sxs-lookup"><span data-stu-id="4c911-123">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="4c911-124">Por exemplo, um C# campo é renderizado pela prefixação `@` ao nome do campo.</span><span class="sxs-lookup"><span data-stu-id="4c911-124">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="4c911-125">O exemplo a seguir avalia e renderiza:</span><span class="sxs-lookup"><span data-stu-id="4c911-125">The following example evaluates and renders:</span></span>

* <span data-ttu-id="4c911-126">`_headingFontStyle` ao valor da propriedade CSS para `font-style`.</span><span class="sxs-lookup"><span data-stu-id="4c911-126">`_headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="4c911-127">`_headingText` o conteúdo do elemento `<h1>`.</span><span class="sxs-lookup"><span data-stu-id="4c911-127">`_headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="4c911-128">Depois que o componente é processado inicialmente, o componente regenera sua árvore de renderização em resposta a eventos.</span><span class="sxs-lookup"><span data-stu-id="4c911-128">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> <span data-ttu-id="4c911-129">em seguida, Blazor compara a nova árvore de renderização com a anterior e aplica quaisquer modificações à Modelo de Objeto do Documento do navegador (DOM).</span><span class="sxs-lookup"><span data-stu-id="4c911-129">Blazor then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="4c911-130">Os componentes são C# classes comuns e podem ser colocados em qualquer lugar dentro de um projeto.</span><span class="sxs-lookup"><span data-stu-id="4c911-130">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="4c911-131">Os componentes que produzem páginas da Web geralmente residem na pasta *páginas* .</span><span class="sxs-lookup"><span data-stu-id="4c911-131">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="4c911-132">Os componentes que não são de página são frequentemente colocados na pasta *compartilhada* ou em uma pasta personalizada adicionada ao projeto.</span><span class="sxs-lookup"><span data-stu-id="4c911-132">Non-page components are frequently placed in the *Shared* folder or a custom folder added to the project.</span></span>

<span data-ttu-id="4c911-133">Normalmente, o namespace de um componente é derivado do namespace raiz do aplicativo e do local do componente (pasta) no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4c911-133">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="4c911-134">Se o namespace raiz do aplicativo for `BlazorApp` e o componente `Counter` residir na pasta *páginas* :</span><span class="sxs-lookup"><span data-stu-id="4c911-134">If the app's root namespace is `BlazorApp` and the `Counter` component resides in the *Pages* folder:</span></span>

* <span data-ttu-id="4c911-135">O namespace do componente de `Counter` é `BlazorApp.Pages`.</span><span class="sxs-lookup"><span data-stu-id="4c911-135">The `Counter` component's namespace is `BlazorApp.Pages`.</span></span>
* <span data-ttu-id="4c911-136">O nome do tipo totalmente qualificado do componente é `BlazorApp.Pages.Counter`.</span><span class="sxs-lookup"><span data-stu-id="4c911-136">The fully qualified type name of the component is `BlazorApp.Pages.Counter`.</span></span>

<span data-ttu-id="4c911-137">Para obter mais informações, consulte a seção [importar componentes](#import-components) .</span><span class="sxs-lookup"><span data-stu-id="4c911-137">For more information, see the [Import components](#import-components) section.</span></span>

<span data-ttu-id="4c911-138">Para usar uma pasta personalizada, adicione o namespace da pasta personalizada ao componente pai ou ao arquivo *_Imports. Razor* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4c911-138">To use a custom folder, add the custom folder's namespace to either the parent component or to the app's *_Imports.razor* file.</span></span> <span data-ttu-id="4c911-139">Por exemplo, o namespace a seguir torna os componentes em uma pasta *componentes* disponíveis quando o namespace raiz do aplicativo é `BlazorApp`:</span><span class="sxs-lookup"><span data-stu-id="4c911-139">For example, the following namespace makes components in a *Components* folder available when the app's root namespace is `BlazorApp`:</span></span>

```razor
@using BlazorApp.Components
```

## <a name="integrate-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="4c911-140">Integrar componentes em aplicativos Razor Pages e MVC</span><span class="sxs-lookup"><span data-stu-id="4c911-140">Integrate components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="4c911-141">Os componentes do Razor podem ser integrados em aplicativos Razor Pages e MVC.</span><span class="sxs-lookup"><span data-stu-id="4c911-141">Razor components can be integrated into Razor Pages and MVC apps.</span></span> <span data-ttu-id="4c911-142">Quando a página ou a exibição é renderizada, os componentes podem ser renderizados ao mesmo tempo.</span><span class="sxs-lookup"><span data-stu-id="4c911-142">When the page or view is rendered, components can be prerendered at the same time.</span></span>

<span data-ttu-id="4c911-143">Para preparar um aplicativo Razor Pages ou MVC para hospedar componentes do Razor, siga as orientações na seção *integrar componentes do Razor em Razor Pages e aplicativos MVC* do artigo <xref:blazor/hosting-models#integrate-razor-components-into-razor-pages-and-mvc-apps>.</span><span class="sxs-lookup"><span data-stu-id="4c911-143">To prepare a Razor Pages or MVC app to host Razor components, follow the guidance in the *Integrate Razor components into Razor Pages and MVC apps* section of the <xref:blazor/hosting-models#integrate-razor-components-into-razor-pages-and-mvc-apps> article.</span></span>

<span data-ttu-id="4c911-144">Ao usar uma pasta personalizada para manter os componentes do aplicativo, adicione o namespace que representa a pasta à página/exibição ou ao arquivo *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="4c911-144">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="4c911-145">No exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="4c911-145">In the following example:</span></span>

* <span data-ttu-id="4c911-146">Altere `MyAppNamespace` para o namespace do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4c911-146">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="4c911-147">Se uma pasta chamada *Components* não for usada para manter os componentes, altere `Components` para a pasta onde residem os componentes.</span><span class="sxs-lookup"><span data-stu-id="4c911-147">If a folder named *Components* isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```csharp
@using MyAppNamespace.Components
```

<span data-ttu-id="4c911-148">O arquivo *_ViewImports. cshtml* está localizado na pasta *páginas* de um aplicativo Razor pages ou na pasta *views* de um aplicativo MVC.</span><span class="sxs-lookup"><span data-stu-id="4c911-148">The *_ViewImports.cshtml* file is located in the *Pages* folder of a Razor Pages app or the *Views* folder of an MVC app.</span></span>

<span data-ttu-id="4c911-149">Para renderizar um componente de uma página ou exibição, use o `Component` o auxiliar de marca:</span><span class="sxs-lookup"><span data-stu-id="4c911-149">To render a component from a page or view, use the `Component` Tag Helper:</span></span>

```cshtml
<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-IncrementAmount="10" />
```

<span data-ttu-id="4c911-150">Há suporte para a passagem de parâmetros (por exemplo, `IncrementAmount` no exemplo anterior).</span><span class="sxs-lookup"><span data-stu-id="4c911-150">Passing parameters (for example, `IncrementAmount` in the preceding example) is supported.</span></span>

<span data-ttu-id="4c911-151">`RenderMode` configura se o componente:</span><span class="sxs-lookup"><span data-stu-id="4c911-151">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="4c911-152">É renderizado na página.</span><span class="sxs-lookup"><span data-stu-id="4c911-152">Is prerendered into the page.</span></span>
* <span data-ttu-id="4c911-153">É renderizado como HTML estático na página ou se inclui as informações necessárias para inicializar um aplicativo Blazor do agente do usuário.</span><span class="sxs-lookup"><span data-stu-id="4c911-153">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="4c911-154">Descrição</span><span class="sxs-lookup"><span data-stu-id="4c911-154">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="4c911-155">Renderiza o componente em HTML estático e inclui um marcador para um aplicativo do Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="4c911-155">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="4c911-156">Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um aplicativo Blazor.</span><span class="sxs-lookup"><span data-stu-id="4c911-156">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="4c911-157">Renderiza um marcador para um aplicativo do Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="4c911-157">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="4c911-158">A saída do componente não está incluída.</span><span class="sxs-lookup"><span data-stu-id="4c911-158">Output from the component isn't included.</span></span> <span data-ttu-id="4c911-159">Quando o agente do usuário é iniciado, esse marcador é usado para inicializar um aplicativo Blazor.</span><span class="sxs-lookup"><span data-stu-id="4c911-159">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="4c911-160">Renderiza o componente em HTML estático.</span><span class="sxs-lookup"><span data-stu-id="4c911-160">Renders the component into static HTML.</span></span> |

<span data-ttu-id="4c911-161">Embora as páginas e exibições possam usar componentes, o inverso não é verdadeiro.</span><span class="sxs-lookup"><span data-stu-id="4c911-161">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="4c911-162">Os componentes não podem usar cenários específicos de exibição e de página, como exibições parciais e seções.</span><span class="sxs-lookup"><span data-stu-id="4c911-162">Components can't use view- and page-specific scenarios, such as partial views and sections.</span></span> <span data-ttu-id="4c911-163">Para usar a lógica da exibição parcial em um componente, desfatore a lógica de exibição parcial em um componente.</span><span class="sxs-lookup"><span data-stu-id="4c911-163">To use logic from partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="4c911-164">Não há suporte para a renderização de componentes de servidor de uma página HTML estática.</span><span class="sxs-lookup"><span data-stu-id="4c911-164">Rendering server components from a static HTML page isn't supported.</span></span>

<span data-ttu-id="4c911-165">Para obter mais informações sobre como os componentes são renderizados, estado do componente e o auxiliar de marca de `Component`, consulte <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="4c911-165">For more information on how components are rendered, component state, and the `Component` Tag Helper, see <xref:blazor/hosting-models>.</span></span>

## <a name="use-components"></a><span data-ttu-id="4c911-166">Usar componentes</span><span class="sxs-lookup"><span data-stu-id="4c911-166">Use components</span></span>

<span data-ttu-id="4c911-167">Os componentes podem incluir outros componentes, declarando-os usando a sintaxe do elemento HTML.</span><span class="sxs-lookup"><span data-stu-id="4c911-167">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="4c911-168">A marcação para uso de um componente é semelhante a uma marca HTML, em que o nome da marca é o tipo de componente.</span><span class="sxs-lookup"><span data-stu-id="4c911-168">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="4c911-169">A associação de atributo diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="4c911-169">Attribute binding is case sensitive.</span></span> <span data-ttu-id="4c911-170">Por exemplo, `@bind` é válido e `@Bind` é inválido.</span><span class="sxs-lookup"><span data-stu-id="4c911-170">For example, `@bind` is valid, and `@Bind` is invalid.</span></span>

<span data-ttu-id="4c911-171">A marcação a seguir no *index. Razor* renderiza uma instância de `HeadingComponent`:</span><span class="sxs-lookup"><span data-stu-id="4c911-171">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="4c911-172">*Componentes/HeadingComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="4c911-172">*Components/HeadingComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

<span data-ttu-id="4c911-173">Se um componente contiver um elemento HTML com uma letra maiúscula ou minúscula que não corresponda a um nome de componente, um aviso será emitido indicando que o elemento tem um nome inesperado.</span><span class="sxs-lookup"><span data-stu-id="4c911-173">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="4c911-174">Adicionar uma instrução `@using` para o namespace do componente disponibiliza o componente, o que remove o aviso.</span><span class="sxs-lookup"><span data-stu-id="4c911-174">Adding an `@using` statement for the component's namespace makes the component available, which removes the warning.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="4c911-175">Parâmetros do componente</span><span class="sxs-lookup"><span data-stu-id="4c911-175">Component parameters</span></span>

<span data-ttu-id="4c911-176">Os componentes podem ter *parâmetros de componente*, que são definidos usando propriedades públicas na classe de componente com o atributo `[Parameter]`.</span><span class="sxs-lookup"><span data-stu-id="4c911-176">Components can have *component parameters*, which are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="4c911-177">Use atributos para especificar argumentos para um componente na marcação.</span><span class="sxs-lookup"><span data-stu-id="4c911-177">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="4c911-178">*Componentes/ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="4c911-178">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=11-12)]

<span data-ttu-id="4c911-179">No exemplo a seguir do aplicativo de exemplo, o `ParentComponent` define o valor da propriedade `Title` do `ChildComponent`.</span><span class="sxs-lookup"><span data-stu-id="4c911-179">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="4c911-180">*Páginas/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="4c911-180">*Pages/ParentComponent.razor*:</span></span>

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

## <a name="child-content"></a><span data-ttu-id="4c911-181">Conteúdo filho</span><span class="sxs-lookup"><span data-stu-id="4c911-181">Child content</span></span>

<span data-ttu-id="4c911-182">Os componentes podem definir o conteúdo de outro componente.</span><span class="sxs-lookup"><span data-stu-id="4c911-182">Components can set the content of another component.</span></span> <span data-ttu-id="4c911-183">O componente de atribuição fornece o conteúdo entre as marcas que especificam o componente de recebimento.</span><span class="sxs-lookup"><span data-stu-id="4c911-183">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="4c911-184">No exemplo a seguir, o `ChildComponent` tem uma propriedade `ChildContent` que representa um `RenderFragment`, que representa um segmento de interface do usuário a ser renderizado.</span><span class="sxs-lookup"><span data-stu-id="4c911-184">In the following example, the `ChildComponent` has a `ChildContent` property that represents a `RenderFragment`, which represents a segment of UI to render.</span></span> <span data-ttu-id="4c911-185">O valor de `ChildContent` é posicionado na marcação do componente onde o conteúdo deve ser renderizado.</span><span class="sxs-lookup"><span data-stu-id="4c911-185">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="4c911-186">O valor de `ChildContent` é recebido do componente pai e renderizado dentro do `panel-body`do painel de inicialização.</span><span class="sxs-lookup"><span data-stu-id="4c911-186">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="4c911-187">*Componentes/ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="4c911-187">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="4c911-188">A propriedade que recebe o conteúdo de `RenderFragment` deve ser nomeada `ChildContent` por convenção.</span><span class="sxs-lookup"><span data-stu-id="4c911-188">The property receiving the `RenderFragment` content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="4c911-189">O `ParentComponent` no aplicativo de exemplo pode fornecer conteúdo para renderizar o `ChildComponent` colocando o conteúdo dentro das marcas de `<ChildComponent>`.</span><span class="sxs-lookup"><span data-stu-id="4c911-189">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="4c911-190">*Páginas/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="4c911-190">*Pages/ParentComponent.razor*:</span></span>

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

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="4c911-191">Atributo nivelamento e parâmetros arbitrários</span><span class="sxs-lookup"><span data-stu-id="4c911-191">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="4c911-192">Os componentes podem capturar e renderizar atributos adicionais além dos parâmetros declarados do componente.</span><span class="sxs-lookup"><span data-stu-id="4c911-192">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="4c911-193">Atributos adicionais podem ser capturados em um dicionário e, em seguida, *splatted* em um elemento quando o componente é renderizado usando a diretiva [`@attributes`](xref:mvc/views/razor#attributes) Razor.</span><span class="sxs-lookup"><span data-stu-id="4c911-193">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`](xref:mvc/views/razor#attributes) Razor directive.</span></span> <span data-ttu-id="4c911-194">Esse cenário é útil ao definir um componente que produz um elemento de marcação que dá suporte a uma variedade de personalizações.</span><span class="sxs-lookup"><span data-stu-id="4c911-194">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="4c911-195">Por exemplo, pode ser entediante definir atributos separadamente para um `<input>` que ofereça suporte a muitos parâmetros.</span><span class="sxs-lookup"><span data-stu-id="4c911-195">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="4c911-196">No exemplo a seguir, o primeiro elemento `<input>` (`id="useIndividualParams"`) usa parâmetros de componente individuais, enquanto o segundo elemento `<input>` (`id="useAttributesDict"`) usa o atributo nivelamento:</span><span class="sxs-lookup"><span data-stu-id="4c911-196">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

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

<span data-ttu-id="4c911-197">O tipo do parâmetro deve implementar `IEnumerable<KeyValuePair<string, object>>` com chaves de cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="4c911-197">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="4c911-198">Usar `IReadOnlyDictionary<string, object>` também é uma opção nesse cenário.</span><span class="sxs-lookup"><span data-stu-id="4c911-198">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="4c911-199">Os elementos `<input>` renderizados usando ambas as abordagens são idênticos:</span><span class="sxs-lookup"><span data-stu-id="4c911-199">The rendered `<input>` elements using both approaches is identical:</span></span>

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

<span data-ttu-id="4c911-200">Para aceitar atributos arbitrários, defina um parâmetro de componente usando o atributo `[Parameter]` com a propriedade `CaptureUnmatchedValues` definida como `true`:</span><span class="sxs-lookup"><span data-stu-id="4c911-200">To accept arbitrary attributes, define a component parameter using the `[Parameter]` attribute with the `CaptureUnmatchedValues` property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="4c911-201">A propriedade `CaptureUnmatchedValues` em `[Parameter]` permite que o parâmetro corresponda a todos os atributos que não correspondem a nenhum outro parâmetro.</span><span class="sxs-lookup"><span data-stu-id="4c911-201">The `CaptureUnmatchedValues` property on `[Parameter]` allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="4c911-202">Um componente só pode definir um único parâmetro com `CaptureUnmatchedValues`.</span><span class="sxs-lookup"><span data-stu-id="4c911-202">A component can only define a single parameter with `CaptureUnmatchedValues`.</span></span> <span data-ttu-id="4c911-203">O tipo de propriedade usado com `CaptureUnmatchedValues` deve ser atribuível de `Dictionary<string, object>` com chaves de cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="4c911-203">The property type used with `CaptureUnmatchedValues` must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="4c911-204">`IEnumerable<KeyValuePair<string, object>>` ou `IReadOnlyDictionary<string, object>` também são opções neste cenário.</span><span class="sxs-lookup"><span data-stu-id="4c911-204">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="4c911-205">A posição de `@attributes` em relação à posição dos atributos do elemento é importante.</span><span class="sxs-lookup"><span data-stu-id="4c911-205">The position of `@attributes` relative to the position of element attributes is important.</span></span> <span data-ttu-id="4c911-206">Quando `@attributes` são splatted no elemento, os atributos são processados da direita para a esquerda (último a primeiro).</span><span class="sxs-lookup"><span data-stu-id="4c911-206">When `@attributes` are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="4c911-207">Considere o exemplo a seguir de um componente que consome um componente `Child`:</span><span class="sxs-lookup"><span data-stu-id="4c911-207">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="4c911-208">*ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="4c911-208">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="4c911-209">*ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="4c911-209">*ChildComponent.razor*:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="4c911-210">O atributo `extra` do componente de `Child` é definido à direita de `@attributes`.</span><span class="sxs-lookup"><span data-stu-id="4c911-210">The `Child` component's `extra` attribute is set to the right of `@attributes`.</span></span> <span data-ttu-id="4c911-211">O `<div>` renderizado do componente `Parent` contém `extra="5"` quando passa pelo atributo adicional porque os atributos são processados da direita para a esquerda (último a primeiro):</span><span class="sxs-lookup"><span data-stu-id="4c911-211">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="4c911-212">No exemplo a seguir, a ordem de `extra` e `@attributes` é revertida na `<div>`do componente `Child`:</span><span class="sxs-lookup"><span data-stu-id="4c911-212">In the following example, the order of `extra` and `@attributes` is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="4c911-213">*ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="4c911-213">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="4c911-214">*ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="4c911-214">*ChildComponent.razor*:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="4c911-215">O `<div>` renderizado no componente `Parent` contém `extra="10"` quando passado pelo atributo adicional:</span><span class="sxs-lookup"><span data-stu-id="4c911-215">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="data-binding"></a><span data-ttu-id="4c911-216">Associação de dados</span><span class="sxs-lookup"><span data-stu-id="4c911-216">Data binding</span></span>

<span data-ttu-id="4c911-217">A vinculação de dados para os componentes e os elementos DOM é realizada com o atributo [`@bind`](xref:mvc/views/razor#bind) .</span><span class="sxs-lookup"><span data-stu-id="4c911-217">Data binding to both components and DOM elements is accomplished with the [`@bind`](xref:mvc/views/razor#bind) attribute.</span></span> <span data-ttu-id="4c911-218">O exemplo a seguir associa uma propriedade `CurrentValue` ao valor da caixa de texto:</span><span class="sxs-lookup"><span data-stu-id="4c911-218">The following example binds a `CurrentValue` property to the text box's value:</span></span>

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="4c911-219">Quando a caixa de texto perde o foco, o valor da propriedade é atualizado.</span><span class="sxs-lookup"><span data-stu-id="4c911-219">When the text box loses focus, the property's value is updated.</span></span>

<span data-ttu-id="4c911-220">A caixa de texto é atualizada na interface do usuário somente quando o componente é renderizado, não em resposta à alteração do valor da propriedade.</span><span class="sxs-lookup"><span data-stu-id="4c911-220">The text box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="4c911-221">Como os componentes são renderizados após a execução do código do manipulador de eventos, as atualizações de propriedade *geralmente* são refletidas na interface do usuário imediatamente após um manipulador de eventos ser disparado.</span><span class="sxs-lookup"><span data-stu-id="4c911-221">Since components render themselves after event handler code executes, property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="4c911-222">O uso de `@bind` com a propriedade `CurrentValue` (`<input @bind="CurrentValue" />`) é essencialmente equivalente ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="4c911-222">Using `@bind` with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="4c911-223">Quando o componente é renderizado, a `value` do elemento input é proveniente da propriedade `CurrentValue`.</span><span class="sxs-lookup"><span data-stu-id="4c911-223">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="4c911-224">Quando o usuário digita na caixa de texto e altera o foco do elemento, o evento `onchange` é acionado e a propriedade `CurrentValue` é definida como o valor alterado.</span><span class="sxs-lookup"><span data-stu-id="4c911-224">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="4c911-225">Na realidade, a geração de código é mais complexa porque `@bind` trata casos em que conversões de tipo são executadas.</span><span class="sxs-lookup"><span data-stu-id="4c911-225">In reality, the code generation is more complex because `@bind` handles cases where type conversions are performed.</span></span> <span data-ttu-id="4c911-226">Em princípio, `@bind` associa o valor atual de uma expressão a um atributo `value` e manipula as alterações usando o manipulador registrado.</span><span class="sxs-lookup"><span data-stu-id="4c911-226">In principle, `@bind` associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="4c911-227">Além de manipular `onchange` eventos com a sintaxe `@bind`, uma propriedade ou um campo pode ser associado usando outros eventos, especificando um atributo [`@bind-value`](xref:mvc/views/razor#bind) com um parâmetro `event` ([`@bind-value:event`](xref:mvc/views/razor#bind)).</span><span class="sxs-lookup"><span data-stu-id="4c911-227">In addition to handling `onchange` events with `@bind` syntax, a property or field can be bound using other events by specifying an [`@bind-value`](xref:mvc/views/razor#bind) attribute with an `event` parameter ([`@bind-value:event`](xref:mvc/views/razor#bind)).</span></span> <span data-ttu-id="4c911-228">O exemplo a seguir associa a propriedade `CurrentValue` para o evento `oninput`:</span><span class="sxs-lookup"><span data-stu-id="4c911-228">The following example binds the `CurrentValue` property for the `oninput` event:</span></span>

```razor
<input @bind-value="CurrentValue" @bind-value:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="4c911-229">Ao contrário de `onchange`, que é disparado quando o elemento perde o foco, `oninput` é acionado quando o valor da caixa de texto é alterado.</span><span class="sxs-lookup"><span data-stu-id="4c911-229">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="4c911-230">`@bind-value` no exemplo anterior associa:</span><span class="sxs-lookup"><span data-stu-id="4c911-230">`@bind-value` in the preceding example binds:</span></span>

* <span data-ttu-id="4c911-231">A expressão especificada (`CurrentValue`) para o atributo `value` do elemento.</span><span class="sxs-lookup"><span data-stu-id="4c911-231">The specified expression (`CurrentValue`) to the element's `value` attribute.</span></span>
* <span data-ttu-id="4c911-232">Um delegado de evento de alteração para o evento especificado por `@bind-value:event`.</span><span class="sxs-lookup"><span data-stu-id="4c911-232">A change event delegate to the event specified by `@bind-value:event`.</span></span>

<span data-ttu-id="4c911-233">**Valores não analisáveis**</span><span class="sxs-lookup"><span data-stu-id="4c911-233">**Unparsable values**</span></span>

<span data-ttu-id="4c911-234">Quando um usuário fornece um valor não analisável para um elemento de ligação de valores, o valor não analisável é revertido automaticamente para seu valor anterior quando o evento de ligação é disparado.</span><span class="sxs-lookup"><span data-stu-id="4c911-234">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="4c911-235">Considere o seguinte cenário:</span><span class="sxs-lookup"><span data-stu-id="4c911-235">Consider the following scenario:</span></span>

* <span data-ttu-id="4c911-236">Um elemento `<input>` está associado a um tipo de `int` com um valor inicial de `123`:</span><span class="sxs-lookup"><span data-stu-id="4c911-236">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* <span data-ttu-id="4c911-237">O usuário atualiza o valor do elemento para `123.45` na página e altera o foco do elemento.</span><span class="sxs-lookup"><span data-stu-id="4c911-237">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="4c911-238">No cenário anterior, o valor do elemento é revertido para `123`.</span><span class="sxs-lookup"><span data-stu-id="4c911-238">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="4c911-239">Quando o valor `123.45` é rejeitado em favor do valor original de `123`, o usuário entende que seu valor não foi aceito.</span><span class="sxs-lookup"><span data-stu-id="4c911-239">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="4c911-240">Por padrão, a associação aplica-se ao evento de `onchange` do elemento (`@bind="{PROPERTY OR FIELD}"`).</span><span class="sxs-lookup"><span data-stu-id="4c911-240">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="4c911-241">Use `@bind-value="{PROPERTY OR FIELD}" @bind-value:event={EVENT}` para definir um evento diferente.</span><span class="sxs-lookup"><span data-stu-id="4c911-241">Use `@bind-value="{PROPERTY OR FIELD}" @bind-value:event={EVENT}` to set a different event.</span></span> <span data-ttu-id="4c911-242">Para o evento de `oninput` (`@bind-value:event="oninput"`), a reversão ocorre após qualquer pressionamento de tecla que introduz um valor não analisável.</span><span class="sxs-lookup"><span data-stu-id="4c911-242">For the `oninput` event (`@bind-value:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="4c911-243">Ao direcionar o evento de `oninput` com um tipo de associação de `int`, um usuário é impedido de digitar um caractere `.`.</span><span class="sxs-lookup"><span data-stu-id="4c911-243">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="4c911-244">Um caractere `.` é removido imediatamente e, portanto, o usuário recebe comentários imediatos de que apenas números inteiros são permitidos.</span><span class="sxs-lookup"><span data-stu-id="4c911-244">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="4c911-245">Há cenários em que a reversão do valor no evento `oninput` não é ideal, por exemplo, quando o usuário deve ter permissão para limpar um valor de `<input>` não analisável.</span><span class="sxs-lookup"><span data-stu-id="4c911-245">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="4c911-246">As alternativas incluem:</span><span class="sxs-lookup"><span data-stu-id="4c911-246">Alternatives include:</span></span>

* <span data-ttu-id="4c911-247">Não use o evento `oninput`.</span><span class="sxs-lookup"><span data-stu-id="4c911-247">Don't use the `oninput` event.</span></span> <span data-ttu-id="4c911-248">Use o evento de `onchange` padrão (`@bind="{PROPERTY OR FIELD}"`), em que um valor inválido não é revertido até que o elemento perca o foco.</span><span class="sxs-lookup"><span data-stu-id="4c911-248">Use the default `onchange` event (`@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="4c911-249">Associar a um tipo anulável, como `int?` ou `string`, e fornecer uma lógica personalizada para manipular entradas inválidas.</span><span class="sxs-lookup"><span data-stu-id="4c911-249">Bind to a nullable type, such as `int?` or `string`, and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="4c911-250">Use um [componente de validação de formulário](xref:blazor/forms-validation), como `InputNumber` ou `InputDate`.</span><span class="sxs-lookup"><span data-stu-id="4c911-250">Use a [form validation component](xref:blazor/forms-validation), such as `InputNumber` or `InputDate`.</span></span> <span data-ttu-id="4c911-251">Os componentes de validação de formulário têm suporte interno para gerenciar entradas inválidas.</span><span class="sxs-lookup"><span data-stu-id="4c911-251">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="4c911-252">Componentes de validação de formulário:</span><span class="sxs-lookup"><span data-stu-id="4c911-252">Form validation components:</span></span>
  * <span data-ttu-id="4c911-253">Permitir que o usuário forneça erros de entrada e de validação inválidos no `EditContext`associado.</span><span class="sxs-lookup"><span data-stu-id="4c911-253">Permit the user to provide invalid input and receive validation errors on the associated `EditContext`.</span></span>
  * <span data-ttu-id="4c911-254">Exibir erros de validação na interface de usuário sem interferir no usuário inserindo dados adicionais do WebForms.</span><span class="sxs-lookup"><span data-stu-id="4c911-254">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

<span data-ttu-id="4c911-255">**Globalização**</span><span class="sxs-lookup"><span data-stu-id="4c911-255">**Globalization**</span></span>

<span data-ttu-id="4c911-256">`@bind` valores são formatados para exibição e analisados usando as regras da cultura atual.</span><span class="sxs-lookup"><span data-stu-id="4c911-256">`@bind` values are formatted for display and parsed using the current culture's rules.</span></span>

<span data-ttu-id="4c911-257">A cultura atual pode ser acessada a partir da propriedade <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="4c911-257">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="4c911-258">[CultureInfo. InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) é usado para os seguintes tipos de campo (`<input type="{TYPE}" />`):</span><span class="sxs-lookup"><span data-stu-id="4c911-258">[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="4c911-259">Os tipos de campo anteriores:</span><span class="sxs-lookup"><span data-stu-id="4c911-259">The preceding field types:</span></span>

* <span data-ttu-id="4c911-260">São exibidos usando suas regras de formatação baseadas em navegador apropriadas.</span><span class="sxs-lookup"><span data-stu-id="4c911-260">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="4c911-261">Não pode conter texto de forma livre.</span><span class="sxs-lookup"><span data-stu-id="4c911-261">Can't contain free-form text.</span></span>
* <span data-ttu-id="4c911-262">Fornecer características de interação do usuário com base na implementação do navegador.</span><span class="sxs-lookup"><span data-stu-id="4c911-262">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="4c911-263">Os seguintes tipos de campo têm requisitos de formatação específicos e atualmente não têm suporte do Blazor porque eles não têm suporte em todos os principais navegadores:</span><span class="sxs-lookup"><span data-stu-id="4c911-263">The following field types have specific formatting requirements and aren't currently supported by Blazor because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="4c911-264">`@bind` dá suporte ao parâmetro `@bind:culture` para fornecer um <xref:System.Globalization.CultureInfo?displayProperty=fullName> para análise e formatação de um valor.</span><span class="sxs-lookup"><span data-stu-id="4c911-264">`@bind` supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="4c911-265">Não é recomendável especificar uma cultura ao usar os tipos de campo `date` e `number`.</span><span class="sxs-lookup"><span data-stu-id="4c911-265">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="4c911-266">`date` e `number` têm suporte interno a Blazor que fornece a cultura necessária.</span><span class="sxs-lookup"><span data-stu-id="4c911-266">`date` and `number` have built-in Blazor support that provides the required culture.</span></span>

<span data-ttu-id="4c911-267">Para obter informações sobre como definir a cultura do usuário, consulte a seção [localização](#localization) .</span><span class="sxs-lookup"><span data-stu-id="4c911-267">For information on how to set the user's culture, see the [Localization](#localization) section.</span></span>

<span data-ttu-id="4c911-268">**Formatar cadeias de caracteres**</span><span class="sxs-lookup"><span data-stu-id="4c911-268">**Format strings**</span></span>

<span data-ttu-id="4c911-269">A vinculação de dados funciona com <xref:System.DateTime> cadeias de caracteres de formato usando [`@bind:format`](xref:mvc/views/razor#bind).</span><span class="sxs-lookup"><span data-stu-id="4c911-269">Data binding works with <xref:System.DateTime> format strings using [`@bind:format`](xref:mvc/views/razor#bind).</span></span> <span data-ttu-id="4c911-270">Outras expressões de formato, como formatos de moeda ou número, não estão disponíveis no momento.</span><span class="sxs-lookup"><span data-stu-id="4c911-270">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="4c911-271">No código anterior, o tipo de campo (`type`) do elemento `<input>` tem como padrão `text`.</span><span class="sxs-lookup"><span data-stu-id="4c911-271">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="4c911-272">`@bind:format` tem suporte para ligar os seguintes tipos .NET:</span><span class="sxs-lookup"><span data-stu-id="4c911-272">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="4c911-273"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="4c911-273"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="4c911-274"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="4c911-274"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="4c911-275">O atributo `@bind:format` especifica o formato de data a ser aplicado ao `value` do elemento `<input>`.</span><span class="sxs-lookup"><span data-stu-id="4c911-275">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="4c911-276">O formato também é usado para analisar o valor quando ocorre um evento `onchange`.</span><span class="sxs-lookup"><span data-stu-id="4c911-276">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="4c911-277">Não é recomendável especificar um formato para o tipo de campo `date` porque Blazor tem suporte interno para formatar datas.</span><span class="sxs-lookup"><span data-stu-id="4c911-277">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="4c911-278">Apesar da recomendação, use apenas o formato de data `yyyy-MM-dd` para que a associação funcione corretamente se um formato for fornecido com o tipo de campo `date`:</span><span class="sxs-lookup"><span data-stu-id="4c911-278">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to work correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

<span data-ttu-id="4c911-279">**Parâmetros do componente**</span><span class="sxs-lookup"><span data-stu-id="4c911-279">**Component parameters**</span></span>

<span data-ttu-id="4c911-280">A associação reconhece os parâmetros do componente, em que `@bind-{property}` pode associar um valor de propriedade entre componentes.</span><span class="sxs-lookup"><span data-stu-id="4c911-280">Binding recognizes component parameters, where `@bind-{property}` can bind a property value across components.</span></span>

<span data-ttu-id="4c911-281">O componente filho a seguir (`ChildComponent`) tem um parâmetro de componente `Year` e `YearChanged` retorno de chamada:</span><span class="sxs-lookup"><span data-stu-id="4c911-281">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

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

<span data-ttu-id="4c911-282">`EventCallback<T>` é explicado na seção [EventCallback](#eventcallback) .</span><span class="sxs-lookup"><span data-stu-id="4c911-282">`EventCallback<T>` is explained in the [EventCallback](#eventcallback) section.</span></span>

<span data-ttu-id="4c911-283">O componente pai a seguir usa `ChildComponent` e associa o parâmetro `ParentYear` do pai ao parâmetro `Year` no componente filho:</span><span class="sxs-lookup"><span data-stu-id="4c911-283">The following parent component uses `ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component:</span></span>

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

<span data-ttu-id="4c911-284">Carregar o `ParentComponent` produz a seguinte marcação:</span><span class="sxs-lookup"><span data-stu-id="4c911-284">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="4c911-285">Se o valor da propriedade `ParentYear` for alterado selecionando o botão na `ParentComponent`, a propriedade `Year` do `ChildComponent` será atualizada.</span><span class="sxs-lookup"><span data-stu-id="4c911-285">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="4c911-286">O novo valor de `Year` é renderizado na interface do usuário quando a `ParentComponent` é reprocessada:</span><span class="sxs-lookup"><span data-stu-id="4c911-286">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="4c911-287">O parâmetro `Year` é acoplável porque tem um evento complementar `YearChanged` que corresponde ao tipo do parâmetro `Year`.</span><span class="sxs-lookup"><span data-stu-id="4c911-287">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="4c911-288">Por convenção, `<ChildComponent @bind-Year="ParentYear" />` é essencialmente equivalente a escrever:</span><span class="sxs-lookup"><span data-stu-id="4c911-288">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="4c911-289">Em geral, uma propriedade pode ser associada a um manipulador de eventos correspondente usando `@bind-property:event` atributo.</span><span class="sxs-lookup"><span data-stu-id="4c911-289">In general, a property can be bound to a corresponding event handler using `@bind-property:event` attribute.</span></span> <span data-ttu-id="4c911-290">Por exemplo, a propriedade `MyProp` pode ser associada a `MyEventHandler` usando os dois atributos a seguir:</span><span class="sxs-lookup"><span data-stu-id="4c911-290">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

<span data-ttu-id="4c911-291">**Botões de opção**</span><span class="sxs-lookup"><span data-stu-id="4c911-291">**Radio buttons**</span></span>

<span data-ttu-id="4c911-292">Para obter informações sobre a associação a botões de opção em um formulário, consulte <xref:blazor/forms-validation#work-with-radio-buttons>.</span><span class="sxs-lookup"><span data-stu-id="4c911-292">For information on binding to radio buttons in a form, see <xref:blazor/forms-validation#work-with-radio-buttons>.</span></span>

## <a name="event-handling"></a><span data-ttu-id="4c911-293">Manipulação de eventos</span><span class="sxs-lookup"><span data-stu-id="4c911-293">Event handling</span></span>

<span data-ttu-id="4c911-294">Os componentes do Razor fornecem recursos de manipulação de eventos.</span><span class="sxs-lookup"><span data-stu-id="4c911-294">Razor components provide event handling features.</span></span> <span data-ttu-id="4c911-295">Para um atributo de elemento HTML chamado `on{EVENT}` (por exemplo, `onclick` e `onsubmit`) com um valor de tipo delegado, os componentes do Razor tratam o valor do atributo como um manipulador de eventos.</span><span class="sxs-lookup"><span data-stu-id="4c911-295">For an HTML element attribute named `on{EVENT}` (for example, `onclick` and `onsubmit`) with a delegate-typed value, Razor components treats the attribute's value as an event handler.</span></span> <span data-ttu-id="4c911-296">O nome do atributo é sempre formatado [`@on{EVENT}`](xref:mvc/views/razor#onevent).</span><span class="sxs-lookup"><span data-stu-id="4c911-296">The attribute's name is always formatted [`@on{EVENT}`](xref:mvc/views/razor#onevent).</span></span>

<span data-ttu-id="4c911-297">O código a seguir chama o método `UpdateHeading` quando o botão é selecionado na interface do usuário:</span><span class="sxs-lookup"><span data-stu-id="4c911-297">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="4c911-298">O código a seguir chama o método `CheckChanged` quando a caixa de seleção é alterada na interface do usuário:</span><span class="sxs-lookup"><span data-stu-id="4c911-298">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="4c911-299">Os manipuladores de eventos também podem ser assíncronos e retornar um <xref:System.Threading.Tasks.Task>.</span><span class="sxs-lookup"><span data-stu-id="4c911-299">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="4c911-300">Não é necessário chamar [StateHasChanged](xref:blazor/lifecycle#state-changes)manualmente.</span><span class="sxs-lookup"><span data-stu-id="4c911-300">There's no need to manually call [StateHasChanged](xref:blazor/lifecycle#state-changes).</span></span> <span data-ttu-id="4c911-301">As exceções são registradas quando ocorrem.</span><span class="sxs-lookup"><span data-stu-id="4c911-301">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="4c911-302">No exemplo a seguir, `UpdateHeading` é chamado de forma assíncrona quando o botão é selecionado:</span><span class="sxs-lookup"><span data-stu-id="4c911-302">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

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

### <a name="event-argument-types"></a><span data-ttu-id="4c911-303">Tipos de argumento de evento</span><span class="sxs-lookup"><span data-stu-id="4c911-303">Event argument types</span></span>

<span data-ttu-id="4c911-304">Para alguns eventos, são permitidos tipos de argumento de evento.</span><span class="sxs-lookup"><span data-stu-id="4c911-304">For some events, event argument types are permitted.</span></span> <span data-ttu-id="4c911-305">Se o acesso a um desses tipos de evento não for necessário, ele não será necessário na chamada do método.</span><span class="sxs-lookup"><span data-stu-id="4c911-305">If access to one of these event types isn't necessary, it isn't required in the method call.</span></span>

<span data-ttu-id="4c911-306">Os `EventArgs` com suporte são mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="4c911-306">Supported `EventArgs` are shown in the following table.</span></span>

| <span data-ttu-id="4c911-307">Event</span><span class="sxs-lookup"><span data-stu-id="4c911-307">Event</span></span>            | <span data-ttu-id="4c911-308">Classe</span><span class="sxs-lookup"><span data-stu-id="4c911-308">Class</span></span>                | <span data-ttu-id="4c911-309">Eventos e observações do DOM</span><span class="sxs-lookup"><span data-stu-id="4c911-309">DOM events and notes</span></span> |
| ---------------- | -------------------- | -------------------- |
| <span data-ttu-id="4c911-310">Área de Transferência</span><span class="sxs-lookup"><span data-stu-id="4c911-310">Clipboard</span></span>        | `ClipboardEventArgs` | <span data-ttu-id="4c911-311">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="4c911-311">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="4c911-312">Arrastar</span><span class="sxs-lookup"><span data-stu-id="4c911-312">Drag</span></span>             | `DragEventArgs`      | <span data-ttu-id="4c911-313">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="4c911-313">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="4c911-314">`DataTransfer` e `DataTransferItem` manter os dados do item arrastados.</span><span class="sxs-lookup"><span data-stu-id="4c911-314">`DataTransfer` and `DataTransferItem` hold dragged item data.</span></span> |
| <span data-ttu-id="4c911-315">Erro do</span><span class="sxs-lookup"><span data-stu-id="4c911-315">Error</span></span>            | `ErrorEventArgs`     | `onerror` |
| <span data-ttu-id="4c911-316">Event</span><span class="sxs-lookup"><span data-stu-id="4c911-316">Event</span></span>            | `EventArgs`          | <span data-ttu-id="4c911-317">*Geral*</span><span class="sxs-lookup"><span data-stu-id="4c911-317">*General*</span></span><br><span data-ttu-id="4c911-318">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="4c911-318">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="4c911-319">*Área de transferência*</span><span class="sxs-lookup"><span data-stu-id="4c911-319">*Clipboard*</span></span><br><span data-ttu-id="4c911-320">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="4c911-320">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="4c911-321">*Entrada*</span><span class="sxs-lookup"><span data-stu-id="4c911-321">*Input*</span></span><br><span data-ttu-id="4c911-322">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="4c911-322">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="4c911-323">*Mídia*</span><span class="sxs-lookup"><span data-stu-id="4c911-323">*Media*</span></span><br><span data-ttu-id="4c911-324">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="4c911-324">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span> |
| <span data-ttu-id="4c911-325">Foco</span><span class="sxs-lookup"><span data-stu-id="4c911-325">Focus</span></span>            | `FocusEventArgs`     | <span data-ttu-id="4c911-326">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="4c911-326">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="4c911-327">Não inclui suporte para `relatedTarget`.</span><span class="sxs-lookup"><span data-stu-id="4c911-327">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="4c911-328">Entrada</span><span class="sxs-lookup"><span data-stu-id="4c911-328">Input</span></span>            | `ChangeEventArgs`    | <span data-ttu-id="4c911-329">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="4c911-329">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="4c911-330">Teclado</span><span class="sxs-lookup"><span data-stu-id="4c911-330">Keyboard</span></span>         | `KeyboardEventArgs`  | <span data-ttu-id="4c911-331">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="4c911-331">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="4c911-332">Mouse</span><span class="sxs-lookup"><span data-stu-id="4c911-332">Mouse</span></span>            | `MouseEventArgs`     | <span data-ttu-id="4c911-333">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="4c911-333">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="4c911-334">Ponteiro do mouse</span><span class="sxs-lookup"><span data-stu-id="4c911-334">Mouse pointer</span></span>    | `PointerEventArgs`   | <span data-ttu-id="4c911-335">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="4c911-335">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="4c911-336">Botão de rolagem do mouse</span><span class="sxs-lookup"><span data-stu-id="4c911-336">Mouse wheel</span></span>      | `WheelEventArgs`     | <span data-ttu-id="4c911-337">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="4c911-337">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="4c911-338">Progresso</span><span class="sxs-lookup"><span data-stu-id="4c911-338">Progress</span></span>         | `ProgressEventArgs`  | <span data-ttu-id="4c911-339">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="4c911-339">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="4c911-340">Toque</span><span class="sxs-lookup"><span data-stu-id="4c911-340">Touch</span></span>            | `TouchEventArgs`     | <span data-ttu-id="4c911-341">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="4c911-341">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="4c911-342">`TouchPoint` representa um único ponto de contato em um dispositivo sensível ao toque.</span><span class="sxs-lookup"><span data-stu-id="4c911-342">`TouchPoint` represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="4c911-343">Para obter informações sobre as propriedades e o comportamento de manipulação de eventos dos eventos na tabela anterior, consulte [classes EventArgs na fonte de referência (a ramificação dotnet/aspnetcore Release/3.1)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="4c911-343">For information on the properties and event handling behavior of the events in the preceding table, see [EventArgs classes in the reference source (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span></span>

### <a name="lambda-expressions"></a><span data-ttu-id="4c911-344">Expressões lambda</span><span class="sxs-lookup"><span data-stu-id="4c911-344">Lambda expressions</span></span>

<span data-ttu-id="4c911-345">As expressões lambda também podem ser usadas:</span><span class="sxs-lookup"><span data-stu-id="4c911-345">Lambda expressions can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="4c911-346">Geralmente, é conveniente fechar valores adicionais, como ao iterar em um conjunto de elementos.</span><span class="sxs-lookup"><span data-stu-id="4c911-346">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="4c911-347">O exemplo a seguir cria três botões, cada um dos quais chamadas `UpdateHeading` passando um argumento de evento (`MouseEventArgs`) e seu número de botão (`buttonNumber`) quando selecionado na interface do usuário:</span><span class="sxs-lookup"><span data-stu-id="4c911-347">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (`MouseEventArgs`) and its button number (`buttonNumber`) when selected in the UI:</span></span>

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
> <span data-ttu-id="4c911-348">**Não** use a variável de loop (`i`) em um loop de `for` diretamente em uma expressão lambda.</span><span class="sxs-lookup"><span data-stu-id="4c911-348">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="4c911-349">Caso contrário, a mesma variável é usada por todas as expressões lambda, fazendo com que `i`valor seja o mesmo em todos os lambdas.</span><span class="sxs-lookup"><span data-stu-id="4c911-349">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="4c911-350">Sempre Capture seu valor em uma variável local (`buttonNumber` no exemplo anterior) e, em seguida, use-o.</span><span class="sxs-lookup"><span data-stu-id="4c911-350">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

### <a name="eventcallback"></a><span data-ttu-id="4c911-351">EventCallback</span><span class="sxs-lookup"><span data-stu-id="4c911-351">EventCallback</span></span>

<span data-ttu-id="4c911-352">Um cenário comum com componentes aninhados é o desejo de executar o método de um componente pai quando ocorre um evento de componente filho&mdash;por exemplo, quando um evento de `onclick` ocorre no filho.</span><span class="sxs-lookup"><span data-stu-id="4c911-352">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs&mdash;for example, when an `onclick` event occurs in the child.</span></span> <span data-ttu-id="4c911-353">Para expor eventos entre componentes, use um `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="4c911-353">To expose events across components, use an `EventCallback`.</span></span> <span data-ttu-id="4c911-354">Um componente pai pode atribuir um método de retorno de chamada para o `EventCallback`de um componente filho.</span><span class="sxs-lookup"><span data-stu-id="4c911-354">A parent component can assign a callback method to a child component's `EventCallback`.</span></span>

<span data-ttu-id="4c911-355">O `ChildComponent` no aplicativo de exemplo (*Components/ChildComponent. Razor*) demonstra como o manipulador de `onclick` de um botão é configurado para receber um delegado de `EventCallback` da `ParentComponent`de exemplo.</span><span class="sxs-lookup"><span data-stu-id="4c911-355">The `ChildComponent` in the sample app (*Components/ChildComponent.razor*) demonstrates how a button's `onclick` handler is set up to receive an `EventCallback` delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="4c911-356">O `EventCallback` é digitado com `MouseEventArgs`, que é apropriado para um evento de `onclick` de um dispositivo periférico:</span><span class="sxs-lookup"><span data-stu-id="4c911-356">The `EventCallback` is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="4c911-357">O `ParentComponent` define o `EventCallback<T>` do filho como seu método `ShowMessage`.</span><span class="sxs-lookup"><span data-stu-id="4c911-357">The `ParentComponent` sets the child's `EventCallback<T>` to its `ShowMessage` method.</span></span>

<span data-ttu-id="4c911-358">*Páginas/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="4c911-358">*Pages/ParentComponent.razor*:</span></span>

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

<span data-ttu-id="4c911-359">Quando o botão estiver selecionado na `ChildComponent`:</span><span class="sxs-lookup"><span data-stu-id="4c911-359">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="4c911-360">O método `ShowMessage` do `ParentComponent`é chamado.</span><span class="sxs-lookup"><span data-stu-id="4c911-360">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="4c911-361">`messageText` é atualizado e exibido no `ParentComponent`.</span><span class="sxs-lookup"><span data-stu-id="4c911-361">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="4c911-362">Uma chamada para [StateHasChanged](xref:blazor/lifecycle#state-changes) não é necessária no método do retorno de chamada (`ShowMessage`).</span><span class="sxs-lookup"><span data-stu-id="4c911-362">A call to [StateHasChanged](xref:blazor/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="4c911-363">`StateHasChanged` é chamado automaticamente para renderizar novamente o `ParentComponent`, assim como os eventos filho disparam o reprocessamento de componentes em manipuladores de eventos que são executados dentro do filho.</span><span class="sxs-lookup"><span data-stu-id="4c911-363">`StateHasChanged` is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="4c911-364">`EventCallback` e `EventCallback<T>` permitir delegados assíncronos.</span><span class="sxs-lookup"><span data-stu-id="4c911-364">`EventCallback` and `EventCallback<T>` permit asynchronous delegates.</span></span> <span data-ttu-id="4c911-365">`EventCallback<T>` é fortemente tipado e requer um tipo de argumento específico.</span><span class="sxs-lookup"><span data-stu-id="4c911-365">`EventCallback<T>` is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="4c911-366">`EventCallback` é digitado de forma fraca e permite qualquer tipo de argumento.</span><span class="sxs-lookup"><span data-stu-id="4c911-366">`EventCallback` is weakly typed and allows any argument type.</span></span>

```razor
<p><b>@messageText</b></p>

@{ var message = "Default Text"; }

<ChildComponent 
    OnClick="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />

@code {
    private string messageText;
}
```

<span data-ttu-id="4c911-367">Invoque um `EventCallback` ou `EventCallback<T>` com `InvokeAsync` e aguardar a <xref:System.Threading.Tasks.Task>:</span><span class="sxs-lookup"><span data-stu-id="4c911-367">Invoke an `EventCallback` or `EventCallback<T>` with `InvokeAsync` and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="4c911-368">Use `EventCallback` e `EventCallback<T>` para manipulação de eventos e parâmetros de componente de associação.</span><span class="sxs-lookup"><span data-stu-id="4c911-368">Use `EventCallback` and `EventCallback<T>` for event handling and binding component parameters.</span></span>

<span data-ttu-id="4c911-369">Prefira a `EventCallback<T>` com rigidez de tipos sobre `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="4c911-369">Prefer the strongly typed `EventCallback<T>` over `EventCallback`.</span></span> <span data-ttu-id="4c911-370">`EventCallback<T>` fornece melhores comentários de erro para os usuários do componente.</span><span class="sxs-lookup"><span data-stu-id="4c911-370">`EventCallback<T>` provides better error feedback to users of the component.</span></span> <span data-ttu-id="4c911-371">Semelhante a outros manipuladores de eventos de interface do usuário, especificar o parâmetro de evento é opcional.</span><span class="sxs-lookup"><span data-stu-id="4c911-371">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="4c911-372">Use `EventCallback` quando não houver valor passado para o retorno de chamada.</span><span class="sxs-lookup"><span data-stu-id="4c911-372">Use `EventCallback` when there's no value passed to the callback.</span></span>

### <a name="prevent-default-actions"></a><span data-ttu-id="4c911-373">Impedir ações padrão</span><span class="sxs-lookup"><span data-stu-id="4c911-373">Prevent default actions</span></span>

<span data-ttu-id="4c911-374">Use o atributo de diretiva [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) para evitar a ação padrão para um evento.</span><span class="sxs-lookup"><span data-stu-id="4c911-374">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="4c911-375">Quando uma chave é selecionada em um dispositivo de entrada e o foco do elemento está em uma caixa de texto, um navegador normalmente exibe o caractere da chave na caixa de texto.</span><span class="sxs-lookup"><span data-stu-id="4c911-375">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="4c911-376">No exemplo a seguir, o comportamento padrão é impedido pela especificação do atributo de diretiva `@onkeypress:preventDefault`.</span><span class="sxs-lookup"><span data-stu-id="4c911-376">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="4c911-377">O contador é incrementado e a chave de **+** não é capturada no valor do elemento de `<input>`:</span><span class="sxs-lookup"><span data-stu-id="4c911-377">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

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

<span data-ttu-id="4c911-378">Especificar o atributo `@on{EVENT}:preventDefault` sem um valor é equivalente a `@on{EVENT}:preventDefault="true"`.</span><span class="sxs-lookup"><span data-stu-id="4c911-378">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="4c911-379">O valor do atributo também pode ser uma expressão.</span><span class="sxs-lookup"><span data-stu-id="4c911-379">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="4c911-380">No exemplo a seguir, `_shouldPreventDefault` é um campo de `bool` definido como `true` ou `false`:</span><span class="sxs-lookup"><span data-stu-id="4c911-380">In the following example, `_shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="_shouldPreventDefault" />
```

<span data-ttu-id="4c911-381">Um manipulador de eventos não é necessário para impedir a ação padrão.</span><span class="sxs-lookup"><span data-stu-id="4c911-381">An event handler isn't required to prevent the default action.</span></span> <span data-ttu-id="4c911-382">O manipulador de eventos e a prevenção de cenários de ação padrão podem ser usados de forma independente.</span><span class="sxs-lookup"><span data-stu-id="4c911-382">The event handler and prevent default action scenarios can be used independently.</span></span>

### <a name="stop-event-propagation"></a><span data-ttu-id="4c911-383">Parar a propagação do evento</span><span class="sxs-lookup"><span data-stu-id="4c911-383">Stop event propagation</span></span>

<span data-ttu-id="4c911-384">Use o atributo de diretiva [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) para parar a propagação do evento.</span><span class="sxs-lookup"><span data-stu-id="4c911-384">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="4c911-385">No exemplo a seguir, marcar a caixa de seleção impede que eventos de clique do segundo filho `<div>` sejam propagados para o `<div>`pai:</span><span class="sxs-lookup"><span data-stu-id="4c911-385">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

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

## <a name="chained-bind"></a><span data-ttu-id="4c911-386">Associação encadeada</span><span class="sxs-lookup"><span data-stu-id="4c911-386">Chained bind</span></span>

<span data-ttu-id="4c911-387">Um cenário comum é encadear um parâmetro de associação de dados a um elemento de página na saída do componente.</span><span class="sxs-lookup"><span data-stu-id="4c911-387">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="4c911-388">Esse cenário é chamado de *Associação encadeada* porque vários níveis de associação ocorrem simultaneamente.</span><span class="sxs-lookup"><span data-stu-id="4c911-388">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="4c911-389">Uma associação encadeada não pode ser implementada com `@bind` sintaxe no elemento da página.</span><span class="sxs-lookup"><span data-stu-id="4c911-389">A chained bind can't be implemented with `@bind` syntax in the page's element.</span></span> <span data-ttu-id="4c911-390">O manipulador de eventos e o valor devem ser especificados separadamente.</span><span class="sxs-lookup"><span data-stu-id="4c911-390">The event handler and value must be specified separately.</span></span> <span data-ttu-id="4c911-391">Um componente pai, no entanto, pode usar `@bind` sintaxe com o parâmetro do componente.</span><span class="sxs-lookup"><span data-stu-id="4c911-391">A parent component, however, can use `@bind` syntax with the component's parameter.</span></span>

<span data-ttu-id="4c911-392">O componente `PasswordField` a seguir (*passwordField. Razor*):</span><span class="sxs-lookup"><span data-stu-id="4c911-392">The following `PasswordField` component (*PasswordField.razor*):</span></span>

* <span data-ttu-id="4c911-393">Define um valor de elemento de `<input>` para uma propriedade `Password`.</span><span class="sxs-lookup"><span data-stu-id="4c911-393">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="4c911-394">Expõe as alterações da propriedade `Password` para um componente pai com um [EventCallback](#eventcallback).</span><span class="sxs-lookup"><span data-stu-id="4c911-394">Exposes changes of the `Password` property to a parent component with an [EventCallback](#eventcallback).</span></span>

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

<span data-ttu-id="4c911-395">O componente `PasswordField` é usado em outro componente:</span><span class="sxs-lookup"><span data-stu-id="4c911-395">The `PasswordField` component is used in another component:</span></span>

```razor
<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

<span data-ttu-id="4c911-396">Para executar verificações ou interceptar erros na senha no exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="4c911-396">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="4c911-397">Crie um campo de backup para `Password` (`password` no código de exemplo a seguir).</span><span class="sxs-lookup"><span data-stu-id="4c911-397">Create a backing field for `Password` (`password` in the following example code).</span></span>
* <span data-ttu-id="4c911-398">Execute os erros de verificação ou interceptação no setter de `Password`.</span><span class="sxs-lookup"><span data-stu-id="4c911-398">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="4c911-399">O exemplo a seguir fornecerá comentários imediatos para o usuário se um espaço for usado no valor da senha:</span><span class="sxs-lookup"><span data-stu-id="4c911-399">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

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

## <a name="capture-references-to-components"></a><span data-ttu-id="4c911-400">Capturar referências a componentes</span><span class="sxs-lookup"><span data-stu-id="4c911-400">Capture references to components</span></span>

<span data-ttu-id="4c911-401">As referências de componente fornecem uma maneira de fazer referência a uma instância de componente para que você possa emitir comandos para essa instância, como `Show` ou `Reset`.</span><span class="sxs-lookup"><span data-stu-id="4c911-401">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="4c911-402">Para capturar uma referência de componente:</span><span class="sxs-lookup"><span data-stu-id="4c911-402">To capture a component reference:</span></span>

* <span data-ttu-id="4c911-403">Adicione um atributo [`@ref`](xref:mvc/views/razor#ref) ao componente filho.</span><span class="sxs-lookup"><span data-stu-id="4c911-403">Add an [`@ref`](xref:mvc/views/razor#ref) attribute to the child component.</span></span>
* <span data-ttu-id="4c911-404">Defina um campo com o mesmo tipo do componente filho.</span><span class="sxs-lookup"><span data-stu-id="4c911-404">Define a field with the same type as the child component.</span></span>

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

<span data-ttu-id="4c911-405">Quando o componente é renderizado, o campo `loginDialog` é populado com a instância de componente filho `MyLoginDialog`.</span><span class="sxs-lookup"><span data-stu-id="4c911-405">When the component is rendered, the `loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="4c911-406">Em seguida, você pode invocar os métodos .NET na instância do componente.</span><span class="sxs-lookup"><span data-stu-id="4c911-406">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="4c911-407">A variável `loginDialog` é populada apenas depois que o componente é renderizado e sua saída inclui o elemento `MyLoginDialog`.</span><span class="sxs-lookup"><span data-stu-id="4c911-407">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="4c911-408">Até esse ponto, não há nada a fazer referência.</span><span class="sxs-lookup"><span data-stu-id="4c911-408">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="4c911-409">Para manipular referências de componentes após a conclusão da renderização do componente, use os [métodos OnAfterRenderAsync ou OnAfterRender](xref:blazor/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="4c911-409">To manipulate components references after the component has finished rendering, use the [OnAfterRenderAsync or OnAfterRender methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="4c911-410">Embora a captura de referências de componente use uma sintaxe semelhante à [captura de referências de elemento](xref:blazor/javascript-interop#capture-references-to-elements), ela não é um recurso de [interoperabilidade do JavaScript](xref:blazor/javascript-interop) .</span><span class="sxs-lookup"><span data-stu-id="4c911-410">While capturing component references use a similar syntax to [capturing element references](xref:blazor/javascript-interop#capture-references-to-elements), it isn't a [JavaScript interop](xref:blazor/javascript-interop) feature.</span></span> <span data-ttu-id="4c911-411">As referências de componente não são passadas para o código JavaScript&mdash;elas são usadas apenas no código .NET.</span><span class="sxs-lookup"><span data-stu-id="4c911-411">Component references aren't passed to JavaScript code&mdash;they're only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="4c911-412">Não **use referências** de componente para converter o estado dos componentes filho.</span><span class="sxs-lookup"><span data-stu-id="4c911-412">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="4c911-413">Em vez disso, use parâmetros declarativos normais para passar dados para componentes filho.</span><span class="sxs-lookup"><span data-stu-id="4c911-413">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="4c911-414">O uso de parâmetros declarativos normais resulta em componentes filho que são reprocessados nos horários corretos automaticamente.</span><span class="sxs-lookup"><span data-stu-id="4c911-414">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="4c911-415">Invocar métodos de componente externamente para atualizar o estado</span><span class="sxs-lookup"><span data-stu-id="4c911-415">Invoke component methods externally to update state</span></span>

Blazor<span data-ttu-id="4c911-416"> usa uma `SynchronizationContext` para impor um único thread lógico de execução.</span><span class="sxs-lookup"><span data-stu-id="4c911-416"> uses a `SynchronizationContext` to enforce a single logical thread of execution.</span></span> <span data-ttu-id="4c911-417">Os métodos de [ciclo de vida](xref:blazor/lifecycle) de um componente e quaisquer retornos de chamada de evento gerados por Blazor são executados nesse `SynchronizationContext`.</span><span class="sxs-lookup"><span data-stu-id="4c911-417">A component's [lifecycle methods](xref:blazor/lifecycle) and any event callbacks that are raised by Blazor are executed on this `SynchronizationContext`.</span></span> <span data-ttu-id="4c911-418">No caso de um componente precisar ser atualizado com base em um evento externo, como um temporizador ou outras notificações, use o método `InvokeAsync`, que será expedido para o `SynchronizationContext`de Blazor.</span><span class="sxs-lookup"><span data-stu-id="4c911-418">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which will dispatch to Blazor's `SynchronizationContext`.</span></span>

<span data-ttu-id="4c911-419">Por exemplo, considere um *serviço de notificação* que pode notificar qualquer componente de escuta do estado atualizado:</span><span class="sxs-lookup"><span data-stu-id="4c911-419">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

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

<span data-ttu-id="4c911-420">Uso do `NotifierService` para atualizar um componente:</span><span class="sxs-lookup"><span data-stu-id="4c911-420">Usage of the `NotifierService` to update a component:</span></span>

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

<span data-ttu-id="4c911-421">No exemplo anterior, `NotifierService` invoca o método `OnNotify` do componente fora da `SynchronizationContext`do Blazor.</span><span class="sxs-lookup"><span data-stu-id="4c911-421">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's `SynchronizationContext`.</span></span> <span data-ttu-id="4c911-422">`InvokeAsync` é usado para alternar para o contexto correto e enfileirar uma renderização.</span><span class="sxs-lookup"><span data-stu-id="4c911-422">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="4c911-423">Use \@chave para controlar a preservação de elementos e componentes</span><span class="sxs-lookup"><span data-stu-id="4c911-423">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="4c911-424">Ao renderizar uma lista de elementos ou componentes e os elementos ou componentes subsequentemente mudam, o algoritmo de diferenciação do Blazordeve decidir qual dos elementos ou componentes anteriores podem ser mantidos e como os objetos de modelo devem ser mapeados para eles.</span><span class="sxs-lookup"><span data-stu-id="4c911-424">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="4c911-425">Normalmente, esse processo é automático e pode ser ignorado, mas há casos em que você talvez queira controlar o processo.</span><span class="sxs-lookup"><span data-stu-id="4c911-425">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="4c911-426">Considere o exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="4c911-426">Consider the following example:</span></span>

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

<span data-ttu-id="4c911-427">O conteúdo da coleção de `People` pode ser alterado com entradas inseridas, excluídas ou reordenadas.</span><span class="sxs-lookup"><span data-stu-id="4c911-427">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="4c911-428">Quando o componente é rerenderizado, o componente `<DetailsEditor>` pode ser alterado para receber diferentes valores de parâmetro `Details`.</span><span class="sxs-lookup"><span data-stu-id="4c911-428">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="4c911-429">Isso pode causar um reprocessamento mais complexo do que o esperado.</span><span class="sxs-lookup"><span data-stu-id="4c911-429">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="4c911-430">Em alguns casos, a rerenderização pode levar a diferenças de comportamento visíveis, como o foco de elemento perdido.</span><span class="sxs-lookup"><span data-stu-id="4c911-430">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="4c911-431">O processo de mapeamento pode ser controlado com o atributo de diretiva `@key`.</span><span class="sxs-lookup"><span data-stu-id="4c911-431">The mapping process can be controlled with the `@key` directive attribute.</span></span> <span data-ttu-id="4c911-432">`@key` faz com que o algoritmo diff garanta a preservação de elementos ou componentes com base no valor da chave:</span><span class="sxs-lookup"><span data-stu-id="4c911-432">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

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

<span data-ttu-id="4c911-433">Quando a coleção de `People` é alterada, o algoritmo diff mantém a associação entre instâncias de `<DetailsEditor>` e `person` instâncias:</span><span class="sxs-lookup"><span data-stu-id="4c911-433">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="4c911-434">Se uma `Person` for excluída da lista de `People`, somente a instância `<DetailsEditor>` correspondente será removida da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="4c911-434">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="4c911-435">Outras instâncias permanecem inalteradas.</span><span class="sxs-lookup"><span data-stu-id="4c911-435">Other instances are left unchanged.</span></span>
* <span data-ttu-id="4c911-436">Se uma `Person` for inserida em alguma posição na lista, uma nova instância de `<DetailsEditor>` será inserida na posição correspondente.</span><span class="sxs-lookup"><span data-stu-id="4c911-436">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="4c911-437">Outras instâncias permanecem inalteradas.</span><span class="sxs-lookup"><span data-stu-id="4c911-437">Other instances are left unchanged.</span></span>
* <span data-ttu-id="4c911-438">Se `Person` entradas forem reordenadas, as instâncias de `<DetailsEditor>` correspondentes serão preservadas e reordenadas na interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="4c911-438">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="4c911-439">Em alguns cenários, o uso de `@key` minimiza a complexidade de rerenderização e evita possíveis problemas com partes com estado da alteração do DOM, como a posição do foco.</span><span class="sxs-lookup"><span data-stu-id="4c911-439">In some scenarios, use of `@key` minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="4c911-440">As chaves são locais para cada elemento ou componente de contêiner.</span><span class="sxs-lookup"><span data-stu-id="4c911-440">Keys are local to each container element or component.</span></span> <span data-ttu-id="4c911-441">As chaves não são comparadas globalmente ao longo do documento.</span><span class="sxs-lookup"><span data-stu-id="4c911-441">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="4c911-442">Quando usar \@chave</span><span class="sxs-lookup"><span data-stu-id="4c911-442">When to use \@key</span></span>

<span data-ttu-id="4c911-443">Normalmente, faz sentido usar `@key` sempre que uma lista é renderizada (por exemplo, em um bloco de `@foreach`) e um valor adequado existe para definir o `@key`.</span><span class="sxs-lookup"><span data-stu-id="4c911-443">Typically, it makes sense to use `@key` whenever a list is rendered (for example, in a `@foreach` block) and a suitable value exists to define the `@key`.</span></span>

<span data-ttu-id="4c911-444">Você também pode usar `@key` para impedir Blazor de preservar uma subárvore de elemento ou componente quando um objeto for alterado:</span><span class="sxs-lookup"><span data-stu-id="4c911-444">You can also use `@key` to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="4c911-445">Se `@currentPerson` for alterado, a diretiva de atributo `@key` forçará Blazor a descartar toda a `<div>` e seus descendentes e recriará a subárvore dentro da interface do usuário com novos elementos e componentes.</span><span class="sxs-lookup"><span data-stu-id="4c911-445">If `@currentPerson` changes, the `@key` attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="4c911-446">Isso pode ser útil se você precisar garantir que nenhum estado da interface do usuário seja preservado quando `@currentPerson` for alterado.</span><span class="sxs-lookup"><span data-stu-id="4c911-446">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="4c911-447">Quando não usar \@chave</span><span class="sxs-lookup"><span data-stu-id="4c911-447">When not to use \@key</span></span>

<span data-ttu-id="4c911-448">Há um custo de desempenho ao comparar com `@key`.</span><span class="sxs-lookup"><span data-stu-id="4c911-448">There's a performance cost when diffing with `@key`.</span></span> <span data-ttu-id="4c911-449">O custo de desempenho não é grande, mas só especifique `@key` se o controle das regras de preservação de elemento ou componente beneficiar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4c911-449">The performance cost isn't large, but only specify `@key` if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="4c911-450">Mesmo que `@key` não seja usado, Blazor preserva as instâncias de elemento filho e de componente o máximo possível.</span><span class="sxs-lookup"><span data-stu-id="4c911-450">Even if `@key` isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="4c911-451">A única vantagem de usar `@key` é o controle sobre *como* as instâncias de modelo são mapeadas para as instâncias de componente preservadas, em vez do algoritmo diff, selecionando o mapeamento.</span><span class="sxs-lookup"><span data-stu-id="4c911-451">The only advantage to using `@key` is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="4c911-452">Quais valores usar para \@chave</span><span class="sxs-lookup"><span data-stu-id="4c911-452">What values to use for \@key</span></span>

<span data-ttu-id="4c911-453">Geralmente, faz sentido fornecer um dos seguintes tipos de valor para `@key`:</span><span class="sxs-lookup"><span data-stu-id="4c911-453">Generally, it makes sense to supply one of the following kinds of value for `@key`:</span></span>

* <span data-ttu-id="4c911-454">Instâncias de objeto de modelo (por exemplo, uma instância de `Person` como no exemplo anterior).</span><span class="sxs-lookup"><span data-stu-id="4c911-454">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="4c911-455">Isso garante a preservação com base na igualdade de referência de objeto.</span><span class="sxs-lookup"><span data-stu-id="4c911-455">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="4c911-456">Identificadores exclusivos (por exemplo, valores de chave primária do tipo `int`, `string`ou `Guid`).</span><span class="sxs-lookup"><span data-stu-id="4c911-456">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="4c911-457">Verifique se os valores usados para `@key` não estão em conflito.</span><span class="sxs-lookup"><span data-stu-id="4c911-457">Ensure that values used for `@key` don't clash.</span></span> <span data-ttu-id="4c911-458">Se valores conflitantes forem detectados dentro do mesmo elemento pai, Blazor lançará uma exceção porque ele não pode mapear de forma determinística elementos ou componentes antigos para novos elementos ou componentes.</span><span class="sxs-lookup"><span data-stu-id="4c911-458">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="4c911-459">Use apenas valores distintos, como instâncias de objeto ou valores de chave primária.</span><span class="sxs-lookup"><span data-stu-id="4c911-459">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="routing"></a><span data-ttu-id="4c911-460">Roteamento</span><span class="sxs-lookup"><span data-stu-id="4c911-460">Routing</span></span>

<span data-ttu-id="4c911-461">O roteamento no Blazor é obtido fornecendo um modelo de rota para cada componente acessível no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4c911-461">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="4c911-462">Quando um arquivo Razor com uma diretiva `@page` é compilado, a classe gerada recebe um <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> especificando o modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="4c911-462">When a Razor file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="4c911-463">Em tempo de execução, o roteador procura classes de componentes com um `RouteAttribute` e renderiza qualquer componente que tenha um modelo de rota que corresponda à URL solicitada.</span><span class="sxs-lookup"><span data-stu-id="4c911-463">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

<span data-ttu-id="4c911-464">Vários modelos de rota podem ser aplicados a um componente.</span><span class="sxs-lookup"><span data-stu-id="4c911-464">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="4c911-465">O componente a seguir responde às solicitações de `/BlazorRoute` e `/DifferentBlazorRoute`.</span><span class="sxs-lookup"><span data-stu-id="4c911-465">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`.</span></span>

<span data-ttu-id="4c911-466">*Páginas/BlazorRoute. Razor*:</span><span class="sxs-lookup"><span data-stu-id="4c911-466">*Pages/BlazorRoute.razor*:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

## <a name="route-parameters"></a><span data-ttu-id="4c911-467">Parâmetros de rota</span><span class="sxs-lookup"><span data-stu-id="4c911-467">Route parameters</span></span>

<span data-ttu-id="4c911-468">Os componentes podem receber parâmetros de rota do modelo de rota fornecido na diretiva `@page`.</span><span class="sxs-lookup"><span data-stu-id="4c911-468">Components can receive route parameters from the route template provided in the `@page` directive.</span></span> <span data-ttu-id="4c911-469">O roteador usa parâmetros de rota para preencher os parâmetros de componente correspondentes.</span><span class="sxs-lookup"><span data-stu-id="4c911-469">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="4c911-470">*Páginas/RouteParameter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="4c911-470">*Pages/RouteParameter.razor*:</span></span>

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

<span data-ttu-id="4c911-471">Não há suporte para parâmetros opcionais, portanto, duas diretivas `@page` são aplicadas no exemplo acima.</span><span class="sxs-lookup"><span data-stu-id="4c911-471">Optional parameters aren't supported, so two `@page` directives are applied in the example above.</span></span> <span data-ttu-id="4c911-472">O primeiro permite a navegação para o componente sem um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="4c911-472">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="4c911-473">A segunda diretiva de `@page` usa o parâmetro `{text}` Route e atribui o valor à propriedade `Text`.</span><span class="sxs-lookup"><span data-stu-id="4c911-473">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

<span data-ttu-id="4c911-474">A sintaxe de parâmetro *catch-all* (`*`/`**`), que captura o caminho entre vários limites de pasta, **não** tem suporte em componentes do Razor ( *. Razor*).</span><span class="sxs-lookup"><span data-stu-id="4c911-474">*Catch-all* parameter syntax (`*`/`**`), which captures the path across multiple folder boundaries, is **not** supported in Razor components (*.razor*).</span></span>

## <a name="partial-class-support"></a><span data-ttu-id="4c911-475">Suporte de classe parcial</span><span class="sxs-lookup"><span data-stu-id="4c911-475">Partial class support</span></span>

<span data-ttu-id="4c911-476">Os componentes do Razor são gerados como classes parciais.</span><span class="sxs-lookup"><span data-stu-id="4c911-476">Razor components are generated as partial classes.</span></span> <span data-ttu-id="4c911-477">Os componentes do Razor são criados usando uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="4c911-477">Razor components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="4c911-478">C#o código é definido em um bloco de [`@code`](xref:mvc/views/razor#code) com marcação HTML e código do Razor em um único arquivo.</span><span class="sxs-lookup"><span data-stu-id="4c911-478">C# code is defined in an [`@code`](xref:mvc/views/razor#code) block with HTML markup and Razor code in a single file.</span></span> Blazor<span data-ttu-id="4c911-479"> modelos definem seus componentes do Razor usando essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="4c911-479"> templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="4c911-480">C#o código é colocado em um arquivo code-behind definido como uma classe parcial.</span><span class="sxs-lookup"><span data-stu-id="4c911-480">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="4c911-481">O exemplo a seguir mostra o componente de `Counter` padrão com um bloco de `@code` em um aplicativo gerado por meio de um modelo de Blazor.</span><span class="sxs-lookup"><span data-stu-id="4c911-481">The following example shows the default `Counter` component with an `@code` block in an app generated from a Blazor template.</span></span> <span data-ttu-id="4c911-482">Marcação HTML, código do Razor e C# código estão no mesmo arquivo:</span><span class="sxs-lookup"><span data-stu-id="4c911-482">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="4c911-483">*Counter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="4c911-483">*Counter.razor*:</span></span>

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

<span data-ttu-id="4c911-484">O componente `Counter` também pode ser criado usando um arquivo code-behind com uma classe parcial:</span><span class="sxs-lookup"><span data-stu-id="4c911-484">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="4c911-485">*Counter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="4c911-485">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="4c911-486">*Counter.Razor.cs*:</span><span class="sxs-lookup"><span data-stu-id="4c911-486">*Counter.razor.cs*:</span></span>

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

<span data-ttu-id="4c911-487">Adicione quaisquer namespaces necessários ao arquivo de classe parcial, conforme necessário.</span><span class="sxs-lookup"><span data-stu-id="4c911-487">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="4c911-488">Os namespaces típicos usados pelos componentes do Razor incluem:</span><span class="sxs-lookup"><span data-stu-id="4c911-488">Typical namespaces used by Razor components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

## <a name="import-components"></a><span data-ttu-id="4c911-489">Importar componentes</span><span class="sxs-lookup"><span data-stu-id="4c911-489">Import components</span></span>

<span data-ttu-id="4c911-490">O namespace de um componente criado com o Razor baseia-se em (em ordem de prioridade):</span><span class="sxs-lookup"><span data-stu-id="4c911-490">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="4c911-491">[`@namespace`](xref:mvc/views/razor#namespace) designação na marcação de arquivo Razor ( *. Razor*) (`@namespace BlazorSample.MyNamespace`).</span><span class="sxs-lookup"><span data-stu-id="4c911-491">[`@namespace`](xref:mvc/views/razor#namespace) designation in Razor file (*.razor*) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="4c911-492">O `RootNamespace` do projeto no arquivo de projeto (`<RootNamespace>BlazorSample</RootNamespace>`).</span><span class="sxs-lookup"><span data-stu-id="4c911-492">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="4c911-493">O nome do projeto, obtido do nome do arquivo do projeto ( *. csproj*) e o caminho da raiz do projeto para o componente.</span><span class="sxs-lookup"><span data-stu-id="4c911-493">The project name, taken from the project file's file name (*.csproj*), and the path from the project root to the component.</span></span> <span data-ttu-id="4c911-494">Por exemplo, a estrutura resolve *{raiz do projeto}/pages/index.Razor* (*BlazorSample. csproj*) para o namespace `BlazorSample.Pages`.</span><span class="sxs-lookup"><span data-stu-id="4c911-494">For example, the framework resolves *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample.csproj*) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="4c911-495">Os componentes C# seguem regras de associação de nome.</span><span class="sxs-lookup"><span data-stu-id="4c911-495">Components follow C# name binding rules.</span></span> <span data-ttu-id="4c911-496">Para o componente `Index` neste exemplo, os componentes no escopo são todos os componentes:</span><span class="sxs-lookup"><span data-stu-id="4c911-496">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="4c911-497">Na mesma pasta, *páginas*.</span><span class="sxs-lookup"><span data-stu-id="4c911-497">In the same folder, *Pages*.</span></span>
  * <span data-ttu-id="4c911-498">Os componentes na raiz do projeto que não especificam explicitamente um namespace diferente.</span><span class="sxs-lookup"><span data-stu-id="4c911-498">The components in the project's root that don't explicitly specify a different namespace.</span></span>

<span data-ttu-id="4c911-499">Os componentes definidos em um namespace diferente são trazidos para o escopo usando a diretiva [`@using`](xref:mvc/views/razor#using) do Razor.</span><span class="sxs-lookup"><span data-stu-id="4c911-499">Components defined in a different namespace are brought into scope using Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span>

<span data-ttu-id="4c911-500">Se outro componente, `NavMenu.razor`, existir na pasta *BlazorSample/Shared/* , o componente poderá ser usado em `Index.razor` com a seguinte instrução `@using`:</span><span class="sxs-lookup"><span data-stu-id="4c911-500">If another component, `NavMenu.razor`, exists in the *BlazorSample/Shared/* folder, the component can be used in `Index.razor` with the following `@using` statement:</span></span>

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

<span data-ttu-id="4c911-501">Os componentes também podem ser referenciados usando seus nomes totalmente qualificados, o que não requer a diretiva [`@using`](xref:mvc/views/razor#using) :</span><span class="sxs-lookup"><span data-stu-id="4c911-501">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`](xref:mvc/views/razor#using) directive:</span></span>

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> <span data-ttu-id="4c911-502">Não há suporte para a qualificação de `global::`.</span><span class="sxs-lookup"><span data-stu-id="4c911-502">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="4c911-503">Não há suporte para a importação de componentes com instruções de `using` com alias (por exemplo, `@using Foo = Bar`).</span><span class="sxs-lookup"><span data-stu-id="4c911-503">Importing components with aliased `using` statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="4c911-504">Não há suporte para nomes parcialmente qualificados.</span><span class="sxs-lookup"><span data-stu-id="4c911-504">Partially qualified names aren't supported.</span></span> <span data-ttu-id="4c911-505">Por exemplo, não há suporte para a adição de `@using BlazorSample` e referência `NavMenu.razor` com `<Shared.NavMenu></Shared.NavMenu>`.</span><span class="sxs-lookup"><span data-stu-id="4c911-505">For example, adding `@using BlazorSample` and referencing `NavMenu.razor` with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="4c911-506">Atributos de elemento HTML condicional</span><span class="sxs-lookup"><span data-stu-id="4c911-506">Conditional HTML element attributes</span></span>

<span data-ttu-id="4c911-507">Os atributos de elemento HTML são processados condicionalmente com base no valor do .NET.</span><span class="sxs-lookup"><span data-stu-id="4c911-507">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="4c911-508">Se o valor for `false` ou `null`, o atributo não será renderizado.</span><span class="sxs-lookup"><span data-stu-id="4c911-508">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="4c911-509">Se o valor é `true`, o atributo é processado minimizado.</span><span class="sxs-lookup"><span data-stu-id="4c911-509">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="4c911-510">No exemplo a seguir, `IsCompleted` determina se `checked` é renderizado na marcação do elemento:</span><span class="sxs-lookup"><span data-stu-id="4c911-510">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="4c911-511">Se `IsCompleted` for `true`, a caixa de seleção será renderizada como:</span><span class="sxs-lookup"><span data-stu-id="4c911-511">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="4c911-512">Se `IsCompleted` for `false`, a caixa de seleção será renderizada como:</span><span class="sxs-lookup"><span data-stu-id="4c911-512">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="4c911-513">Para obter mais informações, consulte <xref:mvc/views/razor>.</span><span class="sxs-lookup"><span data-stu-id="4c911-513">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="4c911-514">Alguns atributos HTML, como [pressionados pelo Aria](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), não funcionam corretamente quando o tipo .net é um `bool`.</span><span class="sxs-lookup"><span data-stu-id="4c911-514">Some HTML attributes, such as [aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="4c911-515">Nesses casos, use um tipo de `string` em vez de um `bool`.</span><span class="sxs-lookup"><span data-stu-id="4c911-515">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="4c911-516">HTML bruto</span><span class="sxs-lookup"><span data-stu-id="4c911-516">Raw HTML</span></span>

<span data-ttu-id="4c911-517">Normalmente, as cadeias de caracteres são renderizadas usando nós de texto DOM, o que significa que qualquer marcação que ela possa conter será ignorada e tratada como texto literal.</span><span class="sxs-lookup"><span data-stu-id="4c911-517">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="4c911-518">Para renderizar HTML bruto, empacote o conteúdo HTML em um valor `MarkupString`.</span><span class="sxs-lookup"><span data-stu-id="4c911-518">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="4c911-519">O valor é analisado como HTML ou SVG e inserido no DOM.</span><span class="sxs-lookup"><span data-stu-id="4c911-519">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="4c911-520">O processamento de HTML bruto construído a partir de qualquer fonte não confiável é um **risco à segurança** e deve ser evitado!</span><span class="sxs-lookup"><span data-stu-id="4c911-520">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="4c911-521">O exemplo a seguir mostra como usar o tipo de `MarkupString` para adicionar um bloco de conteúdo HTML estático à saída renderizada de um componente:</span><span class="sxs-lookup"><span data-stu-id="4c911-521">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="templated-components"></a><span data-ttu-id="4c911-522">Componentes de modelo</span><span class="sxs-lookup"><span data-stu-id="4c911-522">Templated components</span></span>

<span data-ttu-id="4c911-523">Componentes modelo são componentes que aceitam um ou mais modelos de interface do usuário como parâmetros, que podem ser usados como parte da lógica de renderização do componente.</span><span class="sxs-lookup"><span data-stu-id="4c911-523">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="4c911-524">Os componentes de modelo permitem que você crie componentes de nível superior que são mais reutilizáveis do que os componentes normais.</span><span class="sxs-lookup"><span data-stu-id="4c911-524">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="4c911-525">Alguns exemplos incluem:</span><span class="sxs-lookup"><span data-stu-id="4c911-525">A couple of examples include:</span></span>

* <span data-ttu-id="4c911-526">Um componente de tabela que permite que um usuário especifique modelos para o cabeçalho, as linhas e o rodapé da tabela.</span><span class="sxs-lookup"><span data-stu-id="4c911-526">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="4c911-527">Um componente de lista que permite que um usuário especifique um modelo para renderizar itens em uma lista.</span><span class="sxs-lookup"><span data-stu-id="4c911-527">A list component that allows a user to specify a template for rendering items in a list.</span></span>

### <a name="template-parameters"></a><span data-ttu-id="4c911-528">Parâmetros do Modelo</span><span class="sxs-lookup"><span data-stu-id="4c911-528">Template parameters</span></span>

<span data-ttu-id="4c911-529">Um componente modelo é definido especificando um ou mais parâmetros de componente do tipo `RenderFragment` ou `RenderFragment<T>`.</span><span class="sxs-lookup"><span data-stu-id="4c911-529">A templated component is defined by specifying one or more component parameters of type `RenderFragment` or `RenderFragment<T>`.</span></span> <span data-ttu-id="4c911-530">Um fragmento de renderização representa um segmento de interface do usuário a ser renderizado.</span><span class="sxs-lookup"><span data-stu-id="4c911-530">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="4c911-531">`RenderFragment<T>` usa um parâmetro de tipo que pode ser especificado quando o fragmento de renderização é invocado.</span><span class="sxs-lookup"><span data-stu-id="4c911-531">`RenderFragment<T>` takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="4c911-532">`TableTemplate` componente:</span><span class="sxs-lookup"><span data-stu-id="4c911-532">`TableTemplate` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

<span data-ttu-id="4c911-533">Ao usar um componente modelo, os parâmetros do modelo podem ser especificados usando elementos filho que correspondem aos nomes dos parâmetros (`TableHeader` e `RowTemplate` no exemplo a seguir):</span><span class="sxs-lookup"><span data-stu-id="4c911-533">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

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

### <a name="template-context-parameters"></a><span data-ttu-id="4c911-534">Parâmetros de contexto de modelo</span><span class="sxs-lookup"><span data-stu-id="4c911-534">Template context parameters</span></span>

<span data-ttu-id="4c911-535">Os argumentos de componente do tipo `RenderFragment<T>` passados como elementos têm um parâmetro implícito denominado `context` (por exemplo, do exemplo de código anterior, `@context.PetId`), mas você pode alterar o nome do parâmetro usando o atributo `Context` no elemento filho.</span><span class="sxs-lookup"><span data-stu-id="4c911-535">Component arguments of type `RenderFragment<T>` passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="4c911-536">No exemplo a seguir, o atributo `Context` do elemento `RowTemplate` especifica o parâmetro `pet`:</span><span class="sxs-lookup"><span data-stu-id="4c911-536">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

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

<span data-ttu-id="4c911-537">Como alternativa, você pode especificar o atributo `Context` no elemento Component.</span><span class="sxs-lookup"><span data-stu-id="4c911-537">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="4c911-538">O atributo `Context` especificado se aplica a todos os parâmetros de modelo especificados.</span><span class="sxs-lookup"><span data-stu-id="4c911-538">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="4c911-539">Isso pode ser útil quando você deseja especificar o nome do parâmetro de conteúdo para conteúdo filho implícito (sem qualquer elemento filho de disposição).</span><span class="sxs-lookup"><span data-stu-id="4c911-539">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="4c911-540">No exemplo a seguir, o atributo `Context` aparece no elemento `TableTemplate` e se aplica a todos os parâmetros de modelo:</span><span class="sxs-lookup"><span data-stu-id="4c911-540">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

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

### <a name="generic-typed-components"></a><span data-ttu-id="4c911-541">Componentes de tipo genérico</span><span class="sxs-lookup"><span data-stu-id="4c911-541">Generic-typed components</span></span>

<span data-ttu-id="4c911-542">Os componentes modelo são geralmente digitados genericamente.</span><span class="sxs-lookup"><span data-stu-id="4c911-542">Templated components are often generically typed.</span></span> <span data-ttu-id="4c911-543">Por exemplo, um componente de `ListViewTemplate` genérico pode ser usado para processar `IEnumerable<T>` valores.</span><span class="sxs-lookup"><span data-stu-id="4c911-543">For example, a generic `ListViewTemplate` component can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="4c911-544">Para definir um componente genérico, use a diretiva [`@typeparam`](xref:mvc/views/razor#typeparam) para especificar parâmetros de tipo:</span><span class="sxs-lookup"><span data-stu-id="4c911-544">To define a generic component, use the [`@typeparam`](xref:mvc/views/razor#typeparam) directive to specify type parameters:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

<span data-ttu-id="4c911-545">Ao usar componentes de tipos genéricos, o parâmetro de tipo é inferido, se possível:</span><span class="sxs-lookup"><span data-stu-id="4c911-545">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```razor
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

<span data-ttu-id="4c911-546">Caso contrário, o parâmetro de tipo deve ser especificado explicitamente usando um atributo que corresponda ao nome do parâmetro de tipo.</span><span class="sxs-lookup"><span data-stu-id="4c911-546">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="4c911-547">No exemplo a seguir, `TItem="Pet"` especifica o tipo:</span><span class="sxs-lookup"><span data-stu-id="4c911-547">In the following example, `TItem="Pet"` specifies the type:</span></span>

```razor
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="4c911-548">Valores e parâmetros em cascata</span><span class="sxs-lookup"><span data-stu-id="4c911-548">Cascading values and parameters</span></span>

<span data-ttu-id="4c911-549">Em alguns cenários, é inconveniente fluir dados de um componente ancestral para um componente descendente usando [parâmetros de componente](#component-parameters), especialmente quando há várias camadas de componente.</span><span class="sxs-lookup"><span data-stu-id="4c911-549">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="4c911-550">Valores e parâmetros em cascata resolvem esse problema fornecendo uma maneira conveniente para um componente ancestral fornecer um valor para todos os seus componentes descendentes.</span><span class="sxs-lookup"><span data-stu-id="4c911-550">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="4c911-551">Valores e parâmetros em cascata também fornecem uma abordagem para que os componentes sejam coordenados.</span><span class="sxs-lookup"><span data-stu-id="4c911-551">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="4c911-552">Exemplo de tema</span><span class="sxs-lookup"><span data-stu-id="4c911-552">Theme example</span></span>

<span data-ttu-id="4c911-553">No exemplo a seguir do aplicativo de exemplo, a classe `ThemeInfo` especifica as informações do tema para fluir para baixo na hierarquia do componente para que todos os botões de uma determinada parte do aplicativo compartilhem o mesmo estilo.</span><span class="sxs-lookup"><span data-stu-id="4c911-553">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="4c911-554">*UIThemeClasses/ThemeInfo.cs*:</span><span class="sxs-lookup"><span data-stu-id="4c911-554">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="4c911-555">Um componente ancestral pode fornecer um valor em cascata usando o componente de valor em cascata.</span><span class="sxs-lookup"><span data-stu-id="4c911-555">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="4c911-556">O componente `CascadingValue` encapsula uma subárvore da hierarquia de componentes e fornece um único valor para todos os componentes dentro dessa subárvore.</span><span class="sxs-lookup"><span data-stu-id="4c911-556">The `CascadingValue` component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="4c911-557">Por exemplo, o aplicativo de exemplo especifica informações de tema (`ThemeInfo`) em um dos layouts do aplicativo como um parâmetro em cascata para todos os componentes que compõem o corpo do layout da propriedade `@Body`.</span><span class="sxs-lookup"><span data-stu-id="4c911-557">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="4c911-558">`ButtonClass` é atribuído um valor de `btn-success` no componente de layout.</span><span class="sxs-lookup"><span data-stu-id="4c911-558">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="4c911-559">Qualquer componente descendente pode consumir essa propriedade por meio do `ThemeInfo` objeto em cascata.</span><span class="sxs-lookup"><span data-stu-id="4c911-559">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="4c911-560">`CascadingValuesParametersLayout` componente:</span><span class="sxs-lookup"><span data-stu-id="4c911-560">`CascadingValuesParametersLayout` component:</span></span>

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

<span data-ttu-id="4c911-561">Para fazer uso de valores em cascata, os componentes declaram parâmetros em cascata usando o atributo `[CascadingParameter]`.</span><span class="sxs-lookup"><span data-stu-id="4c911-561">To make use of cascading values, components declare cascading parameters using the `[CascadingParameter]` attribute.</span></span> <span data-ttu-id="4c911-562">Os valores em cascata são associados a parâmetros em cascata por tipo.</span><span class="sxs-lookup"><span data-stu-id="4c911-562">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="4c911-563">No aplicativo de exemplo, o componente `CascadingValuesParametersTheme` associa o valor de `ThemeInfo` em cascata a um parâmetro em cascata.</span><span class="sxs-lookup"><span data-stu-id="4c911-563">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="4c911-564">O parâmetro é usado para definir a classe CSS para um dos botões exibidos pelo componente.</span><span class="sxs-lookup"><span data-stu-id="4c911-564">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="4c911-565">`CascadingValuesParametersTheme` componente:</span><span class="sxs-lookup"><span data-stu-id="4c911-565">`CascadingValuesParametersTheme` component:</span></span>

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

<span data-ttu-id="4c911-566">Para propagar vários valores do mesmo tipo dentro da mesma subárvore, forneça uma cadeia de caracteres `Name` exclusiva para cada componente `CascadingValue` e sua `CascadingParameter`correspondente.</span><span class="sxs-lookup"><span data-stu-id="4c911-566">To cascade multiple values of the same type within the same subtree, provide a unique `Name` string to each `CascadingValue` component and its corresponding `CascadingParameter`.</span></span> <span data-ttu-id="4c911-567">No exemplo a seguir, dois componentes `CascadingValue` em cascata diferentes instâncias de `MyCascadingType` por nome:</span><span class="sxs-lookup"><span data-stu-id="4c911-567">In the following example, two `CascadingValue` components cascade different instances of `MyCascadingType` by name:</span></span>

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

<span data-ttu-id="4c911-568">Em um componente descendente, os parâmetros em cascata recebem seus valores dos valores em cascata correspondentes no componente ancestral por nome:</span><span class="sxs-lookup"><span data-stu-id="4c911-568">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="4c911-569">Exemplo de TabSet</span><span class="sxs-lookup"><span data-stu-id="4c911-569">TabSet example</span></span>

<span data-ttu-id="4c911-570">Os parâmetros em cascata também permitem que os componentes colaborem na hierarquia do componente.</span><span class="sxs-lookup"><span data-stu-id="4c911-570">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="4c911-571">Por exemplo, considere o exemplo de *TabSet* a seguir no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="4c911-571">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="4c911-572">O aplicativo de exemplo tem uma interface `ITab` que as guias implementam:</span><span class="sxs-lookup"><span data-stu-id="4c911-572">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

<span data-ttu-id="4c911-573">O componente `CascadingValuesParametersTabSet` usa o componente `TabSet`, que contém vários componentes `Tab`:</span><span class="sxs-lookup"><span data-stu-id="4c911-573">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

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

<span data-ttu-id="4c911-574">Os componentes de `Tab` filhos não são passados explicitamente como parâmetros para o `TabSet`.</span><span class="sxs-lookup"><span data-stu-id="4c911-574">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="4c911-575">Em vez disso, os componentes de `Tab` filhos fazem parte do conteúdo filho do `TabSet`.</span><span class="sxs-lookup"><span data-stu-id="4c911-575">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="4c911-576">No entanto, o `TabSet` ainda precisa saber sobre cada componente `Tab` para que ele possa renderizar os cabeçalhos e a guia ativa. Para habilitar essa coordenação sem a necessidade de código adicional, o componente `TabSet` *pode fornecer a si mesmo como um valor em cascata* que é então coletado pelos componentes do `Tab` descendente.</span><span class="sxs-lookup"><span data-stu-id="4c911-576">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="4c911-577">`TabSet` componente:</span><span class="sxs-lookup"><span data-stu-id="4c911-577">`TabSet` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

<span data-ttu-id="4c911-578">Os componentes `Tab` descendentes capturam o `TabSet` recipiente como um parâmetro em cascata, de modo que os componentes `Tab` se adicionam à `TabSet` e coordenam em qual guia está ativa.</span><span class="sxs-lookup"><span data-stu-id="4c911-578">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="4c911-579">`Tab` componente:</span><span class="sxs-lookup"><span data-stu-id="4c911-579">`Tab` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a><span data-ttu-id="4c911-580">Modelos do Razor</span><span class="sxs-lookup"><span data-stu-id="4c911-580">Razor templates</span></span>

<span data-ttu-id="4c911-581">Os fragmentos de renderização podem ser definidos usando a sintaxe de modelo Razor.</span><span class="sxs-lookup"><span data-stu-id="4c911-581">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="4c911-582">Os modelos Razor são uma maneira de definir um trecho de interface do usuário e assumir o seguinte formato:</span><span class="sxs-lookup"><span data-stu-id="4c911-582">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="4c911-583">O exemplo a seguir ilustra como especificar `RenderFragment` e valores de `RenderFragment<T>` e renderizar modelos diretamente em um componente.</span><span class="sxs-lookup"><span data-stu-id="4c911-583">The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>` values and render templates directly in a component.</span></span> <span data-ttu-id="4c911-584">Os fragmentos de renderização também podem ser passados como argumentos para [componentes de modelo](#templated-components).</span><span class="sxs-lookup"><span data-stu-id="4c911-584">Render fragments can also be passed as arguments to [templated components](#templated-components).</span></span>

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

<span data-ttu-id="4c911-585">Saída renderizada do código anterior:</span><span class="sxs-lookup"><span data-stu-id="4c911-585">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Your pet's name is Rex.</p>
```

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="4c911-586">Lógica RenderTreeBuilder manual</span><span class="sxs-lookup"><span data-stu-id="4c911-586">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="4c911-587">`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` fornece métodos para manipular componentes e elementos, incluindo a criação manual de componentes C# no código.</span><span class="sxs-lookup"><span data-stu-id="4c911-587">`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="4c911-588">O uso de `RenderTreeBuilder` para criar componentes é um cenário avançado.</span><span class="sxs-lookup"><span data-stu-id="4c911-588">Use of `RenderTreeBuilder` to create components is an advanced scenario.</span></span> <span data-ttu-id="4c911-589">Um componente malformado (por exemplo, uma marca de marcação não fechada) pode resultar em um comportamento indefinido.</span><span class="sxs-lookup"><span data-stu-id="4c911-589">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="4c911-590">Considere o seguinte componente de `PetDetails`, que pode ser compilado manualmente em outro componente:</span><span class="sxs-lookup"><span data-stu-id="4c911-590">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="4c911-591">No exemplo a seguir, o loop no método `CreateComponent` gera três componentes `PetDetails`.</span><span class="sxs-lookup"><span data-stu-id="4c911-591">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="4c911-592">Ao chamar `RenderTreeBuilder` métodos para criar os componentes (`OpenComponent` e `AddAttribute`), os números de sequência são números de linha de código-fonte.</span><span class="sxs-lookup"><span data-stu-id="4c911-592">When calling `RenderTreeBuilder` methods to create the components (`OpenComponent` and `AddAttribute`), sequence numbers are source code line numbers.</span></span> <span data-ttu-id="4c911-593">O algoritmo de diferença de Blazor depende dos números de sequência correspondentes a linhas distintas de código, não a invocações de chamada distintas.</span><span class="sxs-lookup"><span data-stu-id="4c911-593">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="4c911-594">Ao criar um componente com métodos `RenderTreeBuilder`, codifique os argumentos para números de sequência.</span><span class="sxs-lookup"><span data-stu-id="4c911-594">When creating a component with `RenderTreeBuilder` methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="4c911-595">**O uso de um cálculo ou contador para gerar o número de sequência pode levar a um desempenho insatisfatório.**</span><span class="sxs-lookup"><span data-stu-id="4c911-595">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="4c911-596">Para obter mais informações, consulte os [números de sequência relacionados à seção números de linha de código e não ordem de execução](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) .</span><span class="sxs-lookup"><span data-stu-id="4c911-596">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="4c911-597">`BuiltContent` componente:</span><span class="sxs-lookup"><span data-stu-id="4c911-597">`BuiltContent` component:</span></span>

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

> [!WARNING]
> <span data-ttu-id="4c911-598">Os tipos no `Microsoft.AspNetCore.Components.RenderTree` permitem o processamento dos *resultados* de operações de renderização.</span><span class="sxs-lookup"><span data-stu-id="4c911-598">The types in `Microsoft.AspNetCore.Components.RenderTree` allow processing of the *results* of rendering operations.</span></span> <span data-ttu-id="4c911-599">Estes são detalhes internos da implementação do Blazor Framework.</span><span class="sxs-lookup"><span data-stu-id="4c911-599">These are internal details of the Blazor framework implementation.</span></span> <span data-ttu-id="4c911-600">Esses tipos devem ser considerados *instáveis* e sujeitos a alterações em versões futuras.</span><span class="sxs-lookup"><span data-stu-id="4c911-600">These types should be considered *unstable* and subject to change in future releases.</span></span>

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="4c911-601">Números de sequência se relacionam a números de linha de código e não a ordem de execução</span><span class="sxs-lookup"><span data-stu-id="4c911-601">Sequence numbers relate to code line numbers and not execution order</span></span>

Blazor<span data-ttu-id="4c911-602"> `.razor` arquivos são sempre compilados.</span><span class="sxs-lookup"><span data-stu-id="4c911-602"> `.razor` files are always compiled.</span></span> <span data-ttu-id="4c911-603">Isso é potencialmente uma grande vantagem para `.razor` porque a etapa de compilação pode ser usada para injetar informações que melhoram o desempenho do aplicativo em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="4c911-603">This is potentially a great advantage for `.razor` because the compile step can be used to inject information that improve app performance at runtime.</span></span>

<span data-ttu-id="4c911-604">Um exemplo importante desses aprimoramentos envolve *números de sequência*.</span><span class="sxs-lookup"><span data-stu-id="4c911-604">A key example of these improvements involve *sequence numbers*.</span></span> <span data-ttu-id="4c911-605">Os números de sequência indicam ao tempo de execução que as saídas vieram de quais linhas de código distintas e ordenadas.</span><span class="sxs-lookup"><span data-stu-id="4c911-605">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="4c911-606">O tempo de execução usa essas informações para gerar comparações de árvore eficientes em tempo linear, o que é muito mais rápido do que normalmente é possível para um algoritmo de comparação de árvore geral.</span><span class="sxs-lookup"><span data-stu-id="4c911-606">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="4c911-607">Considere o seguinte arquivo de componente Razor ( *. Razor*):</span><span class="sxs-lookup"><span data-stu-id="4c911-607">Consider the following Razor component (*.razor*) file:</span></span>

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="4c911-608">O código anterior é compilado para algo semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="4c911-608">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="4c911-609">Quando o código é executado pela primeira vez, se `someFlag` for `true`, o Construtor receberá:</span><span class="sxs-lookup"><span data-stu-id="4c911-609">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="4c911-610">Sequence</span><span class="sxs-lookup"><span data-stu-id="4c911-610">Sequence</span></span> | <span data-ttu-id="4c911-611">{1&gt;Tipo&lt;1}</span><span class="sxs-lookup"><span data-stu-id="4c911-611">Type</span></span>      | <span data-ttu-id="4c911-612">Dados</span><span class="sxs-lookup"><span data-stu-id="4c911-612">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="4c911-613">0</span><span class="sxs-lookup"><span data-stu-id="4c911-613">0</span></span>        | <span data-ttu-id="4c911-614">Nó de texto</span><span class="sxs-lookup"><span data-stu-id="4c911-614">Text node</span></span> | <span data-ttu-id="4c911-615">First</span><span class="sxs-lookup"><span data-stu-id="4c911-615">First</span></span>  |
| <span data-ttu-id="4c911-616">1</span><span class="sxs-lookup"><span data-stu-id="4c911-616">1</span></span>        | <span data-ttu-id="4c911-617">Nó de texto</span><span class="sxs-lookup"><span data-stu-id="4c911-617">Text node</span></span> | <span data-ttu-id="4c911-618">Segundo</span><span class="sxs-lookup"><span data-stu-id="4c911-618">Second</span></span> |

<span data-ttu-id="4c911-619">Imagine que `someFlag` se torna `false`e a marcação é renderizada novamente.</span><span class="sxs-lookup"><span data-stu-id="4c911-619">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="4c911-620">Desta vez, o Construtor recebe:</span><span class="sxs-lookup"><span data-stu-id="4c911-620">This time, the builder receives:</span></span>

| <span data-ttu-id="4c911-621">Sequence</span><span class="sxs-lookup"><span data-stu-id="4c911-621">Sequence</span></span> | <span data-ttu-id="4c911-622">{1&gt;Tipo&lt;1}</span><span class="sxs-lookup"><span data-stu-id="4c911-622">Type</span></span>       | <span data-ttu-id="4c911-623">Dados</span><span class="sxs-lookup"><span data-stu-id="4c911-623">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="4c911-624">1</span><span class="sxs-lookup"><span data-stu-id="4c911-624">1</span></span>        | <span data-ttu-id="4c911-625">Nó de texto</span><span class="sxs-lookup"><span data-stu-id="4c911-625">Text node</span></span>  | <span data-ttu-id="4c911-626">Segundo</span><span class="sxs-lookup"><span data-stu-id="4c911-626">Second</span></span> |

<span data-ttu-id="4c911-627">Quando o tempo de execução executa uma comparação, ele vê que o item na sequência `0` foi removido e, portanto, gera o seguinte *script de edição*trivial:</span><span class="sxs-lookup"><span data-stu-id="4c911-627">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script*:</span></span>

* <span data-ttu-id="4c911-628">Remova o primeiro nó de texto.</span><span class="sxs-lookup"><span data-stu-id="4c911-628">Remove the first text node.</span></span>

#### <a name="what-goes-wrong-if-you-generate-sequence-numbers-programmatically"></a><span data-ttu-id="4c911-629">O que vai errado se você gerar números de sequência programaticamente</span><span class="sxs-lookup"><span data-stu-id="4c911-629">What goes wrong if you generate sequence numbers programmatically</span></span>

<span data-ttu-id="4c911-630">Imagine, em vez disso, que você escreveu a seguinte lógica do construtor de árvore de renderização:</span><span class="sxs-lookup"><span data-stu-id="4c911-630">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="4c911-631">Agora, a primeira saída é:</span><span class="sxs-lookup"><span data-stu-id="4c911-631">Now, the first output is:</span></span>

| <span data-ttu-id="4c911-632">Sequence</span><span class="sxs-lookup"><span data-stu-id="4c911-632">Sequence</span></span> | <span data-ttu-id="4c911-633">{1&gt;Tipo&lt;1}</span><span class="sxs-lookup"><span data-stu-id="4c911-633">Type</span></span>      | <span data-ttu-id="4c911-634">Dados</span><span class="sxs-lookup"><span data-stu-id="4c911-634">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="4c911-635">0</span><span class="sxs-lookup"><span data-stu-id="4c911-635">0</span></span>        | <span data-ttu-id="4c911-636">Nó de texto</span><span class="sxs-lookup"><span data-stu-id="4c911-636">Text node</span></span> | <span data-ttu-id="4c911-637">First</span><span class="sxs-lookup"><span data-stu-id="4c911-637">First</span></span>  |
| <span data-ttu-id="4c911-638">1</span><span class="sxs-lookup"><span data-stu-id="4c911-638">1</span></span>        | <span data-ttu-id="4c911-639">Nó de texto</span><span class="sxs-lookup"><span data-stu-id="4c911-639">Text node</span></span> | <span data-ttu-id="4c911-640">Segundo</span><span class="sxs-lookup"><span data-stu-id="4c911-640">Second</span></span> |

<span data-ttu-id="4c911-641">Esse resultado é idêntico ao caso anterior, portanto, não existem problemas negativos.</span><span class="sxs-lookup"><span data-stu-id="4c911-641">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="4c911-642">`someFlag` é `false` no segundo processamento e a saída é:</span><span class="sxs-lookup"><span data-stu-id="4c911-642">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="4c911-643">Sequence</span><span class="sxs-lookup"><span data-stu-id="4c911-643">Sequence</span></span> | <span data-ttu-id="4c911-644">{1&gt;Tipo&lt;1}</span><span class="sxs-lookup"><span data-stu-id="4c911-644">Type</span></span>      | <span data-ttu-id="4c911-645">Dados</span><span class="sxs-lookup"><span data-stu-id="4c911-645">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="4c911-646">0</span><span class="sxs-lookup"><span data-stu-id="4c911-646">0</span></span>        | <span data-ttu-id="4c911-647">Nó de texto</span><span class="sxs-lookup"><span data-stu-id="4c911-647">Text node</span></span> | <span data-ttu-id="4c911-648">Segundo</span><span class="sxs-lookup"><span data-stu-id="4c911-648">Second</span></span> |

<span data-ttu-id="4c911-649">Desta vez, o algoritmo diff vê que *duas* alterações ocorreram e o algoritmo gera o seguinte script de edição:</span><span class="sxs-lookup"><span data-stu-id="4c911-649">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="4c911-650">Altere o valor do primeiro nó de texto para `Second`.</span><span class="sxs-lookup"><span data-stu-id="4c911-650">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="4c911-651">Remova o segundo nó de texto.</span><span class="sxs-lookup"><span data-stu-id="4c911-651">Remove the second text node.</span></span>

<span data-ttu-id="4c911-652">A geração dos números de sequência perdeu todas as informações úteis sobre onde os `if/else` branches e loops estavam presentes no código original.</span><span class="sxs-lookup"><span data-stu-id="4c911-652">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="4c911-653">Isso resulta em uma comparação **duas vezes mais longa** do que antes.</span><span class="sxs-lookup"><span data-stu-id="4c911-653">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="4c911-654">Esse é um exemplo trivial.</span><span class="sxs-lookup"><span data-stu-id="4c911-654">This is a trivial example.</span></span> <span data-ttu-id="4c911-655">Em casos mais realistas com estruturas complexas e profundamente aninhadas, e especialmente com loops, o custo de desempenho é mais grave.</span><span class="sxs-lookup"><span data-stu-id="4c911-655">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is more severe.</span></span> <span data-ttu-id="4c911-656">Em vez de identificar imediatamente quais blocos de loop ou ramificações foram inseridos ou removidos, o algoritmo diff precisa recorrer profundamente nas árvores de renderização e geralmente criar scripts de edição muito mais, pois ele é informado indiretamente sobre como as estruturas antigas e novas relacionar entre si.</span><span class="sxs-lookup"><span data-stu-id="4c911-656">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees and usually build far longer edit scripts because it is misinformed about how the old and new structures relate to each other.</span></span>

#### <a name="guidance-and-conclusions"></a><span data-ttu-id="4c911-657">Diretrizes e conclusões</span><span class="sxs-lookup"><span data-stu-id="4c911-657">Guidance and conclusions</span></span>

* <span data-ttu-id="4c911-658">O desempenho do aplicativo será afetado se os números de sequência forem gerados dinamicamente.</span><span class="sxs-lookup"><span data-stu-id="4c911-658">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="4c911-659">A estrutura não pode criar seus próprios números de sequência automaticamente em tempo de execução porque as informações necessárias não existem, a menos que sejam capturadas no momento da compilação.</span><span class="sxs-lookup"><span data-stu-id="4c911-659">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="4c911-660">Não grave blocos longos de lógica de `RenderTreeBuilder` implementada manualmente.</span><span class="sxs-lookup"><span data-stu-id="4c911-660">Don't write long blocks of manually-implemented `RenderTreeBuilder` logic.</span></span> <span data-ttu-id="4c911-661">Prefira `.razor` arquivos e permitir que o compilador lide com os números de sequência.</span><span class="sxs-lookup"><span data-stu-id="4c911-661">Prefer `.razor` files and allow the compiler to deal with the sequence numbers.</span></span> <span data-ttu-id="4c911-662">Se você não conseguir evitar a lógica de `RenderTreeBuilder` manual, divida blocos longos de código em partes menores encapsuladas em chamadas `OpenRegion`/`CloseRegion`.</span><span class="sxs-lookup"><span data-stu-id="4c911-662">If you're unable to avoid manual `RenderTreeBuilder` logic, split long blocks of code into smaller pieces wrapped in `OpenRegion`/`CloseRegion` calls.</span></span> <span data-ttu-id="4c911-663">Cada região tem seu próprio espaço separado de números de sequência, para que você possa reiniciar de zero (ou qualquer outro número arbitrário) dentro de cada região.</span><span class="sxs-lookup"><span data-stu-id="4c911-663">Each region has its own separate space of sequence numbers, so you can restart from zero (or any other arbitrary number) inside each region.</span></span>
* <span data-ttu-id="4c911-664">Se os números de sequência forem codificados, o algoritmo diff só exigirá que os números de sequência aumentem de valor.</span><span class="sxs-lookup"><span data-stu-id="4c911-664">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="4c911-665">O valor inicial e as lacunas são irrelevantes.</span><span class="sxs-lookup"><span data-stu-id="4c911-665">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="4c911-666">Uma opção legítima é usar o número de linha de código como o número de sequência, ou começar de zero e aumentar por um ou centenas (ou qualquer intervalo preferencial).</span><span class="sxs-lookup"><span data-stu-id="4c911-666">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* Blazor<span data-ttu-id="4c911-667"> usa números de sequência, enquanto outras estruturas de interface do usuário de diferenciação de árvore não as usam.</span><span class="sxs-lookup"><span data-stu-id="4c911-667"> uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="4c911-668">A comparação é muito mais rápida quando números de sequência são usados e Blazor tem a vantagem de uma etapa de compilação que lida com números de sequência automaticamente para desenvolvedores que criam arquivos *. Razor* .</span><span class="sxs-lookup"><span data-stu-id="4c911-668">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring *.razor* files.</span></span>

## <a name="localization"></a><span data-ttu-id="4c911-669">Localização</span><span class="sxs-lookup"><span data-stu-id="4c911-669">Localization</span></span>

<span data-ttu-id="4c911-670">os aplicativos do Blazor Server são localizados usando o [middleware de localização](xref:fundamentals/localization#localization-middleware).</span><span class="sxs-lookup"><span data-stu-id="4c911-670">Blazor Server apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="4c911-671">O middleware seleciona a cultura apropriada para os usuários que solicitam recursos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4c911-671">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="4c911-672">A cultura pode ser definida usando uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="4c911-672">The culture can be set using one of the following approaches:</span></span>

* [<span data-ttu-id="4c911-673">Cookies</span><span class="sxs-lookup"><span data-stu-id="4c911-673">Cookies</span></span>](#cookies)
* [<span data-ttu-id="4c911-674">Fornecer interface do usuário para escolher a cultura</span><span class="sxs-lookup"><span data-stu-id="4c911-674">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="4c911-675">Para obter mais informações e exemplos, consulte <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="4c911-675">For more information and examples, see <xref:fundamentals/localization>.</span></span>

### <a name="configure-the-linker-for-internationalization-opno-locblazor-webassembly"></a><span data-ttu-id="4c911-676">Configurar o vinculador para internacionalização (Blazor Webassembly)</span><span class="sxs-lookup"><span data-stu-id="4c911-676">Configure the linker for internationalization (Blazor WebAssembly)</span></span>

<span data-ttu-id="4c911-677">Por padrão, a configuração do vinculador Blazorpara aplicativos Webassembly Blazor retira informações de internacionalização, exceto as localidades explicitamente solicitadas.</span><span class="sxs-lookup"><span data-stu-id="4c911-677">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="4c911-678">Para obter mais informações e orientação sobre como controlar o comportamento do vinculador, consulte <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.</span><span class="sxs-lookup"><span data-stu-id="4c911-678">For more information and guidance on controlling the linker's behavior, see <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.</span></span>

### <a name="cookies"></a><span data-ttu-id="4c911-679">Cookies</span><span class="sxs-lookup"><span data-stu-id="4c911-679">Cookies</span></span>

<span data-ttu-id="4c911-680">Um cookie de cultura de localização pode persistir a cultura do usuário.</span><span class="sxs-lookup"><span data-stu-id="4c911-680">A localization culture cookie can persist the user's culture.</span></span> <span data-ttu-id="4c911-681">O cookie é criado pelo método `OnGet` da página host do aplicativo (*pages/host. cshtml. cs*).</span><span class="sxs-lookup"><span data-stu-id="4c911-681">The cookie is created by the `OnGet` method of the app's host page (*Pages/Host.cshtml.cs*).</span></span> <span data-ttu-id="4c911-682">O middleware de localização lê o cookie em solicitações subsequentes para definir a cultura do usuário.</span><span class="sxs-lookup"><span data-stu-id="4c911-682">The Localization Middleware reads the cookie on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="4c911-683">O uso de um cookie garante que a conexão WebSocket possa propagar corretamente a cultura.</span><span class="sxs-lookup"><span data-stu-id="4c911-683">Use of a cookie ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="4c911-684">Se os esquemas de localização forem baseados no caminho da URL ou na cadeia de caracteres de consulta, o esquema pode não ser capaz de trabalhar com WebSockets, portanto, falha ao persistir a cultura.</span><span class="sxs-lookup"><span data-stu-id="4c911-684">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="4c911-685">Portanto, o uso de um cookie de cultura de localização é a abordagem recomendada.</span><span class="sxs-lookup"><span data-stu-id="4c911-685">Therefore, use of a localization culture cookie is the recommended approach.</span></span>

<span data-ttu-id="4c911-686">Qualquer técnica pode ser usada para atribuir uma cultura se a cultura persistir em um cookie de localização.</span><span class="sxs-lookup"><span data-stu-id="4c911-686">Any technique can be used to assign a culture if the culture is persisted in a localization cookie.</span></span> <span data-ttu-id="4c911-687">Se o aplicativo já tiver um esquema de localização estabelecido para ASP.NET Core do lado do servidor, continue a usar a infraestrutura de localização existente do aplicativo e defina o cookie de cultura de localização no esquema do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4c911-687">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture cookie within the app's scheme.</span></span>

<span data-ttu-id="4c911-688">O exemplo a seguir mostra como definir a cultura atual em um cookie que pode ser lido pelo middleware de localização.</span><span class="sxs-lookup"><span data-stu-id="4c911-688">The following example shows how to set the current culture in a cookie that can be read by the Localization Middleware.</span></span> <span data-ttu-id="4c911-689">Crie um arquivo *pages/host. cshtml. cs* com o seguinte conteúdo no aplicativo Blazor Server:</span><span class="sxs-lookup"><span data-stu-id="4c911-689">Create a *Pages/Host.cshtml.cs* file with the following contents in the Blazor Server app:</span></span>

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

<span data-ttu-id="4c911-690">A localização é manipulada no aplicativo:</span><span class="sxs-lookup"><span data-stu-id="4c911-690">Localization is handled in the app:</span></span>

1. <span data-ttu-id="4c911-691">O navegador envia uma solicitação HTTP inicial para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="4c911-691">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="4c911-692">A cultura é atribuída pelo middleware de localização.</span><span class="sxs-lookup"><span data-stu-id="4c911-692">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="4c911-693">O método `OnGet` em *_Host. cshtml. cs* persiste a cultura em um cookie como parte da resposta.</span><span class="sxs-lookup"><span data-stu-id="4c911-693">The `OnGet` method in *_Host.cshtml.cs* persists the culture in a cookie as part of the response.</span></span>
1. <span data-ttu-id="4c911-694">O navegador abre uma conexão WebSocket para criar uma sessão interativa do Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="4c911-694">The browser opens a WebSocket connection to create an interactive Blazor Server session.</span></span>
1. <span data-ttu-id="4c911-695">O middleware de localização lê o cookie e atribui a cultura.</span><span class="sxs-lookup"><span data-stu-id="4c911-695">The Localization Middleware reads the cookie and assigns the culture.</span></span>
1. <span data-ttu-id="4c911-696">A sessão do Blazor Server começa com a cultura correta.</span><span class="sxs-lookup"><span data-stu-id="4c911-696">The Blazor Server session begins with the correct culture.</span></span>

### <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="4c911-697">Fornecer interface do usuário para escolher a cultura</span><span class="sxs-lookup"><span data-stu-id="4c911-697">Provide UI to choose the culture</span></span>

<span data-ttu-id="4c911-698">Para fornecer à interface do usuário a fim de permitir a seleção de uma cultura, é recomendável uma *abordagem baseada em redirecionamento* .</span><span class="sxs-lookup"><span data-stu-id="4c911-698">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="4c911-699">O processo é semelhante ao que acontece em um aplicativo Web quando um usuário tenta acessar um recurso seguro&mdash;o usuário é redirecionado para uma página de entrada e, em seguida, Redirecionado de volta para o recurso original.</span><span class="sxs-lookup"><span data-stu-id="4c911-699">The process is similar to what happens in a web app when a user attempts to access a secure resource&mdash;the user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="4c911-700">O aplicativo persiste a cultura selecionada do usuário por meio de um redirecionamento para um controlador.</span><span class="sxs-lookup"><span data-stu-id="4c911-700">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="4c911-701">O controlador define a cultura selecionada do usuário em um cookie e redireciona o usuário de volta para o URI original.</span><span class="sxs-lookup"><span data-stu-id="4c911-701">The controller sets the user's selected culture into a cookie and redirects the user back to the original URI.</span></span>

<span data-ttu-id="4c911-702">Estabeleça um ponto de extremidade HTTP no servidor para definir a cultura selecionada do usuário em um cookie e execute o redirecionamento de volta para o URI original:</span><span class="sxs-lookup"><span data-stu-id="4c911-702">Establish an HTTP endpoint on the server to set the user's selected culture in a cookie and perform the redirect back to the original URI:</span></span>

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
> <span data-ttu-id="4c911-703">Use o resultado da ação `LocalRedirect` para evitar ataques de redirecionamento abertos.</span><span class="sxs-lookup"><span data-stu-id="4c911-703">Use the `LocalRedirect` action result to prevent open redirect attacks.</span></span> <span data-ttu-id="4c911-704">Para obter mais informações, consulte <xref:security/preventing-open-redirects>.</span><span class="sxs-lookup"><span data-stu-id="4c911-704">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="4c911-705">O componente a seguir mostra um exemplo de como executar o redirecionamento inicial quando o usuário seleciona uma cultura:</span><span class="sxs-lookup"><span data-stu-id="4c911-705">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

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

### <a name="use-net-localization-scenarios-in-opno-locblazor-apps"></a><span data-ttu-id="4c911-706">Usar cenários de localização do .NET em aplicativos Blazor</span><span class="sxs-lookup"><span data-stu-id="4c911-706">Use .NET localization scenarios in Blazor apps</span></span>

<span data-ttu-id="4c911-707">Dentro de aplicativos Blazor, os seguintes cenários de localização e globalização do .NET estão disponíveis:</span><span class="sxs-lookup"><span data-stu-id="4c911-707">Inside Blazor apps, the following .NET localization and globalization scenarios are available:</span></span>

* <span data-ttu-id="4c911-708">. Sistema de recursos da rede</span><span class="sxs-lookup"><span data-stu-id="4c911-708">.NET's resources system</span></span>
* <span data-ttu-id="4c911-709">Formatação de número e data específicos da cultura</span><span class="sxs-lookup"><span data-stu-id="4c911-709">Culture-specific number and date formatting</span></span>

<span data-ttu-id="4c911-710">a funcionalidade de `@bind` do Blazorexecuta a globalização com base na cultura atual do usuário.</span><span class="sxs-lookup"><span data-stu-id="4c911-710">Blazor's `@bind` functionality performs globalization based on the user's current culture.</span></span> <span data-ttu-id="4c911-711">Para obter mais informações, consulte a seção [ligação de dados](#data-binding) .</span><span class="sxs-lookup"><span data-stu-id="4c911-711">For more information, see the [Data binding](#data-binding) section.</span></span>

<span data-ttu-id="4c911-712">No momento, há suporte para um conjunto limitado de cenários de localização de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="4c911-712">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="4c911-713">`IStringLocalizer<>` tem *suporte* em aplicativos Blazor.</span><span class="sxs-lookup"><span data-stu-id="4c911-713">`IStringLocalizer<>` *is supported* in Blazor apps.</span></span>
* <span data-ttu-id="4c911-714">`IHtmlLocalizer<>`, `IViewLocalizer<>`e localização de anotações de dados são ASP.NET Core cenários MVC e **não têm suporte** em aplicativos Blazor.</span><span class="sxs-lookup"><span data-stu-id="4c911-714">`IHtmlLocalizer<>`, `IViewLocalizer<>`, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in Blazor apps.</span></span>

<span data-ttu-id="4c911-715">Para obter mais informações, consulte <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="4c911-715">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="4c911-716">Imagens SVG (gráficos vetoriais escaláveis)</span><span class="sxs-lookup"><span data-stu-id="4c911-716">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="4c911-717">Como Blazor renderiza imagens HTML, com suporte para navegador, incluindo imagens SVG (gráficos de vetor escalonáveis) ( *. svg*), há suporte por meio da marca `<img>`:</span><span class="sxs-lookup"><span data-stu-id="4c911-717">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (*.svg*), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="4c911-718">Da mesma forma, as imagens SVG têm suporte nas regras de CSS de um arquivo de folha de estilos ( *. css*):</span><span class="sxs-lookup"><span data-stu-id="4c911-718">Similarly, SVG images are supported in the CSS rules of a stylesheet file (*.css*):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="4c911-719">No entanto, a marcação SVG embutida não tem suporte em todos os cenários.</span><span class="sxs-lookup"><span data-stu-id="4c911-719">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="4c911-720">Se você posicionar uma marca de `<svg>` diretamente em um arquivo de componente ( *. Razor*), a renderização de imagem básica terá suporte, mas muitos cenários avançados ainda não têm suporte.</span><span class="sxs-lookup"><span data-stu-id="4c911-720">If you place an `<svg>` tag directly into a component file (*.razor*), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="4c911-721">Por exemplo, `<use>` marcas não são respeitadas atualmente e `@bind` não podem ser usadas com algumas marcas SVG.</span><span class="sxs-lookup"><span data-stu-id="4c911-721">For example, `<use>` tags aren't currently respected, and `@bind` can't be used with some SVG tags.</span></span> <span data-ttu-id="4c911-722">Esperamos abordar essas limitações em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="4c911-722">We expect to address these limitations in a future release.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4c911-723">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="4c911-723">Additional resources</span></span>

* <span data-ttu-id="4c911-724"><xref:security/blazor/server> &ndash; inclui diretrizes sobre a criação de aplicativos do Blazor Server que devem combater o esgotamento de recursos.</span><span class="sxs-lookup"><span data-stu-id="4c911-724"><xref:security/blazor/server> &ndash; Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>
