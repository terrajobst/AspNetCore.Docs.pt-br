---
title: Criar e usar ASP.NET Core componentes do Razor
author: guardrex
description: Saiba como criar e usar componentes do Razor, incluindo como associar dados, manipular eventos e gerenciar ciclos de vida do componente.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/06/2019
uid: blazor/components
ms.openlocfilehash: bc9fa06e5acccb773717fe87bf4aabb971b8dee5
ms.sourcegitcommit: 092061c4f6ef46ed2165fa84de6273d3786fb97e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/13/2019
ms.locfileid: "70963776"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="65a8f-103">Criar e usar ASP.NET Core componentes do Razor</span><span class="sxs-lookup"><span data-stu-id="65a8f-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="65a8f-104">De [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="65a8f-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="65a8f-105">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="65a8f-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="65a8f-106">Os aplicativos mais elaborados são criados usando *componentes*.</span><span class="sxs-lookup"><span data-stu-id="65a8f-106">Blazor apps are built using *components*.</span></span> <span data-ttu-id="65a8f-107">Um componente é uma parte independente da interface do usuário (IU), como uma página, uma caixa de diálogo ou um formulário.</span><span class="sxs-lookup"><span data-stu-id="65a8f-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="65a8f-108">Um componente inclui marcação HTML e a lógica de processamento necessária para injetar dados ou responder a eventos da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="65a8f-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="65a8f-109">Os componentes são flexíveis e leves.</span><span class="sxs-lookup"><span data-stu-id="65a8f-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="65a8f-110">Eles podem ser aninhados, reutilizados e compartilhados entre projetos.</span><span class="sxs-lookup"><span data-stu-id="65a8f-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="65a8f-111">Classes de componente</span><span class="sxs-lookup"><span data-stu-id="65a8f-111">Component classes</span></span>

<span data-ttu-id="65a8f-112">Os componentes são implementados em arquivos de componente do [Razor](xref:mvc/views/razor) ( *. Razor*) C# usando uma combinação de marcação HTML e.</span><span class="sxs-lookup"><span data-stu-id="65a8f-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="65a8f-113">Um componente no mais alto é conhecido formalmente como um *componente do Razor*.</span><span class="sxs-lookup"><span data-stu-id="65a8f-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

<span data-ttu-id="65a8f-114">O nome de um componente deve começar com um caractere maiúsculo.</span><span class="sxs-lookup"><span data-stu-id="65a8f-114">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="65a8f-115">Por exemplo, *MyCoolComponent. Razor* é válido e *MyCoolComponent. Razor* é inválido.</span><span class="sxs-lookup"><span data-stu-id="65a8f-115">For example, *MyCoolComponent.razor* is valid, and *myCoolComponent.razor* is invalid.</span></span>

<span data-ttu-id="65a8f-116">Os componentes podem ser criados usando a extensão de arquivo *. cshtml* , desde que os arquivos sejam identificados como arquivos de componente do `_RazorComponentInclude` Razor usando a Propriedade MSBuild.</span><span class="sxs-lookup"><span data-stu-id="65a8f-116">Components can be authored using the *.cshtml* file extension as long as the files are identified as Razor component files using the `_RazorComponentInclude` MSBuild property.</span></span> <span data-ttu-id="65a8f-117">Por exemplo, um aplicativo que especifica que todos os arquivos *. cshtml* na pasta *páginas* devem ser tratados como arquivos de componentes do Razor:</span><span class="sxs-lookup"><span data-stu-id="65a8f-117">For example, an app that specifies that all *.cshtml* files under the *Pages* folder should be treated as Razor components files:</span></span>

```xml
<PropertyGroup>
  <_RazorComponentInclude>Pages\**\*.cshtml</_RazorComponentInclude>
</PropertyGroup>
```

<span data-ttu-id="65a8f-118">A interface do usuário para um componente é definida usando HTML.</span><span class="sxs-lookup"><span data-stu-id="65a8f-118">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="65a8f-119">A lógica de renderização dinâmica (por exemplo, loops, condicionais, expressões) é adicionada usando uma sintaxe de C# inserida chamada [Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="65a8f-119">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="65a8f-120">Quando um aplicativo é compilado, a marcação HTML e C# a lógica de renderização são convertidas em uma classe de componente.</span><span class="sxs-lookup"><span data-stu-id="65a8f-120">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="65a8f-121">O nome da classe gerada corresponde ao nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="65a8f-121">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="65a8f-122">Os membros da classe de componente são definidos em um bloco `@code`.</span><span class="sxs-lookup"><span data-stu-id="65a8f-122">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="65a8f-123">`@code` No bloco, estado do componente (Propriedades, campos) é especificado com métodos para manipulação de eventos ou para definir outra lógica de componente.</span><span class="sxs-lookup"><span data-stu-id="65a8f-123">In the `@code` block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="65a8f-124">Mais de um bloco de `@code` é permitido.</span><span class="sxs-lookup"><span data-stu-id="65a8f-124">More than one `@code` block is permissible.</span></span>

> [!NOTE]
> <span data-ttu-id="65a8f-125">Nas visualizações anteriores do ASP.NET Core 3,0, `@functions` os blocos foram usados para a mesma finalidade que `@code` os blocos nos componentes do Razor.</span><span class="sxs-lookup"><span data-stu-id="65a8f-125">In prior previews of ASP.NET Core 3.0, `@functions` blocks were used for the same purpose as `@code` blocks in Razor components.</span></span> <span data-ttu-id="65a8f-126">`@functions`os blocos continuam a funcionar em componentes do Razor, mas é recomendável usar o `@code` bloco no ASP.NET Core 3,0 Preview 6 ou posterior.</span><span class="sxs-lookup"><span data-stu-id="65a8f-126">`@functions` blocks continue to function in Razor components, but we recommend using the `@code` block in ASP.NET Core 3.0 Preview 6 or later.</span></span>

<span data-ttu-id="65a8f-127">Os membros do componente podem ser usados como parte da lógica de renderização do C# componente usando expressões que `@`começam com.</span><span class="sxs-lookup"><span data-stu-id="65a8f-127">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="65a8f-128">Por exemplo, um C# campo é renderizado pela prefixação `@` para o nome do campo.</span><span class="sxs-lookup"><span data-stu-id="65a8f-128">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="65a8f-129">O exemplo a seguir avalia e renderiza:</span><span class="sxs-lookup"><span data-stu-id="65a8f-129">The following example evaluates and renders:</span></span>

* <span data-ttu-id="65a8f-130">`_headingFontStyle`para o valor da propriedade de `font-style`CSS para.</span><span class="sxs-lookup"><span data-stu-id="65a8f-130">`_headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="65a8f-131">`_headingText`para o conteúdo do `<h1>` elemento.</span><span class="sxs-lookup"><span data-stu-id="65a8f-131">`_headingText` to the content of the `<h1>` element.</span></span>

```cshtml
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="65a8f-132">Depois que o componente é processado inicialmente, o componente regenera sua árvore de renderização em resposta a eventos.</span><span class="sxs-lookup"><span data-stu-id="65a8f-132">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> <span data-ttu-id="65a8f-133">Em seguida, ele compara a nova árvore de renderização com a anterior e aplica quaisquer modificações ao Modelo de Objeto do Documento do navegador (DOM).</span><span class="sxs-lookup"><span data-stu-id="65a8f-133">Blazor then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="65a8f-134">Os componentes são C# classes comuns e podem ser colocados em qualquer lugar dentro de um projeto.</span><span class="sxs-lookup"><span data-stu-id="65a8f-134">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="65a8f-135">Os componentes que produzem páginas da Web geralmente residem na pasta *páginas* .</span><span class="sxs-lookup"><span data-stu-id="65a8f-135">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="65a8f-136">Os componentes que não são de página são frequentemente colocados na pasta *compartilhada* ou em uma pasta personalizada adicionada ao projeto.</span><span class="sxs-lookup"><span data-stu-id="65a8f-136">Non-page components are frequently placed in the *Shared* folder or a custom folder added to the project.</span></span> <span data-ttu-id="65a8f-137">Para usar uma pasta personalizada, adicione o namespace da pasta personalizada ao componente pai ou ao arquivo *_Imports. Razor* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="65a8f-137">To use a custom folder, add the custom folder's namespace to either the parent component or to the app's *_Imports.razor* file.</span></span> <span data-ttu-id="65a8f-138">Por exemplo, o namespace a seguir torna os componentes em uma pasta *componentes* disponíveis quando o namespace raiz do `WebApplication`aplicativo é:</span><span class="sxs-lookup"><span data-stu-id="65a8f-138">For example, the following namespace makes components in a *Components* folder available when the app's root namespace is `WebApplication`:</span></span>

```cshtml
@using WebApplication.Components
```

## <a name="integrate-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="65a8f-139">Integrar componentes em aplicativos Razor Pages e MVC</span><span class="sxs-lookup"><span data-stu-id="65a8f-139">Integrate components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="65a8f-140">Use componentes com os aplicativos Razor Pages e MVC existentes.</span><span class="sxs-lookup"><span data-stu-id="65a8f-140">Use components with existing Razor Pages and MVC apps.</span></span> <span data-ttu-id="65a8f-141">Não é necessário reescrever páginas ou exibições existentes para usar os componentes do Razor.</span><span class="sxs-lookup"><span data-stu-id="65a8f-141">There's no need to rewrite existing pages or views to use Razor components.</span></span> <span data-ttu-id="65a8f-142">Quando a página ou a exibição é renderizada, os componentes são renderizados ao mesmo tempo.</span><span class="sxs-lookup"><span data-stu-id="65a8f-142">When the page or view is rendered, components are prerendered at the same time.</span></span>

<span data-ttu-id="65a8f-143">Para renderizar um componente de uma página ou exibição, use `RenderComponentAsync<TComponent>` o método auxiliar HTML:</span><span class="sxs-lookup"><span data-stu-id="65a8f-143">To render a component from a page or view, use the `RenderComponentAsync<TComponent>` HTML helper method:</span></span>

```cshtml
<div id="MyComponent">
    @(await Html.RenderComponentAsync<MyComponent>(RenderMode.ServerPrerendered))
</div>
```

<span data-ttu-id="65a8f-144">Embora as páginas e exibições possam usar componentes, o inverso não é verdadeiro.</span><span class="sxs-lookup"><span data-stu-id="65a8f-144">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="65a8f-145">Os componentes não podem usar cenários específicos de exibição e de página, como exibições parciais e seções.</span><span class="sxs-lookup"><span data-stu-id="65a8f-145">Components can't use view- and page-specific scenarios, such as partial views and sections.</span></span> <span data-ttu-id="65a8f-146">Para usar a lógica da exibição parcial em um componente, desfatore a lógica de exibição parcial em um componente.</span><span class="sxs-lookup"><span data-stu-id="65a8f-146">To use logic from partial view in a component, factor out the partial view logic into a component.</span></span>

<span data-ttu-id="65a8f-147">Para obter mais informações sobre como os componentes são renderizados e o estado do componente é gerenciado em aplicativos de <xref:blazor/hosting-models> servidor mais incrivelmente, consulte o artigo.</span><span class="sxs-lookup"><span data-stu-id="65a8f-147">For more information on how components are rendered and component state is managed in Blazor Server apps, see the <xref:blazor/hosting-models> article.</span></span>

## <a name="use-components"></a><span data-ttu-id="65a8f-148">Usar componentes</span><span class="sxs-lookup"><span data-stu-id="65a8f-148">Use components</span></span>

<span data-ttu-id="65a8f-149">Os componentes podem incluir outros componentes, declarando-os usando a sintaxe do elemento HTML.</span><span class="sxs-lookup"><span data-stu-id="65a8f-149">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="65a8f-150">A marcação para uso de um componente é semelhante a uma marca HTML, em que o nome da marca é o tipo de componente.</span><span class="sxs-lookup"><span data-stu-id="65a8f-150">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="65a8f-151">A associação de atributo diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="65a8f-151">Attribute binding is case sensitive.</span></span> <span data-ttu-id="65a8f-152">Por exemplo, `@bind` é válido e `@Bind` é inválido.</span><span class="sxs-lookup"><span data-stu-id="65a8f-152">For example, `@bind` is valid, and `@Bind` is invalid.</span></span>

<span data-ttu-id="65a8f-153">A marcação a seguir no *index. Razor* renderiza uma `HeadingComponent` instância:</span><span class="sxs-lookup"><span data-stu-id="65a8f-153">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/Index.razor?name=snippet_HeadingComponent)]

<span data-ttu-id="65a8f-154">*Componentes/HeadingComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="65a8f-154">*Components/HeadingComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/HeadingComponent.razor)]

<span data-ttu-id="65a8f-155">Se um componente contiver um elemento HTML com uma letra maiúscula ou minúscula que não corresponda a um nome de componente, um aviso será emitido indicando que o elemento tem um nome inesperado.</span><span class="sxs-lookup"><span data-stu-id="65a8f-155">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="65a8f-156">A adição `@using` de uma instrução para o namespace do componente torna o componente disponível, o que remove o aviso.</span><span class="sxs-lookup"><span data-stu-id="65a8f-156">Adding an `@using` statement for the component's namespace makes the component available, which removes the warning.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="65a8f-157">Parâmetros do componente</span><span class="sxs-lookup"><span data-stu-id="65a8f-157">Component parameters</span></span>

<span data-ttu-id="65a8f-158">Os componentes podem ter *parâmetros de componente*, que são definidos usando propriedades públicas na classe de componente `[Parameter]` com o atributo.</span><span class="sxs-lookup"><span data-stu-id="65a8f-158">Components can have *component parameters*, which are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="65a8f-159">Use atributos para especificar argumentos para um componente na marcação.</span><span class="sxs-lookup"><span data-stu-id="65a8f-159">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="65a8f-160">*Componentes/ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="65a8f-160">*Components/ChildComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=11-12)]

<span data-ttu-id="65a8f-161">No exemplo a seguir, `ParentComponent` define o valor `Title` da Propriedade do `ChildComponent`.</span><span class="sxs-lookup"><span data-stu-id="65a8f-161">In the following example, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="65a8f-162">*Páginas/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="65a8f-162">*Pages/ParentComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=5-6)]

## <a name="child-content"></a><span data-ttu-id="65a8f-163">Conteúdo filho</span><span class="sxs-lookup"><span data-stu-id="65a8f-163">Child content</span></span>

<span data-ttu-id="65a8f-164">Os componentes podem definir o conteúdo de outro componente.</span><span class="sxs-lookup"><span data-stu-id="65a8f-164">Components can set the content of another component.</span></span> <span data-ttu-id="65a8f-165">O componente de atribuição fornece o conteúdo entre as marcas que especificam o componente de recebimento.</span><span class="sxs-lookup"><span data-stu-id="65a8f-165">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="65a8f-166">No exemplo a seguir, o `ChildComponent` tem uma `ChildContent` propriedade que representa um `RenderFragment`, que representa um segmento de interface do usuário a ser renderizado.</span><span class="sxs-lookup"><span data-stu-id="65a8f-166">In the following example, the `ChildComponent` has a `ChildContent` property that represents a `RenderFragment`, which represents a segment of UI to render.</span></span> <span data-ttu-id="65a8f-167">O valor de `ChildContent` é posicionado na marcação do componente onde o conteúdo deve ser renderizado.</span><span class="sxs-lookup"><span data-stu-id="65a8f-167">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="65a8f-168">O valor de `ChildContent` é recebido do componente pai e renderizado dentro do `panel-body`painel de inicialização.</span><span class="sxs-lookup"><span data-stu-id="65a8f-168">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="65a8f-169">*Componentes/ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="65a8f-169">*Components/ChildComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="65a8f-170">A propriedade que recebe `RenderFragment` o conteúdo deve ser `ChildContent` nomeada por convenção.</span><span class="sxs-lookup"><span data-stu-id="65a8f-170">The property receiving the `RenderFragment` content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="65a8f-171">O seguinte `ParentComponent` pode fornecer conteúdo para renderizar `ChildComponent` o colocando o conteúdo dentro das `<ChildComponent>` marcas.</span><span class="sxs-lookup"><span data-stu-id="65a8f-171">The following `ParentComponent` can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="65a8f-172">*Páginas/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="65a8f-172">*Pages/ParentComponent.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="65a8f-173">Atributo nivelamento e parâmetros arbitrários</span><span class="sxs-lookup"><span data-stu-id="65a8f-173">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="65a8f-174">Os componentes podem capturar e renderizar atributos adicionais além dos parâmetros declarados do componente.</span><span class="sxs-lookup"><span data-stu-id="65a8f-174">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="65a8f-175">Atributos adicionais podem ser capturados em um dicionário e, em seguida, *splatted* em um elemento quando o componente [@attributes](xref:mvc/views/razor#attributes) é renderizado usando a diretiva Razor.</span><span class="sxs-lookup"><span data-stu-id="65a8f-175">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [@attributes](xref:mvc/views/razor#attributes) Razor directive.</span></span> <span data-ttu-id="65a8f-176">Esse cenário é útil ao definir um componente que produz um elemento de marcação que dá suporte a uma variedade de personalizações.</span><span class="sxs-lookup"><span data-stu-id="65a8f-176">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="65a8f-177">Por exemplo, pode ser entediante definir atributos separadamente para um `<input>` que dê suporte a muitos parâmetros.</span><span class="sxs-lookup"><span data-stu-id="65a8f-177">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="65a8f-178">No exemplo a seguir, o primeiro `<input>` elemento (`id="useIndividualParams"`) usa parâmetros de componente individuais, enquanto o `<input>` segundo elemento`id="useAttributesDict"`() usa o atributo nivelamento:</span><span class="sxs-lookup"><span data-stu-id="65a8f-178">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

```cshtml
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

<span data-ttu-id="65a8f-179">O tipo do parâmetro deve implementar `IEnumerable<KeyValuePair<string, object>>` com chaves de cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="65a8f-179">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="65a8f-180">Usar `IReadOnlyDictionary<string, object>` também é uma opção nesse cenário.</span><span class="sxs-lookup"><span data-stu-id="65a8f-180">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="65a8f-181">Os elementos `<input>` renderizados usando ambas as abordagens são idênticos:</span><span class="sxs-lookup"><span data-stu-id="65a8f-181">The rendered `<input>` elements using both approaches is identical:</span></span>

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

<span data-ttu-id="65a8f-182">Para aceitar atributos arbitrários, defina um parâmetro de componente `[Parameter]` usando o atributo `CaptureUnmatchedValues` com a propriedade `true`definida como:</span><span class="sxs-lookup"><span data-stu-id="65a8f-182">To accept arbitrary attributes, define a component parameter using the `[Parameter]` attribute with the `CaptureUnmatchedValues` property set to `true`:</span></span>

```cshtml
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="65a8f-183">A `CaptureUnmatchedValues` Propriedade on `[Parameter]` permite que o parâmetro corresponda a todos os atributos que não correspondem a nenhum outro parâmetro.</span><span class="sxs-lookup"><span data-stu-id="65a8f-183">The `CaptureUnmatchedValues` property on `[Parameter]` allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="65a8f-184">Um componente só pode definir um único parâmetro com `CaptureUnmatchedValues`.</span><span class="sxs-lookup"><span data-stu-id="65a8f-184">A component can only define a single parameter with `CaptureUnmatchedValues`.</span></span> <span data-ttu-id="65a8f-185">O tipo de propriedade usado `CaptureUnmatchedValues` com deve ser atribuível de `Dictionary<string, object>` com chaves de cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="65a8f-185">The property type used with `CaptureUnmatchedValues` must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="65a8f-186">`IEnumerable<KeyValuePair<string, object>>`ou `IReadOnlyDictionary<string, object>` também são opções neste cenário.</span><span class="sxs-lookup"><span data-stu-id="65a8f-186">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

## <a name="data-binding"></a><span data-ttu-id="65a8f-187">Associação de dados</span><span class="sxs-lookup"><span data-stu-id="65a8f-187">Data binding</span></span>

<span data-ttu-id="65a8f-188">A ligação de dados com os componentes e os elementos DOM é [@bind](xref:mvc/views/razor#bind) realizada com o atributo.</span><span class="sxs-lookup"><span data-stu-id="65a8f-188">Data binding to both components and DOM elements is accomplished with the [@bind](xref:mvc/views/razor#bind) attribute.</span></span> <span data-ttu-id="65a8f-189">O exemplo a seguir associa o `_italicsCheck` campo ao estado marcado da caixa de seleção:</span><span class="sxs-lookup"><span data-stu-id="65a8f-189">The following example binds the `_italicsCheck` field to the check box's checked state:</span></span>

```cshtml
<input type="checkbox" class="form-check-input" id="italicsCheck" 
    @bind="_italicsCheck" />
```

<span data-ttu-id="65a8f-190">Quando a caixa de seleção é marcada e desmarcada, o valor da propriedade `true` é `false`atualizado para e, respectivamente.</span><span class="sxs-lookup"><span data-stu-id="65a8f-190">When the check box is selected and cleared, the property's value is updated to `true` and `false`, respectively.</span></span>

<span data-ttu-id="65a8f-191">A caixa de seleção é atualizada na interface do usuário somente quando o componente é renderizado, não em resposta à alteração do valor da propriedade.</span><span class="sxs-lookup"><span data-stu-id="65a8f-191">The check box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="65a8f-192">Como os componentes são renderizados após a execução do código do manipulador de eventos, as atualizações de propriedade geralmente são refletidas na interface do usuário imediatamente.</span><span class="sxs-lookup"><span data-stu-id="65a8f-192">Since components render themselves after event handler code executes, property updates are usually reflected in the UI immediately.</span></span>

<span data-ttu-id="65a8f-193">Usar `@bind` with a `CurrentValue` Property (`<input @bind="CurrentValue" />`) é essencialmente equivalente ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="65a8f-193">Using `@bind` with a `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```cshtml
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = __e.Value)" />
```

<span data-ttu-id="65a8f-194">Quando o componente é renderizado, `value` o do elemento de entrada vem `CurrentValue` da propriedade.</span><span class="sxs-lookup"><span data-stu-id="65a8f-194">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="65a8f-195">Quando o usuário digita na caixa de texto, o `onchange` evento é acionado e `CurrentValue` a propriedade é definida como o valor alterado.</span><span class="sxs-lookup"><span data-stu-id="65a8f-195">When the user types in the text box, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="65a8f-196">Na realidade, a geração de código é um pouco mais complexa `@bind` porque lida com alguns casos em que as conversões de tipo são executadas.</span><span class="sxs-lookup"><span data-stu-id="65a8f-196">In reality, the code generation is a little more complex because `@bind` handles a few cases where type conversions are performed.</span></span> <span data-ttu-id="65a8f-197">Em princípio, `@bind` associa o valor atual de uma expressão a um `value` atributo e manipula as alterações usando o manipulador registrado.</span><span class="sxs-lookup"><span data-stu-id="65a8f-197">In principle, `@bind` associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="65a8f-198">Além de `onchange` manipular eventos com [@bind-value](xref:mvc/views/razor#bind) `@bind` sintaxe, uma propriedade ou campo pode ser associado usando outros eventos, especificando um atributo com um `event` parâmetro ([@bind-value:event](xref:mvc/views/razor#bind)).</span><span class="sxs-lookup"><span data-stu-id="65a8f-198">In addition to handling `onchange` events with `@bind` syntax, a property or field can be bound using other events by specifying an [@bind-value](xref:mvc/views/razor#bind) attribute with an `event` parameter ([@bind-value:event](xref:mvc/views/razor#bind)).</span></span> <span data-ttu-id="65a8f-199">O exemplo a seguir associa a `CurrentValue` propriedade para o `oninput` evento:</span><span class="sxs-lookup"><span data-stu-id="65a8f-199">The following example binds the `CurrentValue` property for the `oninput` event:</span></span>

```cshtml
<input @bind-value="CurrentValue" @bind-value:event="oninput" />
```

<span data-ttu-id="65a8f-200">Ao `onchange`contrário de, que é acionado quando o `oninput` elemento perde o foco, é acionado quando o valor da caixa de texto é alterado.</span><span class="sxs-lookup"><span data-stu-id="65a8f-200">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="65a8f-201">**Globalização**</span><span class="sxs-lookup"><span data-stu-id="65a8f-201">**Globalization**</span></span>

<span data-ttu-id="65a8f-202">`@bind`os valores são formatados para exibição e analisados usando as regras da cultura atual.</span><span class="sxs-lookup"><span data-stu-id="65a8f-202">`@bind` values are formatted for display and parsed using the current culture's rules.</span></span>

<span data-ttu-id="65a8f-203">A cultura atual pode ser acessada <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> a partir da propriedade.</span><span class="sxs-lookup"><span data-stu-id="65a8f-203">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="65a8f-204">[CultureInfo. InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) é usado para os seguintes tipos de campo`<input type="{TYPE}" />`():</span><span class="sxs-lookup"><span data-stu-id="65a8f-204">[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="65a8f-205">Os tipos de campo anteriores:</span><span class="sxs-lookup"><span data-stu-id="65a8f-205">The preceding field types:</span></span>

* <span data-ttu-id="65a8f-206">São exibidos usando suas regras de formatação baseadas em navegador apropriadas.</span><span class="sxs-lookup"><span data-stu-id="65a8f-206">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="65a8f-207">Não pode conter texto de forma livre.</span><span class="sxs-lookup"><span data-stu-id="65a8f-207">Can't contain free-form text.</span></span>
* <span data-ttu-id="65a8f-208">Fornecer características de interação do usuário com base na implementação do navegador.</span><span class="sxs-lookup"><span data-stu-id="65a8f-208">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="65a8f-209">Os seguintes tipos de campo têm requisitos de formatação específicos e atualmente não são compatíveis com o mais grande, pois não têm suporte de todos os principais navegadores:</span><span class="sxs-lookup"><span data-stu-id="65a8f-209">The following field types have specific formatting requirements and aren't currently supported by Blazor because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="65a8f-210">`@bind`dá suporte ao <xref:System.Globalization.CultureInfo?displayProperty=fullName> `@bind:culture` parâmetro para fornecer um para análise e formatação de um valor.</span><span class="sxs-lookup"><span data-stu-id="65a8f-210">`@bind` supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="65a8f-211">Não é recomendável especificar uma cultura ao `date` usar `number` os tipos de campo e.</span><span class="sxs-lookup"><span data-stu-id="65a8f-211">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="65a8f-212">`date`e `number` têm suporte interno de mais alto que fornece a cultura necessária.</span><span class="sxs-lookup"><span data-stu-id="65a8f-212">`date` and `number` have built-in Blazor support that provides the required culture.</span></span>

<span data-ttu-id="65a8f-213">Para obter informações sobre como definir a cultura do usuário, consulte a seção [localização](#localization) .</span><span class="sxs-lookup"><span data-stu-id="65a8f-213">For information on how to set the user's culture, see the [Localization](#localization) section.</span></span>

<span data-ttu-id="65a8f-214">**Formatar cadeias de caracteres**</span><span class="sxs-lookup"><span data-stu-id="65a8f-214">**Format strings**</span></span>

<span data-ttu-id="65a8f-215">A vinculação de dados <xref:System.DateTime> funciona com cadeias de caracteres de formato usando. [@bind:format](xref:mvc/views/razor#bind)</span><span class="sxs-lookup"><span data-stu-id="65a8f-215">Data binding works with <xref:System.DateTime> format strings using [@bind:format](xref:mvc/views/razor#bind).</span></span> <span data-ttu-id="65a8f-216">Outras expressões de formato, como formatos de moeda ou número, não estão disponíveis no momento.</span><span class="sxs-lookup"><span data-stu-id="65a8f-216">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```cshtml
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="65a8f-217">No código anterior, o `<input>` tipo de campo do elemento (`type`) usa como `text`padrão.</span><span class="sxs-lookup"><span data-stu-id="65a8f-217">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="65a8f-218">`@bind:format`tem suporte para ligar os seguintes tipos .NET:</span><span class="sxs-lookup"><span data-stu-id="65a8f-218">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="65a8f-219"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="65a8f-219"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="65a8f-220"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="65a8f-220"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="65a8f-221">O `@bind:format` atributo especifica o formato de data a ser aplicado `value` ao do `<input>` elemento.</span><span class="sxs-lookup"><span data-stu-id="65a8f-221">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="65a8f-222">O formato também é usado para analisar o valor quando ocorre `onchange` um evento.</span><span class="sxs-lookup"><span data-stu-id="65a8f-222">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="65a8f-223">Não é recomendável especificar `date` um formato para o tipo de campo porque o mais alto tem suporte interno para formatar datas.</span><span class="sxs-lookup"><span data-stu-id="65a8f-223">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span>

<span data-ttu-id="65a8f-224">**Parâmetros do componente**</span><span class="sxs-lookup"><span data-stu-id="65a8f-224">**Component parameters**</span></span>

<span data-ttu-id="65a8f-225">A associação reconhece os parâmetros do `@bind-{property}` componente, onde pode associar um valor de propriedade entre componentes.</span><span class="sxs-lookup"><span data-stu-id="65a8f-225">Binding recognizes component parameters, where `@bind-{property}` can bind a property value across components.</span></span>

<span data-ttu-id="65a8f-226">O componente filho a seguir`ChildComponent`() tem `Year` um parâmetro de `YearChanged` componente e um retorno de chamada:</span><span class="sxs-lookup"><span data-stu-id="65a8f-226">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

```cshtml
<h2>Child Component</h2>

<p>Year: @Year</p>

@code {
    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }
}
```

<span data-ttu-id="65a8f-227">`EventCallback<T>`é explicado na seção [EventCallback](#eventcallback) .</span><span class="sxs-lookup"><span data-stu-id="65a8f-227">`EventCallback<T>` is explained in the [EventCallback](#eventcallback) section.</span></span>

<span data-ttu-id="65a8f-228">O componente pai a seguir `ChildComponent` usa e associa o `ParentYear` parâmetro `Year` do pai ao parâmetro no componente filho:</span><span class="sxs-lookup"><span data-stu-id="65a8f-228">The following parent component uses `ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component:</span></span>

```cshtml
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

<span data-ttu-id="65a8f-229">O carregamento `ParentComponent` do produz a seguinte marcação:</span><span class="sxs-lookup"><span data-stu-id="65a8f-229">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="65a8f-230">`ParentYear` Se o valor da propriedade for alterado selecionando o botão `ParentComponent`no `ChildComponent` , a `Year` Propriedade do será atualizada.</span><span class="sxs-lookup"><span data-stu-id="65a8f-230">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="65a8f-231">O novo valor de `Year` é renderizado na interface do usuário `ParentComponent` quando o é rerenderizado:</span><span class="sxs-lookup"><span data-stu-id="65a8f-231">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="65a8f-232">O `Year` parâmetro é ligável porque tem um evento `YearChanged` complementar que `Year` corresponde ao tipo do parâmetro.</span><span class="sxs-lookup"><span data-stu-id="65a8f-232">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="65a8f-233">Por convenção, `<ChildComponent @bind-Year="ParentYear" />` é essencialmente equivalente a escrever:</span><span class="sxs-lookup"><span data-stu-id="65a8f-233">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

```cshtml
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="65a8f-234">Em geral, uma propriedade pode ser associada a um manipulador de eventos correspondente `@bind-property:event` usando o atributo.</span><span class="sxs-lookup"><span data-stu-id="65a8f-234">In general, a property can be bound to a corresponding event handler using `@bind-property:event` attribute.</span></span> <span data-ttu-id="65a8f-235">Por exemplo, a propriedade `MyProp` pode ser associada ao `MyEventHandler` uso dos dois atributos a seguir:</span><span class="sxs-lookup"><span data-stu-id="65a8f-235">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```cshtml
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="event-handling"></a><span data-ttu-id="65a8f-236">Manipulação de eventos</span><span class="sxs-lookup"><span data-stu-id="65a8f-236">Event handling</span></span>

<span data-ttu-id="65a8f-237">Os componentes do Razor fornecem recursos de manipulação de eventos.</span><span class="sxs-lookup"><span data-stu-id="65a8f-237">Razor components provide event handling features.</span></span> <span data-ttu-id="65a8f-238">Para um atributo de elemento HTML `on{event}` chamado (por exemplo `onclick` , `onsubmit`e) com um valor de tipo delegado, os componentes do Razor tratam o valor do atributo como um manipulador de eventos.</span><span class="sxs-lookup"><span data-stu-id="65a8f-238">For an HTML element attribute named `on{event}` (for example, `onclick` and `onsubmit`) with a delegate-typed value, Razor components treats the attribute's value as an event handler.</span></span> <span data-ttu-id="65a8f-239">O nome do atributo é sempre formatado [ @on{Event}](xref:mvc/views/razor#onevent).</span><span class="sxs-lookup"><span data-stu-id="65a8f-239">The attribute's name is always formatted [@on{event}](xref:mvc/views/razor#onevent).</span></span>

<span data-ttu-id="65a8f-240">O código a seguir chama `UpdateHeading` o método quando o botão é selecionado na interface do usuário:</span><span class="sxs-lookup"><span data-stu-id="65a8f-240">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

```cshtml
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

<span data-ttu-id="65a8f-241">O código a seguir chama `CheckChanged` o método quando a caixa de seleção é alterada na interface do usuário:</span><span class="sxs-lookup"><span data-stu-id="65a8f-241">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```cshtml
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="65a8f-242">Os manipuladores de eventos também podem ser assíncronos <xref:System.Threading.Tasks.Task>e retornar um.</span><span class="sxs-lookup"><span data-stu-id="65a8f-242">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="65a8f-243">Não há necessidade de chamar `StateHasChanged()`manualmente.</span><span class="sxs-lookup"><span data-stu-id="65a8f-243">There's no need to manually call `StateHasChanged()`.</span></span> <span data-ttu-id="65a8f-244">As exceções são registradas quando ocorrem.</span><span class="sxs-lookup"><span data-stu-id="65a8f-244">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="65a8f-245">No exemplo a seguir, `UpdateHeading` é chamado de forma assíncrona quando o botão é selecionado:</span><span class="sxs-lookup"><span data-stu-id="65a8f-245">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

```cshtml
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

### <a name="event-argument-types"></a><span data-ttu-id="65a8f-246">Tipos de argumento de evento</span><span class="sxs-lookup"><span data-stu-id="65a8f-246">Event argument types</span></span>

<span data-ttu-id="65a8f-247">Para alguns eventos, são permitidos tipos de argumento de evento.</span><span class="sxs-lookup"><span data-stu-id="65a8f-247">For some events, event argument types are permitted.</span></span> <span data-ttu-id="65a8f-248">Se o acesso a um desses tipos de evento não for necessário, ele não será necessário na chamada do método.</span><span class="sxs-lookup"><span data-stu-id="65a8f-248">If access to one of these event types isn't necessary, it isn't required in the method call.</span></span>

<span data-ttu-id="65a8f-249">O [EventArgs](https://github.com/aspnet/AspNetCore/tree/release/3.0-preview9/src/Components/Web/src/Web) com suporte é mostrado na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="65a8f-249">Supported [EventArgs](https://github.com/aspnet/AspNetCore/tree/release/3.0-preview9/src/Components/Web/src/Web) are shown in the following table.</span></span>

| <span data-ttu-id="65a8f-250">evento</span><span class="sxs-lookup"><span data-stu-id="65a8f-250">Event</span></span> | <span data-ttu-id="65a8f-251">Classe</span><span class="sxs-lookup"><span data-stu-id="65a8f-251">Class</span></span> |
| ----- | ----- |
| <span data-ttu-id="65a8f-252">Área de Transferência</span><span class="sxs-lookup"><span data-stu-id="65a8f-252">Clipboard</span></span>        | `ClipboardEventArgs` |
| <span data-ttu-id="65a8f-253">Arraste</span><span class="sxs-lookup"><span data-stu-id="65a8f-253">Drag</span></span>             | <span data-ttu-id="65a8f-254">`DragEventArgs`&ndash; emantenhaarrastado`DataTransferItem` os dados do item. `DataTransfer`</span><span class="sxs-lookup"><span data-stu-id="65a8f-254">`DragEventArgs` &ndash; `DataTransfer` and `DataTransferItem` hold dragged item data.</span></span> |
| <span data-ttu-id="65a8f-255">Erro</span><span class="sxs-lookup"><span data-stu-id="65a8f-255">Error</span></span>            | `ErrorEventArgs` |
| <span data-ttu-id="65a8f-256">Foco</span><span class="sxs-lookup"><span data-stu-id="65a8f-256">Focus</span></span>            | <span data-ttu-id="65a8f-257">`FocusEventArgs`Não inclui suporte para `relatedTarget`. &ndash;</span><span class="sxs-lookup"><span data-stu-id="65a8f-257">`FocusEventArgs` &ndash; Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="65a8f-258">`<input>`alteração</span><span class="sxs-lookup"><span data-stu-id="65a8f-258">`<input>` change</span></span> | `ChangeEventArgs` |
| <span data-ttu-id="65a8f-259">Teclado</span><span class="sxs-lookup"><span data-stu-id="65a8f-259">Keyboard</span></span>         | `KeyboardEventArgs` |
| <span data-ttu-id="65a8f-260">Mouse</span><span class="sxs-lookup"><span data-stu-id="65a8f-260">Mouse</span></span>            | `MouseEventArgs` |
| <span data-ttu-id="65a8f-261">Ponteiro do mouse</span><span class="sxs-lookup"><span data-stu-id="65a8f-261">Mouse pointer</span></span>    | `PointerEventArgs` |
| <span data-ttu-id="65a8f-262">Roda do mouse</span><span class="sxs-lookup"><span data-stu-id="65a8f-262">Mouse wheel</span></span>      | `WheelEventArgs` |
| <span data-ttu-id="65a8f-263">Progresso</span><span class="sxs-lookup"><span data-stu-id="65a8f-263">Progress</span></span>         | `ProgressEventArgs` |
| <span data-ttu-id="65a8f-264">Toque</span><span class="sxs-lookup"><span data-stu-id="65a8f-264">Touch</span></span>            | <span data-ttu-id="65a8f-265">`TouchEventArgs`&ndash; representaumúnicopontodecontatoemum`TouchPoint` dispositivo sensível ao toque.</span><span class="sxs-lookup"><span data-stu-id="65a8f-265">`TouchEventArgs` &ndash; `TouchPoint` represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="65a8f-266">Para obter informações sobre as propriedades e o comportamento de manipulação de eventos dos eventos na tabela anterior, consulte [classes EventArgs na fonte de referência (ASPNET/AspNetCore Release/3.0-preview9 Branch)](https://github.com/aspnet/AspNetCore/tree/release/3.0-preview9/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="65a8f-266">For information on the properties and event handling behavior of the events in the preceding table, see [EventArgs classes in the reference source (aspnet/AspNetCore release/3.0-preview9 branch)](https://github.com/aspnet/AspNetCore/tree/release/3.0-preview9/src/Components/Web/src/Web).</span></span>

### <a name="lambda-expressions"></a><span data-ttu-id="65a8f-267">Expressões lambda</span><span class="sxs-lookup"><span data-stu-id="65a8f-267">Lambda expressions</span></span>

<span data-ttu-id="65a8f-268">As expressões lambda também podem ser usadas:</span><span class="sxs-lookup"><span data-stu-id="65a8f-268">Lambda expressions can also be used:</span></span>

```cshtml
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="65a8f-269">Geralmente, é conveniente fechar valores adicionais, como ao iterar em um conjunto de elementos.</span><span class="sxs-lookup"><span data-stu-id="65a8f-269">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="65a8f-270">O exemplo a seguir cria três botões, cada um dos `UpdateHeading` quais chamadas passando um argumento`MouseEventArgs`de evento () e seu`buttonNumber`número de botão () quando selecionado na interface do usuário:</span><span class="sxs-lookup"><span data-stu-id="65a8f-270">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (`MouseEventArgs`) and its button number (`buttonNumber`) when selected in the UI:</span></span>

```cshtml
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
> <span data-ttu-id="65a8f-271">**Não** use a variável de loop (`i`) em um `for` loop diretamente em uma expressão lambda.</span><span class="sxs-lookup"><span data-stu-id="65a8f-271">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="65a8f-272">Caso contrário, a mesma variável é usada por todas as `i`expressões lambda, fazendo com que o valor seja o mesmo em todos os lambdas.</span><span class="sxs-lookup"><span data-stu-id="65a8f-272">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="65a8f-273">Sempre Capture seu valor em uma variável local (`buttonNumber` no exemplo anterior) e use-o.</span><span class="sxs-lookup"><span data-stu-id="65a8f-273">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

### <a name="eventcallback"></a><span data-ttu-id="65a8f-274">EventCallback</span><span class="sxs-lookup"><span data-stu-id="65a8f-274">EventCallback</span></span>

<span data-ttu-id="65a8f-275">Um cenário comum com componentes aninhados é o desejo de executar o método de um componente pai quando ocorre&mdash;um evento de componente filho, por exemplo, quando um `onclick` evento ocorre no filho.</span><span class="sxs-lookup"><span data-stu-id="65a8f-275">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs&mdash;for example, when an `onclick` event occurs in the child.</span></span> <span data-ttu-id="65a8f-276">Para expor eventos entre componentes, use um `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="65a8f-276">To expose events across components, use an `EventCallback`.</span></span> <span data-ttu-id="65a8f-277">Um componente pai pode atribuir um método de retorno de chamada a um `EventCallback`componente filho.</span><span class="sxs-lookup"><span data-stu-id="65a8f-277">A parent component can assign a callback method to a child component's `EventCallback`.</span></span>

<span data-ttu-id="65a8f-278">O `ChildComponent` no aplicativo de exemplo demonstra como o `onclick` manipulador de um botão é configurado para receber um `EventCallback` delegado de exemplo `ParentComponent`.</span><span class="sxs-lookup"><span data-stu-id="65a8f-278">The `ChildComponent` in the sample app demonstrates how a button's `onclick` handler is set up to receive an `EventCallback` delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="65a8f-279">O `EventCallback` é digitado `MouseEventArgs`com, que é apropriado para `onclick` um evento de um dispositivo periférico:</span><span class="sxs-lookup"><span data-stu-id="65a8f-279">The `EventCallback` is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="65a8f-280">O `ParentComponent` define o `EventCallback<T>` filho como seu `ShowMessage` método:</span><span class="sxs-lookup"><span data-stu-id="65a8f-280">The `ParentComponent` sets the child's `EventCallback<T>` to its `ShowMessage` method:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=6,16-19)]

<span data-ttu-id="65a8f-281">Quando o botão estiver selecionado no `ChildComponent`:</span><span class="sxs-lookup"><span data-stu-id="65a8f-281">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="65a8f-282">O `ParentComponent`métodoéchamado `ShowMessage` .</span><span class="sxs-lookup"><span data-stu-id="65a8f-282">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="65a8f-283">`messageText`é atualizado e exibido no `ParentComponent`.</span><span class="sxs-lookup"><span data-stu-id="65a8f-283">`messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="65a8f-284">Uma chamada para `StateHasChanged` não é necessária no método do retorno de`ShowMessage`chamada ().</span><span class="sxs-lookup"><span data-stu-id="65a8f-284">A call to `StateHasChanged` isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="65a8f-285">`StateHasChanged`é chamado automaticamente para renderizar novamente `ParentComponent`o, assim como eventos filho, rerenderização de componente em manipuladores de eventos que são executados dentro do filho.</span><span class="sxs-lookup"><span data-stu-id="65a8f-285">`StateHasChanged` is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="65a8f-286">`EventCallback`e `EventCallback<T>` permitir delegados assíncronos.</span><span class="sxs-lookup"><span data-stu-id="65a8f-286">`EventCallback` and `EventCallback<T>` permit asynchronous delegates.</span></span> <span data-ttu-id="65a8f-287">`EventCallback<T>`é fortemente tipado e requer um tipo de argumento específico.</span><span class="sxs-lookup"><span data-stu-id="65a8f-287">`EventCallback<T>` is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="65a8f-288">`EventCallback`está com tipo fraco e permite qualquer tipo de argumento.</span><span class="sxs-lookup"><span data-stu-id="65a8f-288">`EventCallback` is weakly typed and allows any argument type.</span></span>

```cshtml
<p><b>@messageText</b></p>

@{ var message = "Default Text"; }

<ChildComponent 
    OnClick="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />

@code {
    private string messageText;
}
```

<span data-ttu-id="65a8f-289">Invocar `EventCallback` um `EventCallback<T>` ou `InvokeAsync` com e aguardar <xref:System.Threading.Tasks.Task>:</span><span class="sxs-lookup"><span data-stu-id="65a8f-289">Invoke an `EventCallback` or `EventCallback<T>` with `InvokeAsync` and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="65a8f-290">Use `EventCallback` e`EventCallback<T>` para manipulação de eventos e parâmetros de componente de associação.</span><span class="sxs-lookup"><span data-stu-id="65a8f-290">Use `EventCallback` and `EventCallback<T>` for event handling and binding component parameters.</span></span>

<span data-ttu-id="65a8f-291">Prefira o tipo fortemente `EventCallback<T>` tipado `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="65a8f-291">Prefer the strongly typed `EventCallback<T>` over `EventCallback`.</span></span> <span data-ttu-id="65a8f-292">`EventCallback<T>`fornece melhores comentários de erro para os usuários do componente.</span><span class="sxs-lookup"><span data-stu-id="65a8f-292">`EventCallback<T>` provides better error feedback to users of the component.</span></span> <span data-ttu-id="65a8f-293">Semelhante a outros manipuladores de eventos de interface do usuário, especificar o parâmetro de evento é opcional.</span><span class="sxs-lookup"><span data-stu-id="65a8f-293">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="65a8f-294">Use `EventCallback` quando não houver valor passado para o retorno de chamada.</span><span class="sxs-lookup"><span data-stu-id="65a8f-294">Use `EventCallback` when there's no value passed to the callback.</span></span>

## <a name="chained-bind"></a><span data-ttu-id="65a8f-295">Associação encadeada</span><span class="sxs-lookup"><span data-stu-id="65a8f-295">Chained bind</span></span>

<span data-ttu-id="65a8f-296">Um cenário comum é encadear um parâmetro de associação de dados a um elemento de página na saída do componente.</span><span class="sxs-lookup"><span data-stu-id="65a8f-296">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="65a8f-297">Esse cenário é chamado de *Associação encadeada* porque vários níveis de associação ocorrem simultaneamente.</span><span class="sxs-lookup"><span data-stu-id="65a8f-297">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="65a8f-298">Uma associação encadeada não pode ser implementada com `@bind` a sintaxe no elemento da página.</span><span class="sxs-lookup"><span data-stu-id="65a8f-298">A chained bind can't be implemented with `@bind` syntax in the page's element.</span></span> <span data-ttu-id="65a8f-299">O manipulador de eventos e o valor devem ser especificados separadamente.</span><span class="sxs-lookup"><span data-stu-id="65a8f-299">The event handler and value must be specified separately.</span></span> <span data-ttu-id="65a8f-300">Um componente pai, no entanto, `@bind` pode usar a sintaxe com o parâmetro do componente.</span><span class="sxs-lookup"><span data-stu-id="65a8f-300">A parent component, however, can use `@bind` syntax with the component's parameter.</span></span>

<span data-ttu-id="65a8f-301">O seguinte `PasswordField` componente (*passwordField. Razor*):</span><span class="sxs-lookup"><span data-stu-id="65a8f-301">The following `PasswordField` component (*PasswordField.razor*):</span></span>

* <span data-ttu-id="65a8f-302">Define o `<input>` valor de um elemento para `Password` uma propriedade.</span><span class="sxs-lookup"><span data-stu-id="65a8f-302">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="65a8f-303">Expõe as alterações da `Password` propriedade para um componente pai com um [EventCallback](#eventcallback).</span><span class="sxs-lookup"><span data-stu-id="65a8f-303">Exposes changes of the `Password` property to a parent component with an [EventCallback](#eventcallback).</span></span>

```cshtml
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

<span data-ttu-id="65a8f-304">O `PasswordField` componente é usado em outro componente:</span><span class="sxs-lookup"><span data-stu-id="65a8f-304">The `PasswordField` component is used in another component:</span></span>

```cshtml
<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

<span data-ttu-id="65a8f-305">Para executar verificações ou interceptar erros na senha no exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="65a8f-305">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="65a8f-306">Crie um campo de backup para `Password` (`password` no código de exemplo a seguir).</span><span class="sxs-lookup"><span data-stu-id="65a8f-306">Create a backing field for `Password` (`password` in the following example code).</span></span>
* <span data-ttu-id="65a8f-307">Execute os erros de verificação ou interceptação `Password` no setter.</span><span class="sxs-lookup"><span data-stu-id="65a8f-307">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="65a8f-308">O exemplo a seguir fornecerá comentários imediatos para o usuário se um espaço for usado no valor da senha:</span><span class="sxs-lookup"><span data-stu-id="65a8f-308">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

```cshtml
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

## <a name="capture-references-to-components"></a><span data-ttu-id="65a8f-309">Capturar referências a componentes</span><span class="sxs-lookup"><span data-stu-id="65a8f-309">Capture references to components</span></span>

<span data-ttu-id="65a8f-310">As `Show` referências de componente fornecem uma maneira de fazer referência a uma instância de componente para que você possa emitir comandos para essa `Reset`instância, como ou.</span><span class="sxs-lookup"><span data-stu-id="65a8f-310">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="65a8f-311">Para capturar uma referência de componente:</span><span class="sxs-lookup"><span data-stu-id="65a8f-311">To capture a component reference:</span></span>

* <span data-ttu-id="65a8f-312">Adicione um [@ref](xref:mvc/views/razor#ref) atributo ao componente filho.</span><span class="sxs-lookup"><span data-stu-id="65a8f-312">Add an [@ref](xref:mvc/views/razor#ref) attribute to the child component.</span></span>
* <span data-ttu-id="65a8f-313">Defina um campo com o mesmo tipo do componente filho.</span><span class="sxs-lookup"><span data-stu-id="65a8f-313">Define a field with the same type as the child component.</span></span>

```cshtml
<MyLoginDialog @ref="loginDialog" ... />

@code {
    private MyLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

<span data-ttu-id="65a8f-314">Quando o componente é renderizado, `loginDialog` o campo é populado com a instância de `MyLoginDialog` componente filho.</span><span class="sxs-lookup"><span data-stu-id="65a8f-314">When the component is rendered, the `loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="65a8f-315">Em seguida, você pode invocar os métodos .NET na instância do componente.</span><span class="sxs-lookup"><span data-stu-id="65a8f-315">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="65a8f-316">A `loginDialog` variável é populada apenas depois que o componente é renderizado e `MyLoginDialog` sua saída inclui o elemento.</span><span class="sxs-lookup"><span data-stu-id="65a8f-316">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="65a8f-317">Até esse ponto, não há nada a fazer referência.</span><span class="sxs-lookup"><span data-stu-id="65a8f-317">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="65a8f-318">Para manipular referências de componentes após a conclusão da renderização do componente, `OnAfterRenderAsync` use `OnAfterRender` os métodos ou.</span><span class="sxs-lookup"><span data-stu-id="65a8f-318">To manipulate components references after the component has finished rendering, use the `OnAfterRenderAsync` or `OnAfterRender` methods.</span></span>

<span data-ttu-id="65a8f-319">Embora a captura de referências de componente use uma sintaxe semelhante à [captura de referências de elemento](xref:blazor/javascript-interop#capture-references-to-elements), ela não é um recurso de [interoperabilidade do JavaScript](xref:blazor/javascript-interop) .</span><span class="sxs-lookup"><span data-stu-id="65a8f-319">While capturing component references use a similar syntax to [capturing element references](xref:blazor/javascript-interop#capture-references-to-elements), it isn't a [JavaScript interop](xref:blazor/javascript-interop) feature.</span></span> <span data-ttu-id="65a8f-320">As referências de componente não são passadas para o código&mdash;JavaScript que são usadas apenas no código .net.</span><span class="sxs-lookup"><span data-stu-id="65a8f-320">Component references aren't passed to JavaScript code&mdash;they're only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="65a8f-321">Não **use referências** de componente para converter o estado dos componentes filho.</span><span class="sxs-lookup"><span data-stu-id="65a8f-321">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="65a8f-322">Em vez disso, use parâmetros declarativos normais para passar dados para componentes filho.</span><span class="sxs-lookup"><span data-stu-id="65a8f-322">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="65a8f-323">O uso de parâmetros declarativos normais resulta em componentes filho que são reprocessados nos horários corretos automaticamente.</span><span class="sxs-lookup"><span data-stu-id="65a8f-323">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="65a8f-324">Invocar métodos de componente externamente para atualizar o estado</span><span class="sxs-lookup"><span data-stu-id="65a8f-324">Invoke component methods externally to update state</span></span>

<span data-ttu-id="65a8f-325">O mais incrivelmente usa `SynchronizationContext` um para impor um único thread lógico de execução.</span><span class="sxs-lookup"><span data-stu-id="65a8f-325">Blazor uses a `SynchronizationContext` to enforce a single logical thread of execution.</span></span> <span data-ttu-id="65a8f-326">Os métodos de ciclo de vida de um componente e quaisquer retornos de chamada de evento que são gerados `SynchronizationContext`pelo mais alto são executados nesse.</span><span class="sxs-lookup"><span data-stu-id="65a8f-326">A component's lifecycle methods and any event callbacks that are raised by Blazor are executed on this `SynchronizationContext`.</span></span> <span data-ttu-id="65a8f-327">No caso de um componente precisar ser atualizado com base em um evento externo, como um temporizador ou outras notificações, use `InvokeAsync` o método, que será redespachado para `SynchronizationContext`o mais bem.</span><span class="sxs-lookup"><span data-stu-id="65a8f-327">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which will dispatch to Blazor's `SynchronizationContext`.</span></span>

<span data-ttu-id="65a8f-328">Por exemplo, considere um *serviço de notificação* que pode notificar qualquer componente de escuta do estado atualizado:</span><span class="sxs-lookup"><span data-stu-id="65a8f-328">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

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

<span data-ttu-id="65a8f-329">Uso do `NotifierService` para atualizar um componente:</span><span class="sxs-lookup"><span data-stu-id="65a8f-329">Usage of the `NotifierService` to update a component:</span></span>

```cshtml
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

<span data-ttu-id="65a8f-330">No exemplo anterior, `NotifierService` invoca o método do `OnNotify` componente `SynchronizationContext`fora do mais claro.</span><span class="sxs-lookup"><span data-stu-id="65a8f-330">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's `SynchronizationContext`.</span></span> <span data-ttu-id="65a8f-331">`InvokeAsync`é usado para alternar para o contexto correto e enfileirar uma renderização.</span><span class="sxs-lookup"><span data-stu-id="65a8f-331">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="65a8f-332">Use \@a chave para controlar a preservação de elementos e componentes</span><span class="sxs-lookup"><span data-stu-id="65a8f-332">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="65a8f-333">Ao renderizar uma lista de elementos ou componentes e, subsequentemente, os elementos ou componentes são alterados, o algoritmo de diferenciação do mais claro deve decidir quais elementos ou componentes anteriores podem ser mantidos e como os objetos de modelo devem ser mapeados para eles.</span><span class="sxs-lookup"><span data-stu-id="65a8f-333">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="65a8f-334">Normalmente, esse processo é automático e pode ser ignorado, mas há casos em que você talvez queira controlar o processo.</span><span class="sxs-lookup"><span data-stu-id="65a8f-334">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="65a8f-335">Considere o exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="65a8f-335">Consider the following example:</span></span>

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

<span data-ttu-id="65a8f-336">O conteúdo da `People` coleção pode ser alterado com entradas inseridas, excluídas ou reordenadas.</span><span class="sxs-lookup"><span data-stu-id="65a8f-336">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="65a8f-337">Quando o componente é rerenderizado, `<DetailsEditor>` o componente pode ser alterado para `Details` receber valores de parâmetro diferentes.</span><span class="sxs-lookup"><span data-stu-id="65a8f-337">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="65a8f-338">Isso pode causar um reprocessamento mais complexo do que o esperado.</span><span class="sxs-lookup"><span data-stu-id="65a8f-338">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="65a8f-339">Em alguns casos, a rerenderização pode levar a diferenças de comportamento visíveis, como o foco de elemento perdido.</span><span class="sxs-lookup"><span data-stu-id="65a8f-339">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="65a8f-340">O processo de mapeamento pode ser controlado com `@key` o atributo de diretiva.</span><span class="sxs-lookup"><span data-stu-id="65a8f-340">The mapping process can be controlled with the `@key` directive attribute.</span></span> <span data-ttu-id="65a8f-341">`@key`faz com que o algoritmo diff garanta a preservação de elementos ou componentes com base no valor da chave:</span><span class="sxs-lookup"><span data-stu-id="65a8f-341">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

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

<span data-ttu-id="65a8f-342">Quando a `People` coleção é alterada, o algoritmo diff mantém a associação entre `<DetailsEditor>` instâncias e `person` instâncias:</span><span class="sxs-lookup"><span data-stu-id="65a8f-342">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="65a8f-343">Se um `Person` for excluído `People` da lista, somente a instância correspondente `<DetailsEditor>` será removida da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="65a8f-343">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="65a8f-344">Outras instâncias permanecem inalteradas.</span><span class="sxs-lookup"><span data-stu-id="65a8f-344">Other instances are left unchanged.</span></span>
* <span data-ttu-id="65a8f-345">Se um `Person` for inserido em alguma posição na lista, uma nova `<DetailsEditor>` instância será inserida na posição correspondente.</span><span class="sxs-lookup"><span data-stu-id="65a8f-345">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="65a8f-346">Outras instâncias permanecem inalteradas.</span><span class="sxs-lookup"><span data-stu-id="65a8f-346">Other instances are left unchanged.</span></span>
* <span data-ttu-id="65a8f-347">Se `Person` as entradas forem reordenadas, as `<DetailsEditor>` instâncias correspondentes serão preservadas e reordenadas na interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="65a8f-347">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="65a8f-348">Em alguns cenários, o uso `@key` de minimiza a complexidade da rerenderização e evita possíveis problemas com partes com estado da alteração do dom, como a posição do foco.</span><span class="sxs-lookup"><span data-stu-id="65a8f-348">In some scenarios, use of `@key` minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="65a8f-349">As chaves são locais para cada elemento ou componente de contêiner.</span><span class="sxs-lookup"><span data-stu-id="65a8f-349">Keys are local to each container element or component.</span></span> <span data-ttu-id="65a8f-350">As chaves não são comparadas globalmente ao longo do documento.</span><span class="sxs-lookup"><span data-stu-id="65a8f-350">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="65a8f-351">Quando usar \@a chave</span><span class="sxs-lookup"><span data-stu-id="65a8f-351">When to use \@key</span></span>

<span data-ttu-id="65a8f-352">Normalmente, faz sentido usar `@key` sempre que uma lista é renderizada (por exemplo, em um `@foreach` bloco) e um valor adequado existe para definir o. `@key`</span><span class="sxs-lookup"><span data-stu-id="65a8f-352">Typically, it makes sense to use `@key` whenever a list is rendered (for example, in a `@foreach` block) and a suitable value exists to define the `@key`.</span></span>

<span data-ttu-id="65a8f-353">Você também pode usar `@key` o para evitar que o mais incrivelmente de preservar uma subárvore de elementos ou componentes quando um objeto for alterado:</span><span class="sxs-lookup"><span data-stu-id="65a8f-353">You can also use `@key` to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```cshtml
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="65a8f-354">Se `@currentPerson` forem alteradas `@key` , a diretiva de atributo forçará o mais `<div>` incrivelmente a descartar todo e seus descendentes e recriar a subárvore dentro da interface do usuário com novos elementos e componentes.</span><span class="sxs-lookup"><span data-stu-id="65a8f-354">If `@currentPerson` changes, the `@key` attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="65a8f-355">Isso pode ser útil se você precisar garantir que nenhum estado da interface do usuário seja `@currentPerson` preservado quando houver alterações.</span><span class="sxs-lookup"><span data-stu-id="65a8f-355">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="65a8f-356">Quando não usar \@a chave</span><span class="sxs-lookup"><span data-stu-id="65a8f-356">When not to use \@key</span></span>

<span data-ttu-id="65a8f-357">Há um custo de desempenho ao comparar com `@key`.</span><span class="sxs-lookup"><span data-stu-id="65a8f-357">There's a performance cost when diffing with `@key`.</span></span> <span data-ttu-id="65a8f-358">O custo de desempenho não é grande, mas `@key` só especifica se controlar as regras de preservação de elementos ou componentes beneficiam o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="65a8f-358">The performance cost isn't large, but only specify `@key` if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="65a8f-359">Mesmo que `@key` não seja usado, o mais grande preserva o elemento filho e as instâncias de componente o máximo possível.</span><span class="sxs-lookup"><span data-stu-id="65a8f-359">Even if `@key` isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="65a8f-360">A única vantagem de usar `@key` o é o controle sobre *como* as instâncias de modelo são mapeadas para as instâncias de componente preservadas, em vez do algoritmo diff, selecionando o mapeamento.</span><span class="sxs-lookup"><span data-stu-id="65a8f-360">The only advantage to using `@key` is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="65a8f-361">Quais valores usar para \@a chave</span><span class="sxs-lookup"><span data-stu-id="65a8f-361">What values to use for \@key</span></span>

<span data-ttu-id="65a8f-362">Geralmente, faz sentido fornecer um dos seguintes tipos de valor para `@key`:</span><span class="sxs-lookup"><span data-stu-id="65a8f-362">Generally, it makes sense to supply one of the following kinds of value for `@key`:</span></span>

* <span data-ttu-id="65a8f-363">Instâncias de objeto de modelo (por exemplo `Person` , uma instância como no exemplo anterior).</span><span class="sxs-lookup"><span data-stu-id="65a8f-363">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="65a8f-364">Isso garante a preservação com base na igualdade de referência de objeto.</span><span class="sxs-lookup"><span data-stu-id="65a8f-364">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="65a8f-365">Identificadores exclusivos (por exemplo, valores de chave primária do `int`tipo `string`, ou `Guid`).</span><span class="sxs-lookup"><span data-stu-id="65a8f-365">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="65a8f-366">Verifique se os valores usados `@key` para não conflitam.</span><span class="sxs-lookup"><span data-stu-id="65a8f-366">Ensure that values used for `@key` don't clash.</span></span> <span data-ttu-id="65a8f-367">Se os valores conflitantes forem detectados no mesmo elemento pai, o mais velho lançará uma exceção porque não pode mapear determinísticamente elementos ou componentes antigos para novos elementos ou componentes.</span><span class="sxs-lookup"><span data-stu-id="65a8f-367">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="65a8f-368">Use apenas valores distintos, como instâncias de objeto ou valores de chave primária.</span><span class="sxs-lookup"><span data-stu-id="65a8f-368">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="65a8f-369">Métodos de ciclo de vida</span><span class="sxs-lookup"><span data-stu-id="65a8f-369">Lifecycle methods</span></span>

<span data-ttu-id="65a8f-370">`OnInitializedAsync`e `OnInitialized` execute o código para inicializar o componente.</span><span class="sxs-lookup"><span data-stu-id="65a8f-370">`OnInitializedAsync` and `OnInitialized` execute code to initialize the component.</span></span> <span data-ttu-id="65a8f-371">Para executar uma operação assíncrona, `OnInitializedAsync` use e `await` a palavra-chave na operação:</span><span class="sxs-lookup"><span data-stu-id="65a8f-371">To perform an asynchronous operation, use `OnInitializedAsync` and the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

<span data-ttu-id="65a8f-372">Para uma operação síncrona, use `OnInitialized`:</span><span class="sxs-lookup"><span data-stu-id="65a8f-372">For a synchronous operation, use `OnInitialized`:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="65a8f-373">`OnParametersSetAsync`e `OnParametersSet` são chamados quando um componente recebe parâmetros de seu pai e os valores são atribuídos às propriedades.</span><span class="sxs-lookup"><span data-stu-id="65a8f-373">`OnParametersSetAsync` and `OnParametersSet` are called when a component has received parameters from its parent and the values are assigned to properties.</span></span> <span data-ttu-id="65a8f-374">Esses métodos são executados após a inicialização do componente e cada vez que o componente é renderizado:</span><span class="sxs-lookup"><span data-stu-id="65a8f-374">These methods are executed after component initialization and each time the component is rendered:</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

```csharp
protected override void OnParametersSet()
{
    ...
}
```

<span data-ttu-id="65a8f-375">`OnAfterRenderAsync`e `OnAfterRender` são chamados após a conclusão da renderização de um componente.</span><span class="sxs-lookup"><span data-stu-id="65a8f-375">`OnAfterRenderAsync` and `OnAfterRender` are called after a component has finished rendering.</span></span> <span data-ttu-id="65a8f-376">Referências de elemento e componente são preenchidas neste ponto.</span><span class="sxs-lookup"><span data-stu-id="65a8f-376">Element and component references are populated at this point.</span></span> <span data-ttu-id="65a8f-377">Use este estágio para executar etapas de inicialização adicionais usando o conteúdo renderizado, como a ativação de bibliotecas JavaScript de terceiros que operam nos elementos DOM renderizados.</span><span class="sxs-lookup"><span data-stu-id="65a8f-377">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="65a8f-378">`OnAfterRender`*não é chamado durante o pré-processamento no servidor.*</span><span class="sxs-lookup"><span data-stu-id="65a8f-378">`OnAfterRender` *is not called when prerendering on the server.*</span></span>

<span data-ttu-id="65a8f-379">O `firstRender` parâmetro para `OnAfterRenderAsync` e `OnAfterRender` é:</span><span class="sxs-lookup"><span data-stu-id="65a8f-379">The `firstRender` parameter for `OnAfterRenderAsync` and `OnAfterRender` is:</span></span>

* <span data-ttu-id="65a8f-380">Defina como `true` a primeira vez que a instância do componente é invocada.</span><span class="sxs-lookup"><span data-stu-id="65a8f-380">Set to `true` the first time that the component instance is invoked.</span></span>
* <span data-ttu-id="65a8f-381">Garante que o trabalho de inicialização seja executado apenas uma vez.</span><span class="sxs-lookup"><span data-stu-id="65a8f-381">Ensures that initialization work is only performed once.</span></span>

```csharp
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (firstRender)
    {
        await ...
    }
}
```

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

### <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="65a8f-382">Tratar ações assíncronas incompletas no processamento</span><span class="sxs-lookup"><span data-stu-id="65a8f-382">Handle incomplete async actions at render</span></span>

<span data-ttu-id="65a8f-383">Ações assíncronas executadas em eventos de ciclo de vida podem não ter sido concluídas antes que o componente seja renderizado.</span><span class="sxs-lookup"><span data-stu-id="65a8f-383">Asynchronous actions performed in lifecycle events may not have completed before the component is rendered.</span></span> <span data-ttu-id="65a8f-384">Os objetos podem `null` ser ou preenchidos incompletamente com dados enquanto o método de ciclo de vida está em execução.</span><span class="sxs-lookup"><span data-stu-id="65a8f-384">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="65a8f-385">Forneça a lógica de renderização para confirmar que os objetos são inicializados.</span><span class="sxs-lookup"><span data-stu-id="65a8f-385">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="65a8f-386">Renderizar elementos de interface do usuário de espaço reservado (por exemplo, uma `null`mensagem de carregamento) enquanto objetos são.</span><span class="sxs-lookup"><span data-stu-id="65a8f-386">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="65a8f-387">No componente dos modelos mais claros, `OnInitializedAsync` é substituído para Asychronously receber dados de previsão (`forecasts`). `FetchData`</span><span class="sxs-lookup"><span data-stu-id="65a8f-387">In the `FetchData` component of the Blazor templates, `OnInitializedAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="65a8f-388">Quando `forecasts` é`null`, uma mensagem de carregamento é exibida para o usuário.</span><span class="sxs-lookup"><span data-stu-id="65a8f-388">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="65a8f-389">Depois que `Task` o retornado `OnInitializedAsync` por for concluído, o componente será rerenderizado com o estado atualizado.</span><span class="sxs-lookup"><span data-stu-id="65a8f-389">After the `Task` returned by `OnInitializedAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="65a8f-390">*Pages/FetchData.razor*:</span><span class="sxs-lookup"><span data-stu-id="65a8f-390">*Pages/FetchData.razor*:</span></span>

[!code-cshtml[](components/samples_snapshot/3.x/FetchData.razor?highlight=9)]

### <a name="execute-code-before-parameters-are-set"></a><span data-ttu-id="65a8f-391">Executar código antes que os parâmetros sejam definidos</span><span class="sxs-lookup"><span data-stu-id="65a8f-391">Execute code before parameters are set</span></span>

<span data-ttu-id="65a8f-392">`SetParameters`pode ser substituído para executar o código antes de os parâmetros serem definidos:</span><span class="sxs-lookup"><span data-stu-id="65a8f-392">`SetParameters` can be overridden to execute code before parameters are set:</span></span>

```csharp
public override void SetParameters(ParameterView parameters)
{
    ...

    base.SetParameters(parameters);
}
```

<span data-ttu-id="65a8f-393">Se `base.SetParameters` não for invocado, o código personalizado poderá interpretar o valor dos parâmetros de entrada de qualquer forma necessária.</span><span class="sxs-lookup"><span data-stu-id="65a8f-393">If `base.SetParameters` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="65a8f-394">Por exemplo, os parâmetros de entrada não precisam ser atribuídos às propriedades na classe.</span><span class="sxs-lookup"><span data-stu-id="65a8f-394">For example, the incoming parameters aren't required to be assigned to the properties on the class.</span></span>

### <a name="suppress-refreshing-of-the-ui"></a><span data-ttu-id="65a8f-395">Suprimir a atualização da interface do usuário</span><span class="sxs-lookup"><span data-stu-id="65a8f-395">Suppress refreshing of the UI</span></span>

<span data-ttu-id="65a8f-396">`ShouldRender`pode ser substituído para suprimir a atualização da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="65a8f-396">`ShouldRender` can be overridden to suppress refreshing of the UI.</span></span> <span data-ttu-id="65a8f-397">Se a implementação retornar `true`, a interface do usuário será atualizada.</span><span class="sxs-lookup"><span data-stu-id="65a8f-397">If the implementation returns `true`, the UI is refreshed.</span></span> <span data-ttu-id="65a8f-398">Mesmo se `ShouldRender` for substituído, o componente sempre será renderizado inicialmente.</span><span class="sxs-lookup"><span data-stu-id="65a8f-398">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="65a8f-399">Descarte de componentes com IDisposable</span><span class="sxs-lookup"><span data-stu-id="65a8f-399">Component disposal with IDisposable</span></span>

<span data-ttu-id="65a8f-400">Se um componente implementa <xref:System.IDisposable>, o [método Dispose](/dotnet/standard/garbage-collection/implementing-dispose) é chamado quando o componente é removido da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="65a8f-400">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="65a8f-401">O componente a seguir `@implements IDisposable` usa o `Dispose` e o método:</span><span class="sxs-lookup"><span data-stu-id="65a8f-401">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

```csharp
@using System
@implements IDisposable

...

@code {
    public void Dispose()
    {
        ...
    }
}
```

## <a name="routing"></a><span data-ttu-id="65a8f-402">Roteamento</span><span class="sxs-lookup"><span data-stu-id="65a8f-402">Routing</span></span>

<span data-ttu-id="65a8f-403">O roteamento no mais fácil é obtido fornecendo um modelo de rota para cada componente acessível no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="65a8f-403">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="65a8f-404">Quando um arquivo Razor com uma `@page` diretiva é compilado, a classe gerada recebe um <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> especificando o modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="65a8f-404">When a Razor file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="65a8f-405">Em tempo de execução, o roteador procura classes de componentes `RouteAttribute` com um e renderiza qualquer componente que tenha um modelo de rota que corresponda à URL solicitada.</span><span class="sxs-lookup"><span data-stu-id="65a8f-405">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

<span data-ttu-id="65a8f-406">Vários modelos de rota podem ser aplicados a um componente.</span><span class="sxs-lookup"><span data-stu-id="65a8f-406">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="65a8f-407">O componente a seguir responde a solicitações `/BlazorRoute` para `/DifferentBlazorRoute`o e o:</span><span class="sxs-lookup"><span data-stu-id="65a8f-407">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

## <a name="route-parameters"></a><span data-ttu-id="65a8f-408">Parâmetros de rota</span><span class="sxs-lookup"><span data-stu-id="65a8f-408">Route parameters</span></span>

<span data-ttu-id="65a8f-409">Os componentes podem receber parâmetros de rota do modelo de rota fornecido `@page` na diretiva.</span><span class="sxs-lookup"><span data-stu-id="65a8f-409">Components can receive route parameters from the route template provided in the `@page` directive.</span></span> <span data-ttu-id="65a8f-410">O roteador usa parâmetros de rota para preencher os parâmetros de componente correspondentes.</span><span class="sxs-lookup"><span data-stu-id="65a8f-410">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="65a8f-411">*Componente de parâmetro de rota*:</span><span class="sxs-lookup"><span data-stu-id="65a8f-411">*Route Parameter component*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter)]

<span data-ttu-id="65a8f-412">Não há suporte para parâmetros opcionais `@page` , portanto, duas diretivas são aplicadas no exemplo acima.</span><span class="sxs-lookup"><span data-stu-id="65a8f-412">Optional parameters aren't supported, so two `@page` directives are applied in the example above.</span></span> <span data-ttu-id="65a8f-413">O primeiro permite a navegação para o componente sem um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="65a8f-413">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="65a8f-414">A segunda `@page` diretiva usa o `{text}` parâmetro de rota e atribui o valor à `Text` propriedade.</span><span class="sxs-lookup"><span data-stu-id="65a8f-414">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="base-class-inheritance-for-a-code-behind-experience"></a><span data-ttu-id="65a8f-415">Herança de classe base para uma experiência de "code-behind"</span><span class="sxs-lookup"><span data-stu-id="65a8f-415">Base class inheritance for a "code-behind" experience</span></span>

<span data-ttu-id="65a8f-416">Os arquivos de componente misturam C# o código de marcação e processamento HTML no mesmo arquivo.</span><span class="sxs-lookup"><span data-stu-id="65a8f-416">Component files mix HTML markup and C# processing code in the same file.</span></span> <span data-ttu-id="65a8f-417">A `@inherits` diretiva pode ser usada para fornecer aos aplicativos mais incrivelmente uma experiência de "code-behind" que separa a marcação de componente do código de processamento.</span><span class="sxs-lookup"><span data-stu-id="65a8f-417">The `@inherits` directive can be used to provide Blazor apps with a "code-behind" experience that separates component markup from processing code.</span></span>

<span data-ttu-id="65a8f-418">O [aplicativo de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) mostra como um componente pode herdar uma classe `BlazorRocksBase`base,, para fornecer as propriedades e os métodos do componente.</span><span class="sxs-lookup"><span data-stu-id="65a8f-418">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span>

<span data-ttu-id="65a8f-419">*Páginas/BlazorRocks. Razor*:</span><span class="sxs-lookup"><span data-stu-id="65a8f-419">*Pages/BlazorRocks.razor*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRocks.razor?name=snippet_BlazorRocks)]

<span data-ttu-id="65a8f-420">*BlazorRocksBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="65a8f-420">*BlazorRocksBase.cs*:</span></span>

[!code-csharp[](common/samples/3.x/BlazorSample/Pages/BlazorRocksBase.cs)]

<span data-ttu-id="65a8f-421">A classe base deve derivar `ComponentBase`de.</span><span class="sxs-lookup"><span data-stu-id="65a8f-421">The base class should derive from `ComponentBase`.</span></span>

## <a name="import-components"></a><span data-ttu-id="65a8f-422">Importar componentes</span><span class="sxs-lookup"><span data-stu-id="65a8f-422">Import components</span></span>

<span data-ttu-id="65a8f-423">O namespace de um componente criado com o Razor se baseia em:</span><span class="sxs-lookup"><span data-stu-id="65a8f-423">The namespace of a component authored with Razor is based on:</span></span>

* <span data-ttu-id="65a8f-424">O projeto `RootNamespace`.</span><span class="sxs-lookup"><span data-stu-id="65a8f-424">The project's `RootNamespace`.</span></span>
* <span data-ttu-id="65a8f-425">O caminho da raiz do projeto para o componente.</span><span class="sxs-lookup"><span data-stu-id="65a8f-425">The path from the project root to the component.</span></span> <span data-ttu-id="65a8f-426">Por exemplo, `ComponentsSample/Pages/Index.razor` está no namespace. `ComponentsSample.Pages`</span><span class="sxs-lookup"><span data-stu-id="65a8f-426">For example, `ComponentsSample/Pages/Index.razor` is in the namespace `ComponentsSample.Pages`.</span></span> <span data-ttu-id="65a8f-427">Os componentes C# seguem regras de associação de nome.</span><span class="sxs-lookup"><span data-stu-id="65a8f-427">Components follow C# name binding rules.</span></span> <span data-ttu-id="65a8f-428">No caso de *index. Razor*, todos os componentes na mesma pasta, *páginas*e a pasta pai, *ComponentsSample*, estão no escopo.</span><span class="sxs-lookup"><span data-stu-id="65a8f-428">In the case of *Index.razor*, all components in the same folder, *Pages*, and the parent folder, *ComponentsSample*, are in scope.</span></span>

<span data-ttu-id="65a8f-429">Os componentes definidos em um namespace diferente podem ser trazidos para o escopo usando a diretiva [ \@using](xref:mvc/views/razor#using) do Razor.</span><span class="sxs-lookup"><span data-stu-id="65a8f-429">Components defined in a different namespace can be brought into scope using Razor's [\@using](xref:mvc/views/razor#using) directive.</span></span>

<span data-ttu-id="65a8f-430">Se outro componente, `NavMenu.razor`, existir na pasta `ComponentsSample/Shared/`, o `Index.razor` componente poderá ser usado com a seguinte `@using` instrução:</span><span class="sxs-lookup"><span data-stu-id="65a8f-430">If another component, `NavMenu.razor`, exists in the folder `ComponentsSample/Shared/`, the component can be used in `Index.razor` with the following `@using` statement:</span></span>

```cshtml
@using ComponentsSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

<span data-ttu-id="65a8f-431">Os componentes também podem ser referenciados usando seus nomes totalmente qualificados, o que elimina a [ \@](xref:mvc/views/razor#using) necessidade da diretiva de uso:</span><span class="sxs-lookup"><span data-stu-id="65a8f-431">Components can also be referenced using their fully qualified names, which removes the need for the [\@using](xref:mvc/views/razor#using) directive:</span></span>

```cshtml
This is the Index page.

<ComponentsSample.Shared.NavMenu></ComponentsSample.Shared.NavMenu>
```

> [!NOTE]
> <span data-ttu-id="65a8f-432">Não `global::` há suporte para a qualificação.</span><span class="sxs-lookup"><span data-stu-id="65a8f-432">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="65a8f-433">Não há suporte para a `using` importação de componentes com instruções `@using Foo = Bar`com alias (por exemplo,).</span><span class="sxs-lookup"><span data-stu-id="65a8f-433">Importing components with aliased `using` statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="65a8f-434">Não há suporte para nomes parcialmente qualificados.</span><span class="sxs-lookup"><span data-stu-id="65a8f-434">Partially qualified names aren't supported.</span></span> <span data-ttu-id="65a8f-435">Por exemplo, não `@using ComponentsSample` há suporte `NavMenu.razor` para `<Shared.NavMenu></Shared.NavMenu>` adicionar e referenciar com.</span><span class="sxs-lookup"><span data-stu-id="65a8f-435">For example, adding `@using ComponentsSample` and referencing `NavMenu.razor` with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="65a8f-436">Atributos de elemento HTML condicional</span><span class="sxs-lookup"><span data-stu-id="65a8f-436">Conditional HTML element attributes</span></span>

<span data-ttu-id="65a8f-437">Os atributos de elemento HTML são processados condicionalmente com base no valor do .NET.</span><span class="sxs-lookup"><span data-stu-id="65a8f-437">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="65a8f-438">Se o valor for `false` ou `null`, o atributo não será renderizado.</span><span class="sxs-lookup"><span data-stu-id="65a8f-438">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="65a8f-439">Se o valor é `true`, o atributo é processado minimizado.</span><span class="sxs-lookup"><span data-stu-id="65a8f-439">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="65a8f-440">No exemplo a seguir, `IsCompleted` determina se `checked` é renderizado na marcação do elemento:</span><span class="sxs-lookup"><span data-stu-id="65a8f-440">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```cshtml
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="65a8f-441">Se `IsCompleted` for`true`, a caixa de seleção será renderizada como:</span><span class="sxs-lookup"><span data-stu-id="65a8f-441">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="65a8f-442">Se `IsCompleted` for`false`, a caixa de seleção será renderizada como:</span><span class="sxs-lookup"><span data-stu-id="65a8f-442">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="65a8f-443">Para obter mais informações, consulte <xref:mvc/views/razor>.</span><span class="sxs-lookup"><span data-stu-id="65a8f-443">For more information, see <xref:mvc/views/razor>.</span></span>

## <a name="raw-html"></a><span data-ttu-id="65a8f-444">HTML bruto</span><span class="sxs-lookup"><span data-stu-id="65a8f-444">Raw HTML</span></span>

<span data-ttu-id="65a8f-445">Normalmente, as cadeias de caracteres são renderizadas usando nós de texto DOM, o que significa que qualquer marcação que ela possa conter será ignorada e tratada como texto literal.</span><span class="sxs-lookup"><span data-stu-id="65a8f-445">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="65a8f-446">Para renderizar HTML bruto, empacote o conteúdo HTML em `MarkupString` um valor.</span><span class="sxs-lookup"><span data-stu-id="65a8f-446">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="65a8f-447">O valor é analisado como HTML ou SVG e inserido no DOM.</span><span class="sxs-lookup"><span data-stu-id="65a8f-447">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="65a8f-448">O processamento de HTML bruto construído a partir de qualquer fonte não confiável é um **risco à segurança** e deve ser evitado!</span><span class="sxs-lookup"><span data-stu-id="65a8f-448">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="65a8f-449">O exemplo a seguir mostra como `MarkupString` usar o tipo para adicionar um bloco de conteúdo HTML estático à saída renderizada de um componente:</span><span class="sxs-lookup"><span data-stu-id="65a8f-449">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="templated-components"></a><span data-ttu-id="65a8f-450">Componentes de modelo</span><span class="sxs-lookup"><span data-stu-id="65a8f-450">Templated components</span></span>

<span data-ttu-id="65a8f-451">Componentes modelo são componentes que aceitam um ou mais modelos de interface do usuário como parâmetros, que podem ser usados como parte da lógica de renderização do componente.</span><span class="sxs-lookup"><span data-stu-id="65a8f-451">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="65a8f-452">Os componentes de modelo permitem que você crie componentes de nível superior que são mais reutilizáveis do que os componentes normais.</span><span class="sxs-lookup"><span data-stu-id="65a8f-452">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="65a8f-453">Alguns exemplos incluem:</span><span class="sxs-lookup"><span data-stu-id="65a8f-453">A couple of examples include:</span></span>

* <span data-ttu-id="65a8f-454">Um componente de tabela que permite que um usuário especifique modelos para o cabeçalho, as linhas e o rodapé da tabela.</span><span class="sxs-lookup"><span data-stu-id="65a8f-454">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="65a8f-455">Um componente de lista que permite que um usuário especifique um modelo para renderizar itens em uma lista.</span><span class="sxs-lookup"><span data-stu-id="65a8f-455">A list component that allows a user to specify a template for rendering items in a list.</span></span>

### <a name="template-parameters"></a><span data-ttu-id="65a8f-456">Parâmetros do modelo</span><span class="sxs-lookup"><span data-stu-id="65a8f-456">Template parameters</span></span>

<span data-ttu-id="65a8f-457">Um componente modelo é definido especificando um ou mais parâmetros de componente do tipo `RenderFragment` ou. `RenderFragment<T>`</span><span class="sxs-lookup"><span data-stu-id="65a8f-457">A templated component is defined by specifying one or more component parameters of type `RenderFragment` or `RenderFragment<T>`.</span></span> <span data-ttu-id="65a8f-458">Um fragmento de renderização representa um segmento de interface do usuário a ser renderizado.</span><span class="sxs-lookup"><span data-stu-id="65a8f-458">A render fragment represents a segment of UI to render.</span></span> <span data-ttu-id="65a8f-459">`RenderFragment<T>`usa um parâmetro de tipo que pode ser especificado quando o fragmento de renderização é invocado.</span><span class="sxs-lookup"><span data-stu-id="65a8f-459">`RenderFragment<T>` takes a type parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="65a8f-460">`TableTemplate`componente</span><span class="sxs-lookup"><span data-stu-id="65a8f-460">`TableTemplate` component:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TableTemplate.razor)]

<span data-ttu-id="65a8f-461">Ao usar um componente modelo, os parâmetros do modelo podem ser especificados usando elementos filho que correspondem aos nomes dos parâmetros (`TableHeader` e `RowTemplate` no exemplo a seguir):</span><span class="sxs-lookup"><span data-stu-id="65a8f-461">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

```cshtml
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

### <a name="template-context-parameters"></a><span data-ttu-id="65a8f-462">Parâmetros de contexto de modelo</span><span class="sxs-lookup"><span data-stu-id="65a8f-462">Template context parameters</span></span>

<span data-ttu-id="65a8f-463">Argumentos de componente do `RenderFragment<T>` tipo passado como elementos têm um parâmetro implícito `context` chamado (por exemplo, `@context.PetId`do exemplo de código anterior), mas você pode alterar o nome do parâmetro `Context` usando o atributo no filho elementos.</span><span class="sxs-lookup"><span data-stu-id="65a8f-463">Component arguments of type `RenderFragment<T>` passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="65a8f-464">No exemplo a seguir, o `RowTemplate` atributo do `Context` elemento Especifica o `pet` parâmetro:</span><span class="sxs-lookup"><span data-stu-id="65a8f-464">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

```cshtml
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

<span data-ttu-id="65a8f-465">Como alternativa, você pode especificar o `Context` atributo no elemento Component.</span><span class="sxs-lookup"><span data-stu-id="65a8f-465">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="65a8f-466">O atributo `Context` especificado se aplica a todos os parâmetros de modelo especificados.</span><span class="sxs-lookup"><span data-stu-id="65a8f-466">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="65a8f-467">Isso pode ser útil quando você deseja especificar o nome do parâmetro de conteúdo para conteúdo filho implícito (sem qualquer elemento filho de disposição).</span><span class="sxs-lookup"><span data-stu-id="65a8f-467">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="65a8f-468">No exemplo a seguir, o `Context` atributo aparece `TableTemplate` no elemento e se aplica a todos os parâmetros de modelo:</span><span class="sxs-lookup"><span data-stu-id="65a8f-468">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

```cshtml
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

### <a name="generic-typed-components"></a><span data-ttu-id="65a8f-469">Componentes de tipo genérico</span><span class="sxs-lookup"><span data-stu-id="65a8f-469">Generic-typed components</span></span>

<span data-ttu-id="65a8f-470">Os componentes modelo são geralmente digitados genericamente.</span><span class="sxs-lookup"><span data-stu-id="65a8f-470">Templated components are often generically typed.</span></span> <span data-ttu-id="65a8f-471">Por exemplo, um componente `ListViewTemplate` genérico pode ser usado para renderizar `IEnumerable<T>` valores.</span><span class="sxs-lookup"><span data-stu-id="65a8f-471">For example, a generic `ListViewTemplate` component can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="65a8f-472">Para definir um componente genérico, use a `@typeparam` diretiva para especificar parâmetros de tipo:</span><span class="sxs-lookup"><span data-stu-id="65a8f-472">To define a generic component, use the `@typeparam` directive to specify type parameters:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ListViewTemplate.razor)]

<span data-ttu-id="65a8f-473">Ao usar componentes de tipos genéricos, o parâmetro de tipo é inferido, se possível:</span><span class="sxs-lookup"><span data-stu-id="65a8f-473">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```cshtml
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

<span data-ttu-id="65a8f-474">Caso contrário, o parâmetro de tipo deve ser especificado explicitamente usando um atributo que corresponda ao nome do parâmetro de tipo.</span><span class="sxs-lookup"><span data-stu-id="65a8f-474">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="65a8f-475">No exemplo a seguir, `TItem="Pet"` especifica o tipo:</span><span class="sxs-lookup"><span data-stu-id="65a8f-475">In the following example, `TItem="Pet"` specifies the type:</span></span>

```cshtml
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="65a8f-476">Valores e parâmetros em cascata</span><span class="sxs-lookup"><span data-stu-id="65a8f-476">Cascading values and parameters</span></span>

<span data-ttu-id="65a8f-477">Em alguns cenários, é inconveniente fluir dados de um componente ancestral para um componente descendente usando [parâmetros de componente](#component-parameters), especialmente quando há várias camadas de componente.</span><span class="sxs-lookup"><span data-stu-id="65a8f-477">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="65a8f-478">Valores e parâmetros em cascata resolvem esse problema fornecendo uma maneira conveniente para um componente ancestral fornecer um valor para todos os seus componentes descendentes.</span><span class="sxs-lookup"><span data-stu-id="65a8f-478">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="65a8f-479">Valores e parâmetros em cascata também fornecem uma abordagem para que os componentes sejam coordenados.</span><span class="sxs-lookup"><span data-stu-id="65a8f-479">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="65a8f-480">Exemplo de tema</span><span class="sxs-lookup"><span data-stu-id="65a8f-480">Theme example</span></span>

<span data-ttu-id="65a8f-481">No exemplo a seguir do aplicativo de exemplo, a `ThemeInfo` classe especifica as informações do tema para fluir para baixo na hierarquia do componente para que todos os botões de uma determinada parte do aplicativo compartilhem o mesmo estilo.</span><span class="sxs-lookup"><span data-stu-id="65a8f-481">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="65a8f-482">*UIThemeClasses/ThemeInfo.cs*:</span><span class="sxs-lookup"><span data-stu-id="65a8f-482">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="65a8f-483">Um componente ancestral pode fornecer um valor em cascata usando o componente de valor em cascata.</span><span class="sxs-lookup"><span data-stu-id="65a8f-483">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="65a8f-484">O `CascadingValue` componente encapsula uma subárvore da hierarquia do componente e fornece um único valor para todos os componentes dentro dessa subárvore.</span><span class="sxs-lookup"><span data-stu-id="65a8f-484">The `CascadingValue` component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="65a8f-485">Por exemplo, o aplicativo de exemplo especifica informações de`ThemeInfo`tema () em um dos layouts do aplicativo como um parâmetro em cascata para todos os componentes que compõem o corpo `@Body` do layout da propriedade.</span><span class="sxs-lookup"><span data-stu-id="65a8f-485">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="65a8f-486">`ButtonClass`é atribuído um valor de `btn-success` no componente layout.</span><span class="sxs-lookup"><span data-stu-id="65a8f-486">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="65a8f-487">Qualquer componente descendente pode consumir essa propriedade por meio `ThemeInfo` do objeto em cascata.</span><span class="sxs-lookup"><span data-stu-id="65a8f-487">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="65a8f-488">`CascadingValuesParametersLayout`componente</span><span class="sxs-lookup"><span data-stu-id="65a8f-488">`CascadingValuesParametersLayout` component:</span></span>

```cshtml
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

<span data-ttu-id="65a8f-489">Para fazer uso de valores em cascata, os componentes declaram parâmetros em `[CascadingParameter]` cascata usando o atributo.</span><span class="sxs-lookup"><span data-stu-id="65a8f-489">To make use of cascading values, components declare cascading parameters using the `[CascadingParameter]` attribute.</span></span> <span data-ttu-id="65a8f-490">Os valores em cascata são associados a parâmetros em cascata por tipo.</span><span class="sxs-lookup"><span data-stu-id="65a8f-490">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="65a8f-491">No aplicativo de exemplo, o `CascadingValuesParametersTheme` componente associa o `ThemeInfo` valor em cascata a um parâmetro em cascata.</span><span class="sxs-lookup"><span data-stu-id="65a8f-491">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="65a8f-492">O parâmetro é usado para definir a classe CSS para um dos botões exibidos pelo componente.</span><span class="sxs-lookup"><span data-stu-id="65a8f-492">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="65a8f-493">`CascadingValuesParametersTheme`componente</span><span class="sxs-lookup"><span data-stu-id="65a8f-493">`CascadingValuesParametersTheme` component:</span></span>

```cshtml
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

<span data-ttu-id="65a8f-494">Para propagar vários valores do mesmo tipo dentro da mesma subárvore, forneça uma cadeia `Name` de caracteres exclusiva `CascadingValue` para cada componente e `CascadingParameter`seu correspondente.</span><span class="sxs-lookup"><span data-stu-id="65a8f-494">To cascade multiple values of the same type within the same subtree, provide a unique `Name` string to each `CascadingValue` component and its corresponding `CascadingParameter`.</span></span> <span data-ttu-id="65a8f-495">No exemplo a seguir, dois `CascadingValue` componentes em cascata diferentes instâncias de `MyCascadingType` por nome:</span><span class="sxs-lookup"><span data-stu-id="65a8f-495">In the following example, two `CascadingValue` components cascade different instances of `MyCascadingType` by name:</span></span>

```cshtml
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

<span data-ttu-id="65a8f-496">Em um componente descendente, os parâmetros em cascata recebem seus valores dos valores em cascata correspondentes no componente ancestral por nome:</span><span class="sxs-lookup"><span data-stu-id="65a8f-496">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```cshtml
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="65a8f-497">Exemplo de TabSet</span><span class="sxs-lookup"><span data-stu-id="65a8f-497">TabSet example</span></span>

<span data-ttu-id="65a8f-498">Os parâmetros em cascata também permitem que os componentes colaborem na hierarquia do componente.</span><span class="sxs-lookup"><span data-stu-id="65a8f-498">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="65a8f-499">Por exemplo, considere o exemplo de *TabSet* a seguir no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="65a8f-499">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="65a8f-500">O aplicativo de exemplo tem `ITab` uma interface que implementa as guias:</span><span class="sxs-lookup"><span data-stu-id="65a8f-500">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](common/samples/3.x/BlazorSample/UIInterfaces/ITab.cs)]

<span data-ttu-id="65a8f-501">O `CascadingValuesParametersTabSet` componente usa o `TabSet` componente, que contém vários `Tab` componentes:</span><span class="sxs-lookup"><span data-stu-id="65a8f-501">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/CascadingValuesParametersTabSet.razor?name=snippet_TabSet)]

<span data-ttu-id="65a8f-502">Os componentes `Tab` filho não são passados explicitamente como parâmetros `TabSet`para.</span><span class="sxs-lookup"><span data-stu-id="65a8f-502">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="65a8f-503">Em vez disso, `Tab` os componentes filho fazem parte do conteúdo filho `TabSet`do.</span><span class="sxs-lookup"><span data-stu-id="65a8f-503">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="65a8f-504">No entanto `TabSet` , o ainda precisa saber sobre `Tab` cada componente para que ele possa renderizar os cabeçalhos e a guia ativa. Para habilitar essa coordenação sem a necessidade de código adicional, `TabSet` o componente *pode fornecer a si mesmo como um valor em cascata* que é então coletado `Tab` pelos componentes descendentes.</span><span class="sxs-lookup"><span data-stu-id="65a8f-504">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="65a8f-505">`TabSet`componente</span><span class="sxs-lookup"><span data-stu-id="65a8f-505">`TabSet` component:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TabSet.razor)]

<span data-ttu-id="65a8f-506">Os componentes `Tab` descendentes capturam `TabSet` o que contém como um parâmetro em cascata `Tab` , de modo que os `TabSet` componentes se adicionam à coordenada e na qual a guia está ativa.</span><span class="sxs-lookup"><span data-stu-id="65a8f-506">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="65a8f-507">`Tab`componente</span><span class="sxs-lookup"><span data-stu-id="65a8f-507">`Tab` component:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/Tab.razor)]

## <a name="razor-templates"></a><span data-ttu-id="65a8f-508">Modelos do Razor</span><span class="sxs-lookup"><span data-stu-id="65a8f-508">Razor templates</span></span>

<span data-ttu-id="65a8f-509">Os fragmentos de renderização podem ser definidos usando a sintaxe de modelo Razor.</span><span class="sxs-lookup"><span data-stu-id="65a8f-509">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="65a8f-510">Os modelos Razor são uma maneira de definir um trecho de interface do usuário e assumir o seguinte formato:</span><span class="sxs-lookup"><span data-stu-id="65a8f-510">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```cshtml
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="65a8f-511">O exemplo a seguir ilustra como especificar `RenderFragment` e `RenderFragment<T>` valores e renderizar modelos diretamente em um componente.</span><span class="sxs-lookup"><span data-stu-id="65a8f-511">The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>` values and render templates directly in a component.</span></span> <span data-ttu-id="65a8f-512">Os fragmentos de renderização também podem ser passados como argumentos para [componentes de modelo](#templated-components).</span><span class="sxs-lookup"><span data-stu-id="65a8f-512">Render fragments can also be passed as arguments to [templated components](#templated-components).</span></span>

```cshtml
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

<span data-ttu-id="65a8f-513">Saída renderizada do código anterior:</span><span class="sxs-lookup"><span data-stu-id="65a8f-513">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Your pet's name is Rex.</p>
```

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="65a8f-514">Lógica RenderTreeBuilder manual</span><span class="sxs-lookup"><span data-stu-id="65a8f-514">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="65a8f-515">`Microsoft.AspNetCore.Components.RenderTree`fornece métodos para manipular componentes e elementos, incluindo a criação manual de componentes C# no código.</span><span class="sxs-lookup"><span data-stu-id="65a8f-515">`Microsoft.AspNetCore.Components.RenderTree` provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="65a8f-516">O uso `RenderTreeBuilder` do para criar componentes é um cenário avançado.</span><span class="sxs-lookup"><span data-stu-id="65a8f-516">Use of `RenderTreeBuilder` to create components is an advanced scenario.</span></span> <span data-ttu-id="65a8f-517">Um componente malformado (por exemplo, uma marca de marcação não fechada) pode resultar em um comportamento indefinido.</span><span class="sxs-lookup"><span data-stu-id="65a8f-517">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="65a8f-518">Considere o seguinte `PetDetails` componente, que pode ser compilado manualmente em outro componente:</span><span class="sxs-lookup"><span data-stu-id="65a8f-518">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```cshtml
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="65a8f-519">No exemplo a seguir, o loop no `CreateComponent` método gera três `PetDetails` componentes.</span><span class="sxs-lookup"><span data-stu-id="65a8f-519">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="65a8f-520">Ao chamar `RenderTreeBuilder` métodos para criar os componentes (`OpenComponent` e `AddAttribute`), os números de sequência são números de linha de código-fonte.</span><span class="sxs-lookup"><span data-stu-id="65a8f-520">When calling `RenderTreeBuilder` methods to create the components (`OpenComponent` and `AddAttribute`), sequence numbers are source code line numbers.</span></span> <span data-ttu-id="65a8f-521">O algoritmo de diferença mais grande do que se baseia nos números de sequência correspondentes a linhas distintas de código, não a invocações de chamada distintas.</span><span class="sxs-lookup"><span data-stu-id="65a8f-521">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="65a8f-522">Ao criar um componente com `RenderTreeBuilder` métodos, codifique os argumentos para números de sequência.</span><span class="sxs-lookup"><span data-stu-id="65a8f-522">When creating a component with `RenderTreeBuilder` methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="65a8f-523">**O uso de um cálculo ou contador para gerar o número de sequência pode levar a um desempenho insatisfatório.**</span><span class="sxs-lookup"><span data-stu-id="65a8f-523">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="65a8f-524">Para obter mais informações, consulte os [números de sequência relacionados à seção números de linha de código e não ordem de execução](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) .</span><span class="sxs-lookup"><span data-stu-id="65a8f-524">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="65a8f-525">`BuiltContent`componente</span><span class="sxs-lookup"><span data-stu-id="65a8f-525">`BuiltContent` component:</span></span>

```cshtml
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

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="65a8f-526">Números de sequência se relacionam a números de linha de código e não a ordem de execução</span><span class="sxs-lookup"><span data-stu-id="65a8f-526">Sequence numbers relate to code line numbers and not execution order</span></span>

<span data-ttu-id="65a8f-527">`.razor` Arquivos mais poseriais são sempre compilados.</span><span class="sxs-lookup"><span data-stu-id="65a8f-527">Blazor `.razor` files are always compiled.</span></span> <span data-ttu-id="65a8f-528">Isso é potencialmente uma grande vantagem para `.razor` o porque a etapa de compilação pode ser usada para injetar informações que melhoram o desempenho do aplicativo em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="65a8f-528">This is potentially a great advantage for `.razor` because the compile step can be used to inject information that improve app performance at runtime.</span></span>

<span data-ttu-id="65a8f-529">Um exemplo importante desses aprimoramentos envolve *números de sequência*.</span><span class="sxs-lookup"><span data-stu-id="65a8f-529">A key example of these improvements involve *sequence numbers*.</span></span> <span data-ttu-id="65a8f-530">Os números de sequência indicam ao tempo de execução que as saídas vieram de quais linhas de código distintas e ordenadas.</span><span class="sxs-lookup"><span data-stu-id="65a8f-530">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="65a8f-531">O tempo de execução usa essas informações para gerar comparações de árvore eficientes em tempo linear, o que é muito mais rápido do que normalmente é possível para um algoritmo de comparação de árvore geral.</span><span class="sxs-lookup"><span data-stu-id="65a8f-531">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="65a8f-532">Considere o seguinte arquivo `.razor` simples:</span><span class="sxs-lookup"><span data-stu-id="65a8f-532">Consider the following simple `.razor` file:</span></span>

```cshtml
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="65a8f-533">O código anterior é compilado para algo semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="65a8f-533">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="65a8f-534">Quando o código é executado pela primeira vez, se `someFlag` for `true`, o Construtor receberá:</span><span class="sxs-lookup"><span data-stu-id="65a8f-534">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="65a8f-535">Sequência</span><span class="sxs-lookup"><span data-stu-id="65a8f-535">Sequence</span></span> | <span data-ttu-id="65a8f-536">Tipo</span><span class="sxs-lookup"><span data-stu-id="65a8f-536">Type</span></span>      | <span data-ttu-id="65a8f-537">Dados</span><span class="sxs-lookup"><span data-stu-id="65a8f-537">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="65a8f-538">0</span><span class="sxs-lookup"><span data-stu-id="65a8f-538">0</span></span>        | <span data-ttu-id="65a8f-539">Nó de texto</span><span class="sxs-lookup"><span data-stu-id="65a8f-539">Text node</span></span> | <span data-ttu-id="65a8f-540">First</span><span class="sxs-lookup"><span data-stu-id="65a8f-540">First</span></span>  |
| <span data-ttu-id="65a8f-541">1</span><span class="sxs-lookup"><span data-stu-id="65a8f-541">1</span></span>        | <span data-ttu-id="65a8f-542">Nó de texto</span><span class="sxs-lookup"><span data-stu-id="65a8f-542">Text node</span></span> | <span data-ttu-id="65a8f-543">Segundo</span><span class="sxs-lookup"><span data-stu-id="65a8f-543">Second</span></span> |

<span data-ttu-id="65a8f-544">Imagine que `someFlag` se `false`torna e a marcação é renderizada novamente.</span><span class="sxs-lookup"><span data-stu-id="65a8f-544">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="65a8f-545">Desta vez, o Construtor recebe:</span><span class="sxs-lookup"><span data-stu-id="65a8f-545">This time, the builder receives:</span></span>

| <span data-ttu-id="65a8f-546">Sequência</span><span class="sxs-lookup"><span data-stu-id="65a8f-546">Sequence</span></span> | <span data-ttu-id="65a8f-547">Tipo</span><span class="sxs-lookup"><span data-stu-id="65a8f-547">Type</span></span>       | <span data-ttu-id="65a8f-548">Dados</span><span class="sxs-lookup"><span data-stu-id="65a8f-548">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="65a8f-549">1</span><span class="sxs-lookup"><span data-stu-id="65a8f-549">1</span></span>        | <span data-ttu-id="65a8f-550">Nó de texto</span><span class="sxs-lookup"><span data-stu-id="65a8f-550">Text node</span></span>  | <span data-ttu-id="65a8f-551">Segundo</span><span class="sxs-lookup"><span data-stu-id="65a8f-551">Second</span></span> |

<span data-ttu-id="65a8f-552">Quando o tempo de execução executa uma comparação, ele vê que o item `0` na sequência foi removido e, portanto, gera o seguinte *script de edição*trivial:</span><span class="sxs-lookup"><span data-stu-id="65a8f-552">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script*:</span></span>

* <span data-ttu-id="65a8f-553">Remova o primeiro nó de texto.</span><span class="sxs-lookup"><span data-stu-id="65a8f-553">Remove the first text node.</span></span>

#### <a name="what-goes-wrong-if-you-generate-sequence-numbers-programmatically"></a><span data-ttu-id="65a8f-554">O que vai errado se você gerar números de sequência programaticamente</span><span class="sxs-lookup"><span data-stu-id="65a8f-554">What goes wrong if you generate sequence numbers programmatically</span></span>

<span data-ttu-id="65a8f-555">Imagine, em vez disso, que você escreveu a seguinte lógica do construtor de árvore de renderização:</span><span class="sxs-lookup"><span data-stu-id="65a8f-555">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="65a8f-556">Agora, a primeira saída é:</span><span class="sxs-lookup"><span data-stu-id="65a8f-556">Now, the first output is:</span></span>

| <span data-ttu-id="65a8f-557">Sequência</span><span class="sxs-lookup"><span data-stu-id="65a8f-557">Sequence</span></span> | <span data-ttu-id="65a8f-558">Tipo</span><span class="sxs-lookup"><span data-stu-id="65a8f-558">Type</span></span>      | <span data-ttu-id="65a8f-559">Dados</span><span class="sxs-lookup"><span data-stu-id="65a8f-559">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="65a8f-560">0</span><span class="sxs-lookup"><span data-stu-id="65a8f-560">0</span></span>        | <span data-ttu-id="65a8f-561">Nó de texto</span><span class="sxs-lookup"><span data-stu-id="65a8f-561">Text node</span></span> | <span data-ttu-id="65a8f-562">First</span><span class="sxs-lookup"><span data-stu-id="65a8f-562">First</span></span>  |
| <span data-ttu-id="65a8f-563">1</span><span class="sxs-lookup"><span data-stu-id="65a8f-563">1</span></span>        | <span data-ttu-id="65a8f-564">Nó de texto</span><span class="sxs-lookup"><span data-stu-id="65a8f-564">Text node</span></span> | <span data-ttu-id="65a8f-565">Segundo</span><span class="sxs-lookup"><span data-stu-id="65a8f-565">Second</span></span> |

<span data-ttu-id="65a8f-566">Esse resultado é idêntico ao caso anterior, portanto, não existem problemas negativos.</span><span class="sxs-lookup"><span data-stu-id="65a8f-566">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="65a8f-567">`someFlag`está `false` no segundo processamento e a saída é:</span><span class="sxs-lookup"><span data-stu-id="65a8f-567">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="65a8f-568">Sequência</span><span class="sxs-lookup"><span data-stu-id="65a8f-568">Sequence</span></span> | <span data-ttu-id="65a8f-569">Tipo</span><span class="sxs-lookup"><span data-stu-id="65a8f-569">Type</span></span>      | <span data-ttu-id="65a8f-570">Dados</span><span class="sxs-lookup"><span data-stu-id="65a8f-570">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="65a8f-571">0</span><span class="sxs-lookup"><span data-stu-id="65a8f-571">0</span></span>        | <span data-ttu-id="65a8f-572">Nó de texto</span><span class="sxs-lookup"><span data-stu-id="65a8f-572">Text node</span></span> | <span data-ttu-id="65a8f-573">Segundo</span><span class="sxs-lookup"><span data-stu-id="65a8f-573">Second</span></span> |

<span data-ttu-id="65a8f-574">Desta vez, o algoritmo diff vê que *duas* alterações ocorreram e o algoritmo gera o seguinte script de edição:</span><span class="sxs-lookup"><span data-stu-id="65a8f-574">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="65a8f-575">Altere o valor do primeiro nó de texto para `Second`.</span><span class="sxs-lookup"><span data-stu-id="65a8f-575">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="65a8f-576">Remova o segundo nó de texto.</span><span class="sxs-lookup"><span data-stu-id="65a8f-576">Remove the second text node.</span></span>

<span data-ttu-id="65a8f-577">A geração de números de sequência perdeu todas as informações úteis sobre onde `if/else` os branches e loops estavam presentes no código original.</span><span class="sxs-lookup"><span data-stu-id="65a8f-577">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="65a8f-578">Isso resulta em uma comparação **duas vezes mais longa** do que antes.</span><span class="sxs-lookup"><span data-stu-id="65a8f-578">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="65a8f-579">Esse é um exemplo trivial.</span><span class="sxs-lookup"><span data-stu-id="65a8f-579">This is a trivial example.</span></span> <span data-ttu-id="65a8f-580">Em casos mais realistas com estruturas complexas e profundamente aninhadas, e especialmente com loops, o custo de desempenho é mais grave.</span><span class="sxs-lookup"><span data-stu-id="65a8f-580">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is more severe.</span></span> <span data-ttu-id="65a8f-581">Em vez de identificar imediatamente quais blocos de loop ou ramificações foram inseridos ou removidos, o algoritmo diff precisa recorrer profundamente nas árvores de renderização e geralmente criar scripts de edição muito mais, pois ele é informado indiretamente sobre como as estruturas antigas e novas relacionar entre si.</span><span class="sxs-lookup"><span data-stu-id="65a8f-581">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees and usually build far longer edit scripts because it is misinformed about how the old and new structures relate to each other.</span></span>

#### <a name="guidance-and-conclusions"></a><span data-ttu-id="65a8f-582">Diretrizes e conclusões</span><span class="sxs-lookup"><span data-stu-id="65a8f-582">Guidance and conclusions</span></span>

* <span data-ttu-id="65a8f-583">O desempenho do aplicativo será afetado se os números de sequência forem gerados dinamicamente.</span><span class="sxs-lookup"><span data-stu-id="65a8f-583">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="65a8f-584">A estrutura não pode criar seus próprios números de sequência automaticamente em tempo de execução porque as informações necessárias não existem, a menos que sejam capturadas no momento da compilação.</span><span class="sxs-lookup"><span data-stu-id="65a8f-584">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="65a8f-585">Não grave blocos longos de lógica implementada `RenderTreeBuilder` manualmente.</span><span class="sxs-lookup"><span data-stu-id="65a8f-585">Don't write long blocks of manually-implemented `RenderTreeBuilder` logic.</span></span> <span data-ttu-id="65a8f-586">Prefira `.razor` arquivos e permita que o compilador lide com os números de sequência.</span><span class="sxs-lookup"><span data-stu-id="65a8f-586">Prefer `.razor` files and allow the compiler to deal with the sequence numbers.</span></span>
* <span data-ttu-id="65a8f-587">Se os números de sequência forem codificados, o algoritmo diff só exigirá que os números de sequência aumentem de valor.</span><span class="sxs-lookup"><span data-stu-id="65a8f-587">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="65a8f-588">O valor inicial e as lacunas são irrelevantes.</span><span class="sxs-lookup"><span data-stu-id="65a8f-588">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="65a8f-589">Uma opção legítima é usar o número de linha de código como o número de sequência, ou começar de zero e aumentar por um ou centenas (ou qualquer intervalo preferencial).</span><span class="sxs-lookup"><span data-stu-id="65a8f-589">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* <span data-ttu-id="65a8f-590">O mais alto número de seqüências usa números de sequência, enquanto outras estruturas de interface do usuário de diferenciação de árvore não as usam.</span><span class="sxs-lookup"><span data-stu-id="65a8f-590">Blazor uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="65a8f-591">A comparação é muito mais rápida quando os números de sequência são usados, e o mais vantajoso tem a vantagem de uma etapa de compilação que lida com números de `.razor` sequência automaticamente para desenvolvedores que criam arquivos.</span><span class="sxs-lookup"><span data-stu-id="65a8f-591">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring `.razor` files.</span></span>

## <a name="localization"></a><span data-ttu-id="65a8f-592">Localização</span><span class="sxs-lookup"><span data-stu-id="65a8f-592">Localization</span></span>

<span data-ttu-id="65a8f-593">Os aplicativos de servidor mais incrivelmente são localizados usando o [middleware de localização](xref:fundamentals/localization#localization-middleware).</span><span class="sxs-lookup"><span data-stu-id="65a8f-593">Blazor Server apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="65a8f-594">O middleware seleciona a cultura apropriada para os usuários que solicitam recursos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="65a8f-594">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="65a8f-595">A cultura pode ser definida usando uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="65a8f-595">The culture can be set using one of the following approaches:</span></span>

* [<span data-ttu-id="65a8f-596">Cookies</span><span class="sxs-lookup"><span data-stu-id="65a8f-596">Cookies</span></span>](#cookies)
* [<span data-ttu-id="65a8f-597">Fornecer interface do usuário para escolher a cultura</span><span class="sxs-lookup"><span data-stu-id="65a8f-597">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="65a8f-598">Para obter mais informações e exemplos, consulte <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="65a8f-598">For more information and examples, see <xref:fundamentals/localization>.</span></span>

### <a name="cookies"></a><span data-ttu-id="65a8f-599">Cookies</span><span class="sxs-lookup"><span data-stu-id="65a8f-599">Cookies</span></span>

<span data-ttu-id="65a8f-600">Um cookie de cultura de localização pode persistir a cultura do usuário.</span><span class="sxs-lookup"><span data-stu-id="65a8f-600">A localization culture cookie can persist the user's culture.</span></span> <span data-ttu-id="65a8f-601">O cookie é criado pelo `OnGet` método da página host do aplicativo (*pages/host. cshtml. cs*).</span><span class="sxs-lookup"><span data-stu-id="65a8f-601">The cookie is created by the `OnGet` method of the app's host page (*Pages/Host.cshtml.cs*).</span></span> <span data-ttu-id="65a8f-602">O middleware de localização lê o cookie em solicitações subsequentes para definir a cultura do usuário.</span><span class="sxs-lookup"><span data-stu-id="65a8f-602">The Localization Middleware reads the cookie on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="65a8f-603">O uso de um cookie garante que a conexão WebSocket possa propagar corretamente a cultura.</span><span class="sxs-lookup"><span data-stu-id="65a8f-603">Use of a cookie ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="65a8f-604">Se os esquemas de localização forem baseados no caminho da URL ou na cadeia de caracteres de consulta, o esquema pode não ser capaz de trabalhar com WebSockets, portanto, falha ao persistir a cultura.</span><span class="sxs-lookup"><span data-stu-id="65a8f-604">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="65a8f-605">Portanto, o uso de um cookie de cultura de localização é a abordagem recomendada.</span><span class="sxs-lookup"><span data-stu-id="65a8f-605">Therefore, use of a localization culture cookie is the recommended approach.</span></span>

<span data-ttu-id="65a8f-606">Qualquer técnica pode ser usada para atribuir uma cultura se a cultura persistir em um cookie de localização.</span><span class="sxs-lookup"><span data-stu-id="65a8f-606">Any technique can be used to assign a culture if the culture is persisted in a localization cookie.</span></span> <span data-ttu-id="65a8f-607">Se o aplicativo já tiver um esquema de localização estabelecido para ASP.NET Core do lado do servidor, continue a usar a infraestrutura de localização existente do aplicativo e defina o cookie de cultura de localização no esquema do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="65a8f-607">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture cookie within the app's scheme.</span></span>

<span data-ttu-id="65a8f-608">O exemplo a seguir mostra como definir a cultura atual em um cookie que pode ser lido pelo middleware de localização.</span><span class="sxs-lookup"><span data-stu-id="65a8f-608">The following example shows how to set the current culture in a cookie that can be read by the Localization Middleware.</span></span> <span data-ttu-id="65a8f-609">Crie um arquivo *pages/host. cshtml. cs* com o seguinte conteúdo no aplicativo de servidor mais incrivelmente:</span><span class="sxs-lookup"><span data-stu-id="65a8f-609">Create a *Pages/Host.cshtml.cs* file with the following contents in the Blazor Server app:</span></span>

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

<span data-ttu-id="65a8f-610">A localização é manipulada no aplicativo:</span><span class="sxs-lookup"><span data-stu-id="65a8f-610">Localization is handled in the app:</span></span>

1. <span data-ttu-id="65a8f-611">O navegador envia uma solicitação HTTP inicial para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="65a8f-611">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="65a8f-612">A cultura é atribuída pelo middleware de localização.</span><span class="sxs-lookup"><span data-stu-id="65a8f-612">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="65a8f-613">O `OnGet` método em *_Host. cshtml. cs* persiste a cultura em um cookie como parte da resposta.</span><span class="sxs-lookup"><span data-stu-id="65a8f-613">The `OnGet` method in *_Host.cshtml.cs* persists the culture in a cookie as part of the response.</span></span>
1. <span data-ttu-id="65a8f-614">O navegador abre uma conexão WebSocket para criar uma sessão de servidor mais incrivelmente interativa.</span><span class="sxs-lookup"><span data-stu-id="65a8f-614">The browser opens a WebSocket connection to create an interactive Blazor Server session.</span></span>
1. <span data-ttu-id="65a8f-615">O middleware de localização lê o cookie e atribui a cultura.</span><span class="sxs-lookup"><span data-stu-id="65a8f-615">The Localization Middleware reads the cookie and assigns the culture.</span></span>
1. <span data-ttu-id="65a8f-616">A sessão de servidor mais incrivelmente começa com a cultura correta.</span><span class="sxs-lookup"><span data-stu-id="65a8f-616">The Blazor Server session begins with the correct culture.</span></span>

## <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="65a8f-617">Fornecer interface do usuário para escolher a cultura</span><span class="sxs-lookup"><span data-stu-id="65a8f-617">Provide UI to choose the culture</span></span>

<span data-ttu-id="65a8f-618">Para fornecer à interface do usuário a fim de permitir a seleção de uma cultura, é recomendável uma *abordagem baseada em redirecionamento* .</span><span class="sxs-lookup"><span data-stu-id="65a8f-618">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="65a8f-619">O processo é semelhante ao que acontece em um aplicativo Web quando um usuário tenta acessar um recurso&mdash;seguro que o usuário é redirecionado para uma página de entrada e, em seguida, Redirecionado de volta para o recurso original.</span><span class="sxs-lookup"><span data-stu-id="65a8f-619">The process is similar to what happens in a web app when a user attempts to access a secure resource&mdash;the user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="65a8f-620">O aplicativo persiste a cultura selecionada do usuário por meio de um redirecionamento para um controlador.</span><span class="sxs-lookup"><span data-stu-id="65a8f-620">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="65a8f-621">O controlador define a cultura selecionada do usuário em um cookie e redireciona o usuário de volta para o URI original.</span><span class="sxs-lookup"><span data-stu-id="65a8f-621">The controller sets the user's selected culture into a cookie and redirects the user back to the original URI.</span></span>

<span data-ttu-id="65a8f-622">Estabeleça um ponto de extremidade HTTP no servidor para definir a cultura selecionada do usuário em um cookie e execute o redirecionamento de volta para o URI original:</span><span class="sxs-lookup"><span data-stu-id="65a8f-622">Establish an HTTP endpoint on the server to set the user's selected culture in a cookie and perform the redirect back to the original URI:</span></span>

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
> <span data-ttu-id="65a8f-623">Use o `LocalRedirect` resultado da ação para evitar ataques de redirecionamento abertos.</span><span class="sxs-lookup"><span data-stu-id="65a8f-623">Use the `LocalRedirect` action result to prevent open redirect attacks.</span></span> <span data-ttu-id="65a8f-624">Para obter mais informações, consulte <xref:security/preventing-open-redirects>.</span><span class="sxs-lookup"><span data-stu-id="65a8f-624">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="65a8f-625">O componente a seguir mostra um exemplo de como executar o redirecionamento inicial quando o usuário seleciona uma cultura:</span><span class="sxs-lookup"><span data-stu-id="65a8f-625">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

```cshtml
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

### <a name="use-net-localization-scenarios-in-blazor-apps"></a><span data-ttu-id="65a8f-626">Usar cenários de localização do .NET em aplicativos mais Incrivelmenteos</span><span class="sxs-lookup"><span data-stu-id="65a8f-626">Use .NET localization scenarios in Blazor apps</span></span>

<span data-ttu-id="65a8f-627">Nos aplicativos mais poseriais, os seguintes cenários de localização e globalização do .NET estão disponíveis:</span><span class="sxs-lookup"><span data-stu-id="65a8f-627">Inside Blazor apps, the following .NET localization and globalization scenarios are available:</span></span>

* <span data-ttu-id="65a8f-628">. Sistema de recursos da rede</span><span class="sxs-lookup"><span data-stu-id="65a8f-628">.NET's resources system</span></span>
* <span data-ttu-id="65a8f-629">Formatação de número e data específicos da cultura</span><span class="sxs-lookup"><span data-stu-id="65a8f-629">Culture-specific number and date formatting</span></span>

<span data-ttu-id="65a8f-630">A funcionalidade de `@bind` mais de uma das mais recentes realiza a globalização com base na cultura atual do usuário.</span><span class="sxs-lookup"><span data-stu-id="65a8f-630">Blazor's `@bind` functionality performs globalization based on the user's current culture.</span></span> <span data-ttu-id="65a8f-631">Para obter mais informações, consulte a seção [ligação de dados](#data-binding) .</span><span class="sxs-lookup"><span data-stu-id="65a8f-631">For more information, see the [Data binding](#data-binding) section.</span></span>

<span data-ttu-id="65a8f-632">No momento, há suporte para um conjunto limitado de cenários de localização de ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="65a8f-632">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="65a8f-633">`IStringLocalizer<>`tem *suporte* em aplicativos mais incrivelmenteos.</span><span class="sxs-lookup"><span data-stu-id="65a8f-633">`IStringLocalizer<>` *is supported* in Blazor apps.</span></span>
* <span data-ttu-id="65a8f-634">`IHtmlLocalizer<>`, `IViewLocalizer<>`, e a localização de anotações de dados são ASP.NET Core cenários MVC e **não têm suporte** em aplicativos mais incrivelmenteos.</span><span class="sxs-lookup"><span data-stu-id="65a8f-634">`IHtmlLocalizer<>`, `IViewLocalizer<>`, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in Blazor apps.</span></span>

<span data-ttu-id="65a8f-635">Para obter mais informações, consulte <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="65a8f-635">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="65a8f-636">Imagens SVG (gráficos vetoriais escaláveis)</span><span class="sxs-lookup"><span data-stu-id="65a8f-636">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="65a8f-637">Como o mais alto renderiza imagens html, com suporte para navegadores, incluindo imagens SVG (gráficos de vetor escalonáveis) ( *. svg*), `<img>` há suporte por meio da marca:</span><span class="sxs-lookup"><span data-stu-id="65a8f-637">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (*.svg*), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="65a8f-638">Da mesma forma, as imagens SVG têm suporte nas regras de CSS de um arquivo de folha de estilos ( *. css*):</span><span class="sxs-lookup"><span data-stu-id="65a8f-638">Similarly, SVG images are supported in the CSS rules of a stylesheet file (*.css*):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="65a8f-639">No entanto, a marcação SVG embutida não tem suporte em todos os cenários.</span><span class="sxs-lookup"><span data-stu-id="65a8f-639">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="65a8f-640">Se você posicionar `<svg>` uma marca diretamente em um arquivo de componente ( *. Razor*), a renderização de imagem básica terá suporte, mas muitos cenários avançados ainda não têm suporte.</span><span class="sxs-lookup"><span data-stu-id="65a8f-640">If you place an `<svg>` tag directly into a component file (*.razor*), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="65a8f-641">Por exemplo, `<use>` as marcas não são respeitadas `@bind` atualmente e não podem ser usadas com algumas marcas SVG.</span><span class="sxs-lookup"><span data-stu-id="65a8f-641">For example, `<use>` tags aren't currently respected, and `@bind` can't be used with some SVG tags.</span></span> <span data-ttu-id="65a8f-642">Esperamos abordar essas limitações em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="65a8f-642">We expect to address these limitations in a future release.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="65a8f-643">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="65a8f-643">Additional resources</span></span>

* <span data-ttu-id="65a8f-644"><xref:security/blazor/server>&ndash; Inclui orientações sobre a criação de aplicativos de servidor mais elaborados que devem combater o esgotamento de recursos.</span><span class="sxs-lookup"><span data-stu-id="65a8f-644"><xref:security/blazor/server> &ndash; Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>
