---
title: Criar e usar ASP.NET Core componentes do Razor
author: guardrex
description: Saiba como criar e usar componentes do Razor, incluindo como associar dados, manipular eventos e gerenciar ciclos de vida do componente.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/components
ms.openlocfilehash: f9b4eab29fafe8113528062f57d28dadd0f57577
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77447094"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="440fb-103">Criar e usar ASP.NET Core componentes do Razor</span><span class="sxs-lookup"><span data-stu-id="440fb-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="440fb-104">De [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="440fb-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="440fb-105">[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([como baixar](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="440fb-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

Blazor<span data-ttu-id="440fb-106"> aplicativos são criados usando *componentes*.</span><span class="sxs-lookup"><span data-stu-id="440fb-106"> apps are built using *components*.</span></span> <span data-ttu-id="440fb-107">Um componente é uma parte independente da interface do usuário (IU), como uma página, uma caixa de diálogo ou um formulário.</span><span class="sxs-lookup"><span data-stu-id="440fb-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="440fb-108">Um componente inclui marcação HTML e a lógica de processamento necessária para injetar dados ou responder a eventos da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="440fb-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="440fb-109">Os componentes são flexíveis e leves.</span><span class="sxs-lookup"><span data-stu-id="440fb-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="440fb-110">Eles podem ser aninhados, reutilizados e compartilhados entre projetos.</span><span class="sxs-lookup"><span data-stu-id="440fb-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="440fb-111">Classes de componente</span><span class="sxs-lookup"><span data-stu-id="440fb-111">Component classes</span></span>

<span data-ttu-id="440fb-112">Os componentes são implementados em arquivos de componente do [Razor](xref:mvc/views/razor) ( *. Razor*) C# usando uma combinação de marcação HTML e.</span><span class="sxs-lookup"><span data-stu-id="440fb-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (*.razor*) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="440fb-113">Um componente no Blazor é formalmente referido como um *componente Razor*.</span><span class="sxs-lookup"><span data-stu-id="440fb-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

<span data-ttu-id="440fb-114">O nome de um componente deve começar com um caractere maiúsculo.</span><span class="sxs-lookup"><span data-stu-id="440fb-114">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="440fb-115">Por exemplo, *MyCoolComponent. Razor* é válido e *MyCoolComponent. Razor* é inválido.</span><span class="sxs-lookup"><span data-stu-id="440fb-115">For example, *MyCoolComponent.razor* is valid, and *myCoolComponent.razor* is invalid.</span></span>

<span data-ttu-id="440fb-116">A interface do usuário para um componente é definida usando HTML.</span><span class="sxs-lookup"><span data-stu-id="440fb-116">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="440fb-117">A lógica de renderização dinâmica (por exemplo, loops, condicionais, expressões) é adicionada usando uma sintaxe de C# inserida chamada [Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="440fb-117">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="440fb-118">Quando um aplicativo é compilado, a marcação HTML e C# a lógica de renderização são convertidas em uma classe de componente.</span><span class="sxs-lookup"><span data-stu-id="440fb-118">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="440fb-119">O nome da classe gerada corresponde ao nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="440fb-119">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="440fb-120">Os membros da classe de componente são definidos em um bloco `@code`.</span><span class="sxs-lookup"><span data-stu-id="440fb-120">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="440fb-121">No bloco de `@code`, estado do componente (Propriedades, campos) é especificado com métodos para manipulação de eventos ou para definir outra lógica de componente.</span><span class="sxs-lookup"><span data-stu-id="440fb-121">In the `@code` block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="440fb-122">Mais de um bloco de `@code` é permitido.</span><span class="sxs-lookup"><span data-stu-id="440fb-122">More than one `@code` block is permissible.</span></span>

<span data-ttu-id="440fb-123">Os membros do componente podem ser usados como parte da lógica de renderização do C# componente usando expressões que começam com `@`.</span><span class="sxs-lookup"><span data-stu-id="440fb-123">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="440fb-124">Por exemplo, um C# campo é renderizado pela prefixação `@` ao nome do campo.</span><span class="sxs-lookup"><span data-stu-id="440fb-124">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="440fb-125">O exemplo a seguir avalia e renderiza:</span><span class="sxs-lookup"><span data-stu-id="440fb-125">The following example evaluates and renders:</span></span>

* <span data-ttu-id="440fb-126">`_headingFontStyle` ao valor da propriedade CSS para `font-style`.</span><span class="sxs-lookup"><span data-stu-id="440fb-126">`_headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="440fb-127">`_headingText` o conteúdo do elemento `<h1>`.</span><span class="sxs-lookup"><span data-stu-id="440fb-127">`_headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="440fb-128">Depois que o componente é processado inicialmente, o componente regenera sua árvore de renderização em resposta a eventos.</span><span class="sxs-lookup"><span data-stu-id="440fb-128">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> <span data-ttu-id="440fb-129">em seguida, Blazor compara a nova árvore de renderização com a anterior e aplica quaisquer modificações à Modelo de Objeto do Documento do navegador (DOM).</span><span class="sxs-lookup"><span data-stu-id="440fb-129">Blazor then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="440fb-130">Os componentes são C# classes comuns e podem ser colocados em qualquer lugar dentro de um projeto.</span><span class="sxs-lookup"><span data-stu-id="440fb-130">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="440fb-131">Os componentes que produzem páginas da Web geralmente residem na pasta *páginas* .</span><span class="sxs-lookup"><span data-stu-id="440fb-131">Components that produce webpages usually reside in the *Pages* folder.</span></span> <span data-ttu-id="440fb-132">Os componentes que não são de página são frequentemente colocados na pasta *compartilhada* ou em uma pasta personalizada adicionada ao projeto.</span><span class="sxs-lookup"><span data-stu-id="440fb-132">Non-page components are frequently placed in the *Shared* folder or a custom folder added to the project.</span></span>

<span data-ttu-id="440fb-133">Normalmente, o namespace de um componente é derivado do namespace raiz do aplicativo e do local do componente (pasta) no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="440fb-133">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="440fb-134">Se o namespace raiz do aplicativo for `BlazorApp` e o componente `Counter` residir na pasta *páginas* :</span><span class="sxs-lookup"><span data-stu-id="440fb-134">If the app's root namespace is `BlazorApp` and the `Counter` component resides in the *Pages* folder:</span></span>

* <span data-ttu-id="440fb-135">O namespace do componente de `Counter` é `BlazorApp.Pages`.</span><span class="sxs-lookup"><span data-stu-id="440fb-135">The `Counter` component's namespace is `BlazorApp.Pages`.</span></span>
* <span data-ttu-id="440fb-136">O nome do tipo totalmente qualificado do componente é `BlazorApp.Pages.Counter`.</span><span class="sxs-lookup"><span data-stu-id="440fb-136">The fully qualified type name of the component is `BlazorApp.Pages.Counter`.</span></span>

<span data-ttu-id="440fb-137">Para obter mais informações, consulte a seção [importar componentes](#import-components) .</span><span class="sxs-lookup"><span data-stu-id="440fb-137">For more information, see the [Import components](#import-components) section.</span></span>

<span data-ttu-id="440fb-138">Para usar uma pasta personalizada, adicione o namespace da pasta personalizada ao componente pai ou ao arquivo *_Imports. Razor* do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="440fb-138">To use a custom folder, add the custom folder's namespace to either the parent component or to the app's *_Imports.razor* file.</span></span> <span data-ttu-id="440fb-139">Por exemplo, o namespace a seguir torna os componentes em uma pasta *componentes* disponíveis quando o namespace raiz do aplicativo é `BlazorApp`:</span><span class="sxs-lookup"><span data-stu-id="440fb-139">For example, the following namespace makes components in a *Components* folder available when the app's root namespace is `BlazorApp`:</span></span>

```razor
@using BlazorApp.Components
```

## <a name="tag-helpers-arent-used-in-components"></a><span data-ttu-id="440fb-140">Os auxiliares de marca não são usados em componentes</span><span class="sxs-lookup"><span data-stu-id="440fb-140">Tag Helpers aren't used in components</span></span>

<span data-ttu-id="440fb-141">Os [auxiliares de marca](xref:mvc/views/tag-helpers/intro) não têm suporte em componentes do Razor (arquivos *. Razor* ).</span><span class="sxs-lookup"><span data-stu-id="440fb-141">[Tag Helpers](xref:mvc/views/tag-helpers/intro) aren't supported in Razor components (*.razor* files).</span></span> <span data-ttu-id="440fb-142">Para fornecer a funcionalidade do tipo auxiliar da marca no Blazor, crie um componente com a mesma funcionalidade que o auxiliar de marca e use o componente em vez disso.</span><span class="sxs-lookup"><span data-stu-id="440fb-142">To provide Tag Helper-like functionality in Blazor, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="use-components"></a><span data-ttu-id="440fb-143">Usar componentes</span><span class="sxs-lookup"><span data-stu-id="440fb-143">Use components</span></span>

<span data-ttu-id="440fb-144">Os componentes podem incluir outros componentes, declarando-os usando a sintaxe do elemento HTML.</span><span class="sxs-lookup"><span data-stu-id="440fb-144">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="440fb-145">A marcação para uso de um componente é semelhante a uma marca HTML, em que o nome da marca é o tipo de componente.</span><span class="sxs-lookup"><span data-stu-id="440fb-145">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="440fb-146">A associação de atributo diferencia maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="440fb-146">Attribute binding is case sensitive.</span></span> <span data-ttu-id="440fb-147">Por exemplo, `@bind` é válido e `@Bind` é inválido.</span><span class="sxs-lookup"><span data-stu-id="440fb-147">For example, `@bind` is valid, and `@Bind` is invalid.</span></span>

<span data-ttu-id="440fb-148">A marcação a seguir no *index. Razor* renderiza uma instância de `HeadingComponent`:</span><span class="sxs-lookup"><span data-stu-id="440fb-148">The following markup in *Index.razor* renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="440fb-149">*Componentes/HeadingComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="440fb-149">*Components/HeadingComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

<span data-ttu-id="440fb-150">Se um componente contiver um elemento HTML com uma letra maiúscula ou minúscula que não corresponda a um nome de componente, um aviso será emitido indicando que o elemento tem um nome inesperado.</span><span class="sxs-lookup"><span data-stu-id="440fb-150">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="440fb-151">Adicionar uma diretiva de `@using` para o namespace do componente disponibiliza o componente, o que resolve o aviso.</span><span class="sxs-lookup"><span data-stu-id="440fb-151">Adding an `@using` directive for the component's namespace makes the component available, which resolves the warning.</span></span>

## <a name="routing"></a><span data-ttu-id="440fb-152">Roteamento</span><span class="sxs-lookup"><span data-stu-id="440fb-152">Routing</span></span>

<span data-ttu-id="440fb-153">O roteamento no Blazor é obtido fornecendo um modelo de rota para cada componente acessível no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="440fb-153">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="440fb-154">Quando um arquivo Razor com uma diretiva `@page` é compilado, a classe gerada recebe um <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> especificando o modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="440fb-154">When a Razor file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="440fb-155">Em tempo de execução, o roteador procura classes de componentes com um `RouteAttribute` e renderiza qualquer componente que tenha um modelo de rota que corresponda à URL solicitada.</span><span class="sxs-lookup"><span data-stu-id="440fb-155">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

```razor
@page "/ParentComponent"

...
```

<span data-ttu-id="440fb-156">Para obter mais informações, consulte <xref:blazor/routing>.</span><span class="sxs-lookup"><span data-stu-id="440fb-156">For more information, see <xref:blazor/routing>.</span></span>

## <a name="parameters"></a><span data-ttu-id="440fb-157">parâmetros</span><span class="sxs-lookup"><span data-stu-id="440fb-157">Parameters</span></span>

### <a name="route-parameters"></a><span data-ttu-id="440fb-158">Parâmetros de rota</span><span class="sxs-lookup"><span data-stu-id="440fb-158">Route parameters</span></span>

<span data-ttu-id="440fb-159">Os componentes podem receber parâmetros de rota do modelo de rota fornecido na diretiva `@page`.</span><span class="sxs-lookup"><span data-stu-id="440fb-159">Components can receive route parameters from the route template provided in the `@page` directive.</span></span> <span data-ttu-id="440fb-160">O roteador usa parâmetros de rota para preencher os parâmetros de componente correspondentes.</span><span class="sxs-lookup"><span data-stu-id="440fb-160">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="440fb-161">*Páginas/RouteParameter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="440fb-161">*Pages/RouteParameter.razor*:</span></span>

[!code-razor[](components/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

<span data-ttu-id="440fb-162">Não há suporte para parâmetros opcionais, portanto, duas diretivas `@page` são aplicadas no exemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="440fb-162">Optional parameters aren't supported, so two `@page` directives are applied in the preceding example.</span></span> <span data-ttu-id="440fb-163">O primeiro permite a navegação para o componente sem um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="440fb-163">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="440fb-164">A segunda diretiva `@page` recebe o parâmetro `{text}` Route e atribui o valor à propriedade `Text`.</span><span class="sxs-lookup"><span data-stu-id="440fb-164">The second `@page` directive receives the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

<span data-ttu-id="440fb-165">A sintaxe de parâmetro *catch-all* (`*`/`**`), que captura o caminho entre vários limites de pasta, **não** tem suporte em componentes do Razor ( *. Razor*).</span><span class="sxs-lookup"><span data-stu-id="440fb-165">*Catch-all* parameter syntax (`*`/`**`), which captures the path across multiple folder boundaries, is **not** supported in Razor components (*.razor*).</span></span>

### <a name="component-parameters"></a><span data-ttu-id="440fb-166">Parâmetros do componente</span><span class="sxs-lookup"><span data-stu-id="440fb-166">Component parameters</span></span>

<span data-ttu-id="440fb-167">Os componentes podem ter *parâmetros de componente*, que são definidos usando propriedades públicas na classe de componente com o atributo `[Parameter]`.</span><span class="sxs-lookup"><span data-stu-id="440fb-167">Components can have *component parameters*, which are defined using public properties on the component class with the `[Parameter]` attribute.</span></span> <span data-ttu-id="440fb-168">Use atributos para especificar argumentos para um componente na marcação.</span><span class="sxs-lookup"><span data-stu-id="440fb-168">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="440fb-169">*Componentes/ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="440fb-169">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

<span data-ttu-id="440fb-170">No exemplo a seguir do aplicativo de exemplo, o `ParentComponent` define o valor da propriedade `Title` do `ChildComponent`.</span><span class="sxs-lookup"><span data-stu-id="440fb-170">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="440fb-171">*Páginas/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="440fb-171">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=5-6)]

## <a name="child-content"></a><span data-ttu-id="440fb-172">Conteúdo filho</span><span class="sxs-lookup"><span data-stu-id="440fb-172">Child content</span></span>

<span data-ttu-id="440fb-173">Os componentes podem definir o conteúdo de outro componente.</span><span class="sxs-lookup"><span data-stu-id="440fb-173">Components can set the content of another component.</span></span> <span data-ttu-id="440fb-174">O componente de atribuição fornece o conteúdo entre as marcas que especificam o componente de recebimento.</span><span class="sxs-lookup"><span data-stu-id="440fb-174">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="440fb-175">No exemplo a seguir, o `ChildComponent` tem uma propriedade `ChildContent` que representa um `RenderFragment`, que representa um segmento de interface do usuário a ser renderizado.</span><span class="sxs-lookup"><span data-stu-id="440fb-175">In the following example, the `ChildComponent` has a `ChildContent` property that represents a `RenderFragment`, which represents a segment of UI to render.</span></span> <span data-ttu-id="440fb-176">O valor de `ChildContent` é posicionado na marcação do componente onde o conteúdo deve ser renderizado.</span><span class="sxs-lookup"><span data-stu-id="440fb-176">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="440fb-177">O valor de `ChildContent` é recebido do componente pai e renderizado dentro do `panel-body`do painel de inicialização.</span><span class="sxs-lookup"><span data-stu-id="440fb-177">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="440fb-178">*Componentes/ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="440fb-178">*Components/ChildComponent.razor*:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="440fb-179">A propriedade que recebe o conteúdo de `RenderFragment` deve ser nomeada `ChildContent` por convenção.</span><span class="sxs-lookup"><span data-stu-id="440fb-179">The property receiving the `RenderFragment` content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="440fb-180">O `ParentComponent` no aplicativo de exemplo pode fornecer conteúdo para renderizar o `ChildComponent` colocando o conteúdo dentro das marcas de `<ChildComponent>`.</span><span class="sxs-lookup"><span data-stu-id="440fb-180">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="440fb-181">*Páginas/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="440fb-181">*Pages/ParentComponent.razor*:</span></span>

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="440fb-182">Atributo nivelamento e parâmetros arbitrários</span><span class="sxs-lookup"><span data-stu-id="440fb-182">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="440fb-183">Os componentes podem capturar e renderizar atributos adicionais além dos parâmetros declarados do componente.</span><span class="sxs-lookup"><span data-stu-id="440fb-183">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="440fb-184">Atributos adicionais podem ser capturados em um dicionário e, em seguida, *splatted* em um elemento quando o componente é renderizado usando a diretiva [`@attributes`](xref:mvc/views/razor#attributes) Razor.</span><span class="sxs-lookup"><span data-stu-id="440fb-184">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`](xref:mvc/views/razor#attributes) Razor directive.</span></span> <span data-ttu-id="440fb-185">Esse cenário é útil ao definir um componente que produz um elemento de marcação que dá suporte a uma variedade de personalizações.</span><span class="sxs-lookup"><span data-stu-id="440fb-185">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="440fb-186">Por exemplo, pode ser entediante definir atributos separadamente para um `<input>` que ofereça suporte a muitos parâmetros.</span><span class="sxs-lookup"><span data-stu-id="440fb-186">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="440fb-187">No exemplo a seguir, o primeiro elemento `<input>` (`id="useIndividualParams"`) usa parâmetros de componente individuais, enquanto o segundo elemento `<input>` (`id="useAttributesDict"`) usa o atributo nivelamento:</span><span class="sxs-lookup"><span data-stu-id="440fb-187">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

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

<span data-ttu-id="440fb-188">O tipo do parâmetro deve implementar `IEnumerable<KeyValuePair<string, object>>` com chaves de cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="440fb-188">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` with string keys.</span></span> <span data-ttu-id="440fb-189">Usar `IReadOnlyDictionary<string, object>` também é uma opção nesse cenário.</span><span class="sxs-lookup"><span data-stu-id="440fb-189">Using `IReadOnlyDictionary<string, object>` is also an option in this scenario.</span></span>

<span data-ttu-id="440fb-190">Os elementos `<input>` renderizados usando ambas as abordagens são idênticos:</span><span class="sxs-lookup"><span data-stu-id="440fb-190">The rendered `<input>` elements using both approaches is identical:</span></span>

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

<span data-ttu-id="440fb-191">Para aceitar atributos arbitrários, defina um parâmetro de componente usando o atributo `[Parameter]` com a propriedade `CaptureUnmatchedValues` definida como `true`:</span><span class="sxs-lookup"><span data-stu-id="440fb-191">To accept arbitrary attributes, define a component parameter using the `[Parameter]` attribute with the `CaptureUnmatchedValues` property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="440fb-192">A propriedade `CaptureUnmatchedValues` em `[Parameter]` permite que o parâmetro corresponda a todos os atributos que não correspondem a nenhum outro parâmetro.</span><span class="sxs-lookup"><span data-stu-id="440fb-192">The `CaptureUnmatchedValues` property on `[Parameter]` allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="440fb-193">Um componente só pode definir um único parâmetro com `CaptureUnmatchedValues`.</span><span class="sxs-lookup"><span data-stu-id="440fb-193">A component can only define a single parameter with `CaptureUnmatchedValues`.</span></span> <span data-ttu-id="440fb-194">O tipo de propriedade usado com `CaptureUnmatchedValues` deve ser atribuível de `Dictionary<string, object>` com chaves de cadeia de caracteres.</span><span class="sxs-lookup"><span data-stu-id="440fb-194">The property type used with `CaptureUnmatchedValues` must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="440fb-195">`IEnumerable<KeyValuePair<string, object>>` ou `IReadOnlyDictionary<string, object>` também são opções neste cenário.</span><span class="sxs-lookup"><span data-stu-id="440fb-195">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="440fb-196">A posição de `@attributes` em relação à posição dos atributos do elemento é importante.</span><span class="sxs-lookup"><span data-stu-id="440fb-196">The position of `@attributes` relative to the position of element attributes is important.</span></span> <span data-ttu-id="440fb-197">Quando `@attributes` são splatted no elemento, os atributos são processados da direita para a esquerda (último a primeiro).</span><span class="sxs-lookup"><span data-stu-id="440fb-197">When `@attributes` are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="440fb-198">Considere o exemplo a seguir de um componente que consome um componente `Child`:</span><span class="sxs-lookup"><span data-stu-id="440fb-198">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="440fb-199">*ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="440fb-199">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="440fb-200">*ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="440fb-200">*ChildComponent.razor*:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="440fb-201">O atributo `extra` do componente de `Child` é definido à direita de `@attributes`.</span><span class="sxs-lookup"><span data-stu-id="440fb-201">The `Child` component's `extra` attribute is set to the right of `@attributes`.</span></span> <span data-ttu-id="440fb-202">O `<div>` renderizado do componente `Parent` contém `extra="5"` quando passa pelo atributo adicional porque os atributos são processados da direita para a esquerda (último a primeiro):</span><span class="sxs-lookup"><span data-stu-id="440fb-202">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="440fb-203">No exemplo a seguir, a ordem de `extra` e `@attributes` é revertida na `<div>`do componente `Child`:</span><span class="sxs-lookup"><span data-stu-id="440fb-203">In the following example, the order of `extra` and `@attributes` is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="440fb-204">*ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="440fb-204">*ParentComponent.razor*:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="440fb-205">*ChildComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="440fb-205">*ChildComponent.razor*:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="440fb-206">O `<div>` renderizado no componente `Parent` contém `extra="10"` quando passado pelo atributo adicional:</span><span class="sxs-lookup"><span data-stu-id="440fb-206">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a><span data-ttu-id="440fb-207">Capturar referências a componentes</span><span class="sxs-lookup"><span data-stu-id="440fb-207">Capture references to components</span></span>

<span data-ttu-id="440fb-208">As referências de componente fornecem uma maneira de fazer referência a uma instância de componente para que você possa emitir comandos para essa instância, como `Show` ou `Reset`.</span><span class="sxs-lookup"><span data-stu-id="440fb-208">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="440fb-209">Para capturar uma referência de componente:</span><span class="sxs-lookup"><span data-stu-id="440fb-209">To capture a component reference:</span></span>

* <span data-ttu-id="440fb-210">Adicione um atributo [`@ref`](xref:mvc/views/razor#ref) ao componente filho.</span><span class="sxs-lookup"><span data-stu-id="440fb-210">Add an [`@ref`](xref:mvc/views/razor#ref) attribute to the child component.</span></span>
* <span data-ttu-id="440fb-211">Defina um campo com o mesmo tipo do componente filho.</span><span class="sxs-lookup"><span data-stu-id="440fb-211">Define a field with the same type as the child component.</span></span>

```razor
<MyLoginDialog @ref="_loginDialog" ... />

@code {
    private MyLoginDialog _loginDialog;

    private void OnSomething()
    {
        _loginDialog.Show();
    }
}
```

<span data-ttu-id="440fb-212">Quando o componente é renderizado, o campo `_loginDialog` é populado com a instância de componente filho `MyLoginDialog`.</span><span class="sxs-lookup"><span data-stu-id="440fb-212">When the component is rendered, the `_loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="440fb-213">Em seguida, você pode invocar os métodos .NET na instância do componente.</span><span class="sxs-lookup"><span data-stu-id="440fb-213">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="440fb-214">A variável `_loginDialog` é populada apenas depois que o componente é renderizado e sua saída inclui o elemento `MyLoginDialog`.</span><span class="sxs-lookup"><span data-stu-id="440fb-214">The `_loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="440fb-215">Até esse ponto, não há nada a fazer referência.</span><span class="sxs-lookup"><span data-stu-id="440fb-215">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="440fb-216">Para manipular referências de componentes após a conclusão da renderização do componente, use os [métodos OnAfterRenderAsync ou OnAfterRender](xref:blazor/lifecycle#after-component-render).</span><span class="sxs-lookup"><span data-stu-id="440fb-216">To manipulate components references after the component has finished rendering, use the [OnAfterRenderAsync or OnAfterRender methods](xref:blazor/lifecycle#after-component-render).</span></span>

<span data-ttu-id="440fb-217">Embora a captura de referências de componente use uma sintaxe semelhante à [captura de referências de elemento](xref:blazor/javascript-interop#capture-references-to-elements), ela não é um recurso de [interoperabilidade do JavaScript](xref:blazor/javascript-interop) .</span><span class="sxs-lookup"><span data-stu-id="440fb-217">While capturing component references use a similar syntax to [capturing element references](xref:blazor/javascript-interop#capture-references-to-elements), it isn't a [JavaScript interop](xref:blazor/javascript-interop) feature.</span></span> <span data-ttu-id="440fb-218">As referências de componente não são passadas para o código JavaScript&mdash;elas são usadas apenas no código .NET.</span><span class="sxs-lookup"><span data-stu-id="440fb-218">Component references aren't passed to JavaScript code&mdash;they're only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="440fb-219">Não **use referências** de componente para converter o estado dos componentes filho.</span><span class="sxs-lookup"><span data-stu-id="440fb-219">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="440fb-220">Em vez disso, use parâmetros declarativos normais para passar dados para componentes filho.</span><span class="sxs-lookup"><span data-stu-id="440fb-220">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="440fb-221">O uso de parâmetros declarativos normais resulta em componentes filho que são reprocessados nos horários corretos automaticamente.</span><span class="sxs-lookup"><span data-stu-id="440fb-221">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="440fb-222">Invocar métodos de componente externamente para atualizar o estado</span><span class="sxs-lookup"><span data-stu-id="440fb-222">Invoke component methods externally to update state</span></span>

Blazor<span data-ttu-id="440fb-223"> usa uma `SynchronizationContext` para impor um único thread lógico de execução.</span><span class="sxs-lookup"><span data-stu-id="440fb-223"> uses a `SynchronizationContext` to enforce a single logical thread of execution.</span></span> <span data-ttu-id="440fb-224">Os métodos de [ciclo de vida](xref:blazor/lifecycle) de um componente e quaisquer retornos de chamada de evento gerados por Blazor são executados nesse `SynchronizationContext`.</span><span class="sxs-lookup"><span data-stu-id="440fb-224">A component's [lifecycle methods](xref:blazor/lifecycle) and any event callbacks that are raised by Blazor are executed on this `SynchronizationContext`.</span></span> <span data-ttu-id="440fb-225">No caso de um componente precisar ser atualizado com base em um evento externo, como um temporizador ou outras notificações, use o método `InvokeAsync`, que será expedido para o `SynchronizationContext`de Blazor.</span><span class="sxs-lookup"><span data-stu-id="440fb-225">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which will dispatch to Blazor's `SynchronizationContext`.</span></span>

<span data-ttu-id="440fb-226">Por exemplo, considere um *serviço de notificação* que pode notificar qualquer componente de escuta do estado atualizado:</span><span class="sxs-lookup"><span data-stu-id="440fb-226">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

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

<span data-ttu-id="440fb-227">Registre o `NotifierService` como um singletion:</span><span class="sxs-lookup"><span data-stu-id="440fb-227">Register the `NotifierService` as a singletion:</span></span>

* <span data-ttu-id="440fb-228">Em Blazor Webassembly, registre o serviço no `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="440fb-228">In Blazor WebAssembly, register the service in `Program.Main`:</span></span>

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* <span data-ttu-id="440fb-229">No Blazor Server, registre o serviço no `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="440fb-229">In Blazor Server, register the service in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddSingleton<NotifierService>();
  ```

<span data-ttu-id="440fb-230">Use o `NotifierService` para atualizar um componente:</span><span class="sxs-lookup"><span data-stu-id="440fb-230">Use the `NotifierService` to update a component:</span></span>

```razor
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @_lastNotification.key = @_lastNotification.value</p>

@code {
    private (string key, int value) _lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            _lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

<span data-ttu-id="440fb-231">No exemplo anterior, `NotifierService` invoca o método `OnNotify` do componente fora da `SynchronizationContext`do Blazor.</span><span class="sxs-lookup"><span data-stu-id="440fb-231">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's `SynchronizationContext`.</span></span> <span data-ttu-id="440fb-232">`InvokeAsync` é usado para alternar para o contexto correto e enfileirar uma renderização.</span><span class="sxs-lookup"><span data-stu-id="440fb-232">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="440fb-233">Use \@chave para controlar a preservação de elementos e componentes</span><span class="sxs-lookup"><span data-stu-id="440fb-233">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="440fb-234">Ao renderizar uma lista de elementos ou componentes e os elementos ou componentes subsequentemente mudam, o algoritmo de diferenciação do Blazordeve decidir qual dos elementos ou componentes anteriores podem ser mantidos e como os objetos de modelo devem ser mapeados para eles.</span><span class="sxs-lookup"><span data-stu-id="440fb-234">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="440fb-235">Normalmente, esse processo é automático e pode ser ignorado, mas há casos em que você talvez queira controlar o processo.</span><span class="sxs-lookup"><span data-stu-id="440fb-235">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="440fb-236">Considere o exemplo a seguir:</span><span class="sxs-lookup"><span data-stu-id="440fb-236">Consider the following example:</span></span>

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

<span data-ttu-id="440fb-237">O conteúdo da coleção de `People` pode ser alterado com entradas inseridas, excluídas ou reordenadas.</span><span class="sxs-lookup"><span data-stu-id="440fb-237">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="440fb-238">Quando o componente é rerenderizado, o componente `<DetailsEditor>` pode ser alterado para receber diferentes valores de parâmetro `Details`.</span><span class="sxs-lookup"><span data-stu-id="440fb-238">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="440fb-239">Isso pode causar um reprocessamento mais complexo do que o esperado.</span><span class="sxs-lookup"><span data-stu-id="440fb-239">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="440fb-240">Em alguns casos, a rerenderização pode levar a diferenças de comportamento visíveis, como o foco de elemento perdido.</span><span class="sxs-lookup"><span data-stu-id="440fb-240">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="440fb-241">O processo de mapeamento pode ser controlado com o atributo de diretiva `@key`.</span><span class="sxs-lookup"><span data-stu-id="440fb-241">The mapping process can be controlled with the `@key` directive attribute.</span></span> <span data-ttu-id="440fb-242">`@key` faz com que o algoritmo diff garanta a preservação de elementos ou componentes com base no valor da chave:</span><span class="sxs-lookup"><span data-stu-id="440fb-242">`@key` causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

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

<span data-ttu-id="440fb-243">Quando a coleção de `People` é alterada, o algoritmo diff mantém a associação entre instâncias de `<DetailsEditor>` e `person` instâncias:</span><span class="sxs-lookup"><span data-stu-id="440fb-243">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="440fb-244">Se uma `Person` for excluída da lista de `People`, somente a instância `<DetailsEditor>` correspondente será removida da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="440fb-244">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="440fb-245">Outras instâncias permanecem inalteradas.</span><span class="sxs-lookup"><span data-stu-id="440fb-245">Other instances are left unchanged.</span></span>
* <span data-ttu-id="440fb-246">Se uma `Person` for inserida em alguma posição na lista, uma nova instância de `<DetailsEditor>` será inserida na posição correspondente.</span><span class="sxs-lookup"><span data-stu-id="440fb-246">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="440fb-247">Outras instâncias permanecem inalteradas.</span><span class="sxs-lookup"><span data-stu-id="440fb-247">Other instances are left unchanged.</span></span>
* <span data-ttu-id="440fb-248">Se `Person` entradas forem reordenadas, as instâncias de `<DetailsEditor>` correspondentes serão preservadas e reordenadas na interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="440fb-248">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="440fb-249">Em alguns cenários, o uso de `@key` minimiza a complexidade de rerenderização e evita possíveis problemas com partes com estado da alteração do DOM, como a posição do foco.</span><span class="sxs-lookup"><span data-stu-id="440fb-249">In some scenarios, use of `@key` minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="440fb-250">As chaves são locais para cada elemento ou componente de contêiner.</span><span class="sxs-lookup"><span data-stu-id="440fb-250">Keys are local to each container element or component.</span></span> <span data-ttu-id="440fb-251">As chaves não são comparadas globalmente ao longo do documento.</span><span class="sxs-lookup"><span data-stu-id="440fb-251">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="440fb-252">Quando usar \@chave</span><span class="sxs-lookup"><span data-stu-id="440fb-252">When to use \@key</span></span>

<span data-ttu-id="440fb-253">Normalmente, faz sentido usar `@key` sempre que uma lista é renderizada (por exemplo, em um bloco de `@foreach`) e um valor adequado existe para definir o `@key`.</span><span class="sxs-lookup"><span data-stu-id="440fb-253">Typically, it makes sense to use `@key` whenever a list is rendered (for example, in a `@foreach` block) and a suitable value exists to define the `@key`.</span></span>

<span data-ttu-id="440fb-254">Você também pode usar `@key` para impedir Blazor de preservar uma subárvore de elemento ou componente quando um objeto for alterado:</span><span class="sxs-lookup"><span data-stu-id="440fb-254">You can also use `@key` to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="440fb-255">Se `@currentPerson` for alterado, a diretiva de atributo `@key` forçará Blazor a descartar toda a `<div>` e seus descendentes e recriará a subárvore dentro da interface do usuário com novos elementos e componentes.</span><span class="sxs-lookup"><span data-stu-id="440fb-255">If `@currentPerson` changes, the `@key` attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="440fb-256">Isso pode ser útil se você precisar garantir que nenhum estado da interface do usuário seja preservado quando `@currentPerson` for alterado.</span><span class="sxs-lookup"><span data-stu-id="440fb-256">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="440fb-257">Quando não usar \@chave</span><span class="sxs-lookup"><span data-stu-id="440fb-257">When not to use \@key</span></span>

<span data-ttu-id="440fb-258">Há um custo de desempenho ao comparar com `@key`.</span><span class="sxs-lookup"><span data-stu-id="440fb-258">There's a performance cost when diffing with `@key`.</span></span> <span data-ttu-id="440fb-259">O custo de desempenho não é grande, mas só especifique `@key` se o controle das regras de preservação de elemento ou componente beneficiar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="440fb-259">The performance cost isn't large, but only specify `@key` if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="440fb-260">Mesmo que `@key` não seja usado, Blazor preserva as instâncias de elemento filho e de componente o máximo possível.</span><span class="sxs-lookup"><span data-stu-id="440fb-260">Even if `@key` isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="440fb-261">A única vantagem de usar `@key` é o controle sobre *como* as instâncias de modelo são mapeadas para as instâncias de componente preservadas, em vez do algoritmo diff, selecionando o mapeamento.</span><span class="sxs-lookup"><span data-stu-id="440fb-261">The only advantage to using `@key` is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="440fb-262">Quais valores usar para \@chave</span><span class="sxs-lookup"><span data-stu-id="440fb-262">What values to use for \@key</span></span>

<span data-ttu-id="440fb-263">Geralmente, faz sentido fornecer um dos seguintes tipos de valor para `@key`:</span><span class="sxs-lookup"><span data-stu-id="440fb-263">Generally, it makes sense to supply one of the following kinds of value for `@key`:</span></span>

* <span data-ttu-id="440fb-264">Instâncias de objeto de modelo (por exemplo, uma instância de `Person` como no exemplo anterior).</span><span class="sxs-lookup"><span data-stu-id="440fb-264">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="440fb-265">Isso garante a preservação com base na igualdade de referência de objeto.</span><span class="sxs-lookup"><span data-stu-id="440fb-265">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="440fb-266">Identificadores exclusivos (por exemplo, valores de chave primária do tipo `int`, `string`ou `Guid`).</span><span class="sxs-lookup"><span data-stu-id="440fb-266">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="440fb-267">Verifique se os valores usados para `@key` não estão em conflito.</span><span class="sxs-lookup"><span data-stu-id="440fb-267">Ensure that values used for `@key` don't clash.</span></span> <span data-ttu-id="440fb-268">Se valores conflitantes forem detectados dentro do mesmo elemento pai, Blazor lançará uma exceção porque ele não pode mapear de forma determinística elementos ou componentes antigos para novos elementos ou componentes.</span><span class="sxs-lookup"><span data-stu-id="440fb-268">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="440fb-269">Use apenas valores distintos, como instâncias de objeto ou valores de chave primária.</span><span class="sxs-lookup"><span data-stu-id="440fb-269">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="partial-class-support"></a><span data-ttu-id="440fb-270">Suporte de classe parcial</span><span class="sxs-lookup"><span data-stu-id="440fb-270">Partial class support</span></span>

<span data-ttu-id="440fb-271">Os componentes do Razor são gerados como classes parciais.</span><span class="sxs-lookup"><span data-stu-id="440fb-271">Razor components are generated as partial classes.</span></span> <span data-ttu-id="440fb-272">Os componentes do Razor são criados usando uma das seguintes abordagens:</span><span class="sxs-lookup"><span data-stu-id="440fb-272">Razor components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="440fb-273">C#o código é definido em um bloco de [`@code`](xref:mvc/views/razor#code) com marcação HTML e código do Razor em um único arquivo.</span><span class="sxs-lookup"><span data-stu-id="440fb-273">C# code is defined in an [`@code`](xref:mvc/views/razor#code) block with HTML markup and Razor code in a single file.</span></span> Blazor<span data-ttu-id="440fb-274"> modelos definem seus componentes do Razor usando essa abordagem.</span><span class="sxs-lookup"><span data-stu-id="440fb-274"> templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="440fb-275">C#o código é colocado em um arquivo code-behind definido como uma classe parcial.</span><span class="sxs-lookup"><span data-stu-id="440fb-275">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="440fb-276">O exemplo a seguir mostra o componente de `Counter` padrão com um bloco de `@code` em um aplicativo gerado por meio de um modelo de Blazor.</span><span class="sxs-lookup"><span data-stu-id="440fb-276">The following example shows the default `Counter` component with an `@code` block in an app generated from a Blazor template.</span></span> <span data-ttu-id="440fb-277">Marcação HTML, código do Razor e C# código estão no mesmo arquivo:</span><span class="sxs-lookup"><span data-stu-id="440fb-277">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="440fb-278">*Counter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="440fb-278">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @_currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int _currentCount = 0;

    void IncrementCount()
    {
        _currentCount++;
    }
}
```

<span data-ttu-id="440fb-279">O componente `Counter` também pode ser criado usando um arquivo code-behind com uma classe parcial:</span><span class="sxs-lookup"><span data-stu-id="440fb-279">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="440fb-280">*Counter. Razor*:</span><span class="sxs-lookup"><span data-stu-id="440fb-280">*Counter.razor*:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @_currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="440fb-281">*Counter.Razor.cs*:</span><span class="sxs-lookup"><span data-stu-id="440fb-281">*Counter.razor.cs*:</span></span>

```csharp
namespace BlazorApp.Pages
{
    public partial class Counter
    {
        private int _currentCount = 0;

        void IncrementCount()
        {
            _currentCount++;
        }
    }
}
```

<span data-ttu-id="440fb-282">Adicione quaisquer namespaces necessários ao arquivo de classe parcial, conforme necessário.</span><span class="sxs-lookup"><span data-stu-id="440fb-282">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="440fb-283">Os namespaces típicos usados pelos componentes do Razor incluem:</span><span class="sxs-lookup"><span data-stu-id="440fb-283">Typical namespaces used by Razor components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

## <a name="specify-a-base-class"></a><span data-ttu-id="440fb-284">Especificar uma classe base</span><span class="sxs-lookup"><span data-stu-id="440fb-284">Specify a base class</span></span>

<span data-ttu-id="440fb-285">A diretiva [`@inherits`](xref:mvc/views/razor#inherits) pode ser usada para especificar uma classe base para um componente.</span><span class="sxs-lookup"><span data-stu-id="440fb-285">The [`@inherits`](xref:mvc/views/razor#inherits) directive can be used to specify a base class for a component.</span></span> <span data-ttu-id="440fb-286">O exemplo a seguir mostra como um componente pode herdar uma classe base, `BlazorRocksBase`, para fornecer as propriedades e os métodos do componente.</span><span class="sxs-lookup"><span data-stu-id="440fb-286">The following example shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span> <span data-ttu-id="440fb-287">A classe base deve derivar de `ComponentBase`.</span><span class="sxs-lookup"><span data-stu-id="440fb-287">The base class should derive from `ComponentBase`.</span></span>

<span data-ttu-id="440fb-288">*Páginas/BlazorRocks. Razor*:</span><span class="sxs-lookup"><span data-stu-id="440fb-288">*Pages/BlazorRocks.razor*:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="440fb-289">*BlazorRocksBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="440fb-289">*BlazorRocksBase.cs*:</span></span>

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

## <a name="specify-an-attribute"></a><span data-ttu-id="440fb-290">Especificar um atributo</span><span class="sxs-lookup"><span data-stu-id="440fb-290">Specify an attribute</span></span>

<span data-ttu-id="440fb-291">Os atributos podem ser especificados em componentes do Razor com a diretiva [`@attribute`](xref:mvc/views/razor#attribute) .</span><span class="sxs-lookup"><span data-stu-id="440fb-291">Attributes can be specified in Razor components with the [`@attribute`](xref:mvc/views/razor#attribute) directive.</span></span> <span data-ttu-id="440fb-292">O exemplo a seguir aplica o atributo `[Authorize]` à classe de componente:</span><span class="sxs-lookup"><span data-stu-id="440fb-292">The following example applies the `[Authorize]` attribute to the component class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="import-components"></a><span data-ttu-id="440fb-293">Importar componentes</span><span class="sxs-lookup"><span data-stu-id="440fb-293">Import components</span></span>

<span data-ttu-id="440fb-294">O namespace de um componente criado com o Razor baseia-se em (em ordem de prioridade):</span><span class="sxs-lookup"><span data-stu-id="440fb-294">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="440fb-295">[`@namespace`](xref:mvc/views/razor#namespace) designação na marcação de arquivo Razor ( *. Razor*) (`@namespace BlazorSample.MyNamespace`).</span><span class="sxs-lookup"><span data-stu-id="440fb-295">[`@namespace`](xref:mvc/views/razor#namespace) designation in Razor file (*.razor*) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="440fb-296">O `RootNamespace` do projeto no arquivo de projeto (`<RootNamespace>BlazorSample</RootNamespace>`).</span><span class="sxs-lookup"><span data-stu-id="440fb-296">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="440fb-297">O nome do projeto, obtido do nome do arquivo do projeto ( *. csproj*) e o caminho da raiz do projeto para o componente.</span><span class="sxs-lookup"><span data-stu-id="440fb-297">The project name, taken from the project file's file name (*.csproj*), and the path from the project root to the component.</span></span> <span data-ttu-id="440fb-298">Por exemplo, a estrutura resolve *{raiz do projeto}/pages/index.Razor* (*BlazorSample. csproj*) para o namespace `BlazorSample.Pages`.</span><span class="sxs-lookup"><span data-stu-id="440fb-298">For example, the framework resolves *{PROJECT ROOT}/Pages/Index.razor* (*BlazorSample.csproj*) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="440fb-299">Os componentes C# seguem regras de associação de nome.</span><span class="sxs-lookup"><span data-stu-id="440fb-299">Components follow C# name binding rules.</span></span> <span data-ttu-id="440fb-300">Para o componente `Index` neste exemplo, os componentes no escopo são todos os componentes:</span><span class="sxs-lookup"><span data-stu-id="440fb-300">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="440fb-301">Na mesma pasta, *páginas*.</span><span class="sxs-lookup"><span data-stu-id="440fb-301">In the same folder, *Pages*.</span></span>
  * <span data-ttu-id="440fb-302">Os componentes na raiz do projeto que não especificam explicitamente um namespace diferente.</span><span class="sxs-lookup"><span data-stu-id="440fb-302">The components in the project's root that don't explicitly specify a different namespace.</span></span>

<span data-ttu-id="440fb-303">Os componentes definidos em um namespace diferente são trazidos para o escopo usando a diretiva [`@using`](xref:mvc/views/razor#using) do Razor.</span><span class="sxs-lookup"><span data-stu-id="440fb-303">Components defined in a different namespace are brought into scope using Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span>

<span data-ttu-id="440fb-304">Se outro componente, `NavMenu.razor`, existir na pasta *BlazorSample/Shared/* , o componente poderá ser usado em `Index.razor` com a seguinte instrução `@using`:</span><span class="sxs-lookup"><span data-stu-id="440fb-304">If another component, `NavMenu.razor`, exists in the *BlazorSample/Shared/* folder, the component can be used in `Index.razor` with the following `@using` statement:</span></span>

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

<span data-ttu-id="440fb-305">Os componentes também podem ser referenciados usando seus nomes totalmente qualificados, o que não requer a diretiva [`@using`](xref:mvc/views/razor#using) :</span><span class="sxs-lookup"><span data-stu-id="440fb-305">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`](xref:mvc/views/razor#using) directive:</span></span>

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> <span data-ttu-id="440fb-306">Não há suporte para a qualificação de `global::`.</span><span class="sxs-lookup"><span data-stu-id="440fb-306">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="440fb-307">Não há suporte para a importação de componentes com instruções de `using` com alias (por exemplo, `@using Foo = Bar`).</span><span class="sxs-lookup"><span data-stu-id="440fb-307">Importing components with aliased `using` statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="440fb-308">Não há suporte para nomes parcialmente qualificados.</span><span class="sxs-lookup"><span data-stu-id="440fb-308">Partially qualified names aren't supported.</span></span> <span data-ttu-id="440fb-309">Por exemplo, não há suporte para a adição de `@using BlazorSample` e referência `NavMenu.razor` com `<Shared.NavMenu></Shared.NavMenu>`.</span><span class="sxs-lookup"><span data-stu-id="440fb-309">For example, adding `@using BlazorSample` and referencing `NavMenu.razor` with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="440fb-310">Atributos de elemento HTML condicional</span><span class="sxs-lookup"><span data-stu-id="440fb-310">Conditional HTML element attributes</span></span>

<span data-ttu-id="440fb-311">Os atributos de elemento HTML são processados condicionalmente com base no valor do .NET.</span><span class="sxs-lookup"><span data-stu-id="440fb-311">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="440fb-312">Se o valor for `false` ou `null`, o atributo não será renderizado.</span><span class="sxs-lookup"><span data-stu-id="440fb-312">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="440fb-313">Se o valor é `true`, o atributo é processado minimizado.</span><span class="sxs-lookup"><span data-stu-id="440fb-313">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="440fb-314">No exemplo a seguir, `IsCompleted` determina se `checked` é renderizado na marcação do elemento:</span><span class="sxs-lookup"><span data-stu-id="440fb-314">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="440fb-315">Se `IsCompleted` for `true`, a caixa de seleção será renderizada como:</span><span class="sxs-lookup"><span data-stu-id="440fb-315">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="440fb-316">Se `IsCompleted` for `false`, a caixa de seleção será renderizada como:</span><span class="sxs-lookup"><span data-stu-id="440fb-316">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="440fb-317">Para obter mais informações, consulte <xref:mvc/views/razor>.</span><span class="sxs-lookup"><span data-stu-id="440fb-317">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="440fb-318">Alguns atributos HTML, como [pressionados pelo Aria](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), não funcionam corretamente quando o tipo .net é um `bool`.</span><span class="sxs-lookup"><span data-stu-id="440fb-318">Some HTML attributes, such as [aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="440fb-319">Nesses casos, use um tipo de `string` em vez de um `bool`.</span><span class="sxs-lookup"><span data-stu-id="440fb-319">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="440fb-320">HTML bruto</span><span class="sxs-lookup"><span data-stu-id="440fb-320">Raw HTML</span></span>

<span data-ttu-id="440fb-321">Normalmente, as cadeias de caracteres são renderizadas usando nós de texto DOM, o que significa que qualquer marcação que ela possa conter será ignorada e tratada como texto literal.</span><span class="sxs-lookup"><span data-stu-id="440fb-321">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="440fb-322">Para renderizar HTML bruto, empacote o conteúdo HTML em um valor `MarkupString`.</span><span class="sxs-lookup"><span data-stu-id="440fb-322">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="440fb-323">O valor é analisado como HTML ou SVG e inserido no DOM.</span><span class="sxs-lookup"><span data-stu-id="440fb-323">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="440fb-324">O processamento de HTML bruto construído a partir de qualquer fonte não confiável é um **risco à segurança** e deve ser evitado!</span><span class="sxs-lookup"><span data-stu-id="440fb-324">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="440fb-325">O exemplo a seguir mostra como usar o tipo de `MarkupString` para adicionar um bloco de conteúdo HTML estático à saída renderizada de um componente:</span><span class="sxs-lookup"><span data-stu-id="440fb-325">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)_myMarkup)

@code {
    private string _myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="440fb-326">Valores e parâmetros em cascata</span><span class="sxs-lookup"><span data-stu-id="440fb-326">Cascading values and parameters</span></span>

<span data-ttu-id="440fb-327">Em alguns cenários, é inconveniente fluir dados de um componente ancestral para um componente descendente usando [parâmetros de componente](#component-parameters), especialmente quando há várias camadas de componente.</span><span class="sxs-lookup"><span data-stu-id="440fb-327">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="440fb-328">Valores e parâmetros em cascata resolvem esse problema fornecendo uma maneira conveniente para um componente ancestral fornecer um valor para todos os seus componentes descendentes.</span><span class="sxs-lookup"><span data-stu-id="440fb-328">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="440fb-329">Valores e parâmetros em cascata também fornecem uma abordagem para que os componentes sejam coordenados.</span><span class="sxs-lookup"><span data-stu-id="440fb-329">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="440fb-330">Exemplo de tema</span><span class="sxs-lookup"><span data-stu-id="440fb-330">Theme example</span></span>

<span data-ttu-id="440fb-331">No exemplo a seguir do aplicativo de exemplo, a classe `ThemeInfo` especifica as informações do tema para fluir para baixo na hierarquia do componente para que todos os botões de uma determinada parte do aplicativo compartilhem o mesmo estilo.</span><span class="sxs-lookup"><span data-stu-id="440fb-331">In the following example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="440fb-332">*UIThemeClasses/ThemeInfo. cs*:</span><span class="sxs-lookup"><span data-stu-id="440fb-332">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="440fb-333">Um componente ancestral pode fornecer um valor em cascata usando o componente de valor em cascata.</span><span class="sxs-lookup"><span data-stu-id="440fb-333">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="440fb-334">O componente `CascadingValue` encapsula uma subárvore da hierarquia de componentes e fornece um único valor para todos os componentes dentro dessa subárvore.</span><span class="sxs-lookup"><span data-stu-id="440fb-334">The `CascadingValue` component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="440fb-335">Por exemplo, o aplicativo de exemplo especifica informações de tema (`ThemeInfo`) em um dos layouts do aplicativo como um parâmetro em cascata para todos os componentes que compõem o corpo do layout da propriedade `@Body`.</span><span class="sxs-lookup"><span data-stu-id="440fb-335">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> <span data-ttu-id="440fb-336">`ButtonClass` é atribuído um valor de `btn-success` no componente de layout.</span><span class="sxs-lookup"><span data-stu-id="440fb-336">`ButtonClass` is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="440fb-337">Qualquer componente descendente pode consumir essa propriedade por meio do `ThemeInfo` objeto em cascata.</span><span class="sxs-lookup"><span data-stu-id="440fb-337">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="440fb-338">`CascadingValuesParametersLayout` componente:</span><span class="sxs-lookup"><span data-stu-id="440fb-338">`CascadingValuesParametersLayout` component:</span></span>

```razor
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="_theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo _theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

<span data-ttu-id="440fb-339">Para fazer uso de valores em cascata, os componentes declaram parâmetros em cascata usando o atributo `[CascadingParameter]`.</span><span class="sxs-lookup"><span data-stu-id="440fb-339">To make use of cascading values, components declare cascading parameters using the `[CascadingParameter]` attribute.</span></span> <span data-ttu-id="440fb-340">Os valores em cascata são associados a parâmetros em cascata por tipo.</span><span class="sxs-lookup"><span data-stu-id="440fb-340">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="440fb-341">No aplicativo de exemplo, o componente `CascadingValuesParametersTheme` associa o valor de `ThemeInfo` em cascata a um parâmetro em cascata.</span><span class="sxs-lookup"><span data-stu-id="440fb-341">In the sample app, the `CascadingValuesParametersTheme` component binds the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="440fb-342">O parâmetro é usado para definir a classe CSS para um dos botões exibidos pelo componente.</span><span class="sxs-lookup"><span data-stu-id="440fb-342">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="440fb-343">`CascadingValuesParametersTheme` componente:</span><span class="sxs-lookup"><span data-stu-id="440fb-343">`CascadingValuesParametersTheme` component:</span></span>

```razor
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @_currentCount</p>

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
    private int _currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        _currentCount++;
    }
}
```

<span data-ttu-id="440fb-344">Para propagar vários valores do mesmo tipo dentro da mesma subárvore, forneça uma cadeia de caracteres `Name` exclusiva para cada componente `CascadingValue` e sua `CascadingParameter`correspondente.</span><span class="sxs-lookup"><span data-stu-id="440fb-344">To cascade multiple values of the same type within the same subtree, provide a unique `Name` string to each `CascadingValue` component and its corresponding `CascadingParameter`.</span></span> <span data-ttu-id="440fb-345">No exemplo a seguir, dois componentes `CascadingValue` em cascata diferentes instâncias de `MyCascadingType` por nome:</span><span class="sxs-lookup"><span data-stu-id="440fb-345">In the following example, two `CascadingValue` components cascade different instances of `MyCascadingType` by name:</span></span>

```razor
<CascadingValue Value=@_parentCascadeParameter1 Name="CascadeParam1">
    <CascadingValue Value=@ParentCascadeParameter2 Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType _parentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

<span data-ttu-id="440fb-346">Em um componente descendente, os parâmetros em cascata recebem seus valores dos valores em cascata correspondentes no componente ancestral por nome:</span><span class="sxs-lookup"><span data-stu-id="440fb-346">In a descendant component, the cascaded parameters receive their values from the corresponding cascaded values in the ancestor component by name:</span></span>

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a><span data-ttu-id="440fb-347">Exemplo de TabSet</span><span class="sxs-lookup"><span data-stu-id="440fb-347">TabSet example</span></span>

<span data-ttu-id="440fb-348">Os parâmetros em cascata também permitem que os componentes colaborem na hierarquia do componente.</span><span class="sxs-lookup"><span data-stu-id="440fb-348">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="440fb-349">Por exemplo, considere o exemplo de *TabSet* a seguir no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="440fb-349">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="440fb-350">O aplicativo de exemplo tem uma interface `ITab` que as guias implementam:</span><span class="sxs-lookup"><span data-stu-id="440fb-350">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

<span data-ttu-id="440fb-351">O componente `CascadingValuesParametersTabSet` usa o componente `TabSet`, que contém vários componentes `Tab`:</span><span class="sxs-lookup"><span data-stu-id="440fb-351">The `CascadingValuesParametersTabSet` component uses the `TabSet` component, which contains several `Tab` components:</span></span>

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

<span data-ttu-id="440fb-352">Os componentes de `Tab` filhos não são passados explicitamente como parâmetros para o `TabSet`.</span><span class="sxs-lookup"><span data-stu-id="440fb-352">The child `Tab` components aren't explicitly passed as parameters to the `TabSet`.</span></span> <span data-ttu-id="440fb-353">Em vez disso, os componentes de `Tab` filhos fazem parte do conteúdo filho do `TabSet`.</span><span class="sxs-lookup"><span data-stu-id="440fb-353">Instead, the child `Tab` components are part of the child content of the `TabSet`.</span></span> <span data-ttu-id="440fb-354">No entanto, o `TabSet` ainda precisa saber sobre cada componente `Tab` para que ele possa renderizar os cabeçalhos e a guia ativa. Para habilitar essa coordenação sem a necessidade de código adicional, o componente `TabSet` *pode fornecer a si mesmo como um valor em cascata* que é então coletado pelos componentes do `Tab` descendente.</span><span class="sxs-lookup"><span data-stu-id="440fb-354">However, the `TabSet` still needs to know about each `Tab` component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the `TabSet` component *can provide itself as a cascading value* that is then picked up by the descendent `Tab` components.</span></span>

<span data-ttu-id="440fb-355">`TabSet` componente:</span><span class="sxs-lookup"><span data-stu-id="440fb-355">`TabSet` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

<span data-ttu-id="440fb-356">Os componentes `Tab` descendentes capturam o `TabSet` recipiente como um parâmetro em cascata, de modo que os componentes `Tab` se adicionam à `TabSet` e coordenam em qual guia está ativa.</span><span class="sxs-lookup"><span data-stu-id="440fb-356">The descendent `Tab` components capture the containing `TabSet` as a cascading parameter, so the `Tab` components add themselves to the `TabSet` and coordinate on which tab is active.</span></span>

<span data-ttu-id="440fb-357">`Tab` componente:</span><span class="sxs-lookup"><span data-stu-id="440fb-357">`Tab` component:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a><span data-ttu-id="440fb-358">Modelos do Razor</span><span class="sxs-lookup"><span data-stu-id="440fb-358">Razor templates</span></span>

<span data-ttu-id="440fb-359">Os fragmentos de renderização podem ser definidos usando a sintaxe de modelo Razor.</span><span class="sxs-lookup"><span data-stu-id="440fb-359">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="440fb-360">Os modelos Razor são uma maneira de definir um trecho de interface do usuário e assumir o seguinte formato:</span><span class="sxs-lookup"><span data-stu-id="440fb-360">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="440fb-361">O exemplo a seguir ilustra como especificar `RenderFragment` e valores de `RenderFragment<T>` e renderizar modelos diretamente em um componente.</span><span class="sxs-lookup"><span data-stu-id="440fb-361">The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>` values and render templates directly in a component.</span></span> <span data-ttu-id="440fb-362">Os fragmentos de renderização também podem ser passados como argumentos para [componentes de modelo](xref:blazor/templated-components).</span><span class="sxs-lookup"><span data-stu-id="440fb-362">Render fragments can also be passed as arguments to [templated components](xref:blazor/templated-components).</span></span>

```razor
@_timeTemplate

@_petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment _timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> _petTemplate = (pet) => @<p>Pet: @pet.Name</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

<span data-ttu-id="440fb-363">Saída renderizada do código anterior:</span><span class="sxs-lookup"><span data-stu-id="440fb-363">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="440fb-364">Imagens SVG (gráficos vetoriais escaláveis)</span><span class="sxs-lookup"><span data-stu-id="440fb-364">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="440fb-365">Como Blazor renderiza imagens HTML, com suporte para navegador, incluindo imagens SVG (gráficos de vetor escalonáveis) ( *. svg*), há suporte por meio da marca `<img>`:</span><span class="sxs-lookup"><span data-stu-id="440fb-365">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (*.svg*), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="440fb-366">Da mesma forma, as imagens SVG têm suporte nas regras de CSS de um arquivo de folha de estilos ( *. css*):</span><span class="sxs-lookup"><span data-stu-id="440fb-366">Similarly, SVG images are supported in the CSS rules of a stylesheet file (*.css*):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="440fb-367">No entanto, a marcação SVG embutida não tem suporte em todos os cenários.</span><span class="sxs-lookup"><span data-stu-id="440fb-367">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="440fb-368">Se você posicionar uma marca de `<svg>` diretamente em um arquivo de componente ( *. Razor*), a renderização de imagem básica terá suporte, mas muitos cenários avançados ainda não têm suporte.</span><span class="sxs-lookup"><span data-stu-id="440fb-368">If you place an `<svg>` tag directly into a component file (*.razor*), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="440fb-369">Por exemplo, `<use>` marcas não são respeitadas atualmente e `@bind` não podem ser usadas com algumas marcas SVG.</span><span class="sxs-lookup"><span data-stu-id="440fb-369">For example, `<use>` tags aren't currently respected, and `@bind` can't be used with some SVG tags.</span></span> <span data-ttu-id="440fb-370">Esperamos abordar essas limitações em uma versão futura.</span><span class="sxs-lookup"><span data-stu-id="440fb-370">We expect to address these limitations in a future release.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="440fb-371">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="440fb-371">Additional resources</span></span>

* <span data-ttu-id="440fb-372"><xref:security/blazor/server> &ndash; inclui diretrizes sobre a criação de aplicativos do Blazor Server que devem combater o esgotamento de recursos.</span><span class="sxs-lookup"><span data-stu-id="440fb-372"><xref:security/blazor/server> &ndash; Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>
