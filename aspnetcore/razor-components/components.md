---
title: Criar e usar componentes do Razor
author: guardrex
description: Saiba como criar e usar componentes do Razor, incluindo como associar a dados, manipular eventos e gerenciar os ciclos de vida do componente.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2019
uid: razor-components/components
ms.openlocfilehash: f8ac7f3844b94a162e8d1c45f80ae153d89536ee
ms.sourcegitcommit: 948e533e02c2a7cb6175ada20b2c9cabb7786d0b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/10/2019
ms.locfileid: "59515376"
---
# <a name="create-and-use-razor-components"></a><span data-ttu-id="3f064-103">Criar e usar componentes do Razor</span><span class="sxs-lookup"><span data-stu-id="3f064-103">Create and use Razor Components</span></span>

<span data-ttu-id="3f064-104">Por [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), e [Morné Zaayman](https://github.com/MorneZaayman)</span><span class="sxs-lookup"><span data-stu-id="3f064-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Morné Zaayman](https://github.com/MorneZaayman)</span></span>

<span data-ttu-id="3f064-105">[Exibir ou baixar um código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-components/common/samples/) ([como baixar](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="3f064-105">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-components/common/samples/) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="3f064-106">Veja o tópico [Introdução](xref:razor-components/get-started) para conhecer os pré-requisitos.</span><span class="sxs-lookup"><span data-stu-id="3f064-106">See the [Get started](xref:razor-components/get-started) topic for prerequisites.</span></span>

<span data-ttu-id="3f064-107">Aplicativos de componentes do Razor são criados usando *componentes*.</span><span class="sxs-lookup"><span data-stu-id="3f064-107">Razor Components apps are built using *components*.</span></span> <span data-ttu-id="3f064-108">Um componente é uma parte independente da interface de usuário (UI), como uma página, caixa de diálogo ou formulário.</span><span class="sxs-lookup"><span data-stu-id="3f064-108">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="3f064-109">Um componente inclui a marcação HTML e a lógica de processamento necessário para injetar dados ou responder a eventos de interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="3f064-109">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="3f064-110">Componentes são simples e flexível.</span><span class="sxs-lookup"><span data-stu-id="3f064-110">Components are flexible and lightweight.</span></span> <span data-ttu-id="3f064-111">Eles podem ser aninhados, reutilizados e compartilhados entres projetos.</span><span class="sxs-lookup"><span data-stu-id="3f064-111">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="3f064-112">Classes de componentes</span><span class="sxs-lookup"><span data-stu-id="3f064-112">Component classes</span></span>

<span data-ttu-id="3f064-113">Componentes são normalmente implementados em arquivos de componente do Razor (*.razor*) usando uma combinação de C# e a marcação HTML (*. cshtml* arquivos são usados em aplicativos Blazor).</span><span class="sxs-lookup"><span data-stu-id="3f064-113">Components are typically implemented in Razor Component files (*.razor*) using a combination of C# and HTML markup (*.cshtml* files are used in Blazor apps).</span></span>

<span data-ttu-id="3f064-114">Componentes que podem ser criados em aplicativos de componentes do Razor usando o *. cshtml* extensão de arquivo, desde que os arquivos são identificados como arquivos de componente do Razor usando o `_RazorComponentInclude` propriedade do MSBuild.</span><span class="sxs-lookup"><span data-stu-id="3f064-114">Components can be authored in Razor Components apps using the *.cshtml* file extension as long as the files are identified as Razor Component files using the `_RazorComponentInclude` MSBuild property.</span></span> <span data-ttu-id="3f064-115">Por exemplo, um aplicativo criado usando o modelo de componente do Razor Especifica que todos os *. cshtml* arquivos sob o *componentes* pasta deve ser tratada como arquivos de componentes do Razor:</span><span class="sxs-lookup"><span data-stu-id="3f064-115">For example, an app created using the Razor Component template specifies that all *.cshtml* files under the *Components* folder should be treated as Razor Components files:</span></span>

```xml
<_RazorComponentInclude>Components\**\*.cshtml</_RazorComponentInclude>
```

<span data-ttu-id="3f064-116">A interface do usuário para um componente é definida usando HTML.</span><span class="sxs-lookup"><span data-stu-id="3f064-116">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="3f064-117">A lógica de renderização dinâmica (por exemplo, loops, condicionais, expressões) é adicionada usando uma sintaxe de C# inserida chamada [Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="3f064-117">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="3f064-118">Quando um aplicativo de componentes do Razor é compilado, a marcação HTML e C# lógica de renderização são convertidas em uma classe de componente.</span><span class="sxs-lookup"><span data-stu-id="3f064-118">When a Razor Components app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="3f064-119">O nome da classe gerada corresponde ao nome do arquivo.</span><span class="sxs-lookup"><span data-stu-id="3f064-119">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="3f064-120">Os membros da classe de componente são definidos em uma `@functions` bloco (mais de um `@functions` bloco é permitido).</span><span class="sxs-lookup"><span data-stu-id="3f064-120">Members of the component class are defined in a `@functions` block (more than one `@functions` block is permissible).</span></span> <span data-ttu-id="3f064-121">No `@functions` bloco, o estado do componente (propriedades, campos) é especificado com métodos para manipulação de eventos ou para definir outra lógica do componente.</span><span class="sxs-lookup"><span data-stu-id="3f064-121">In the `@functions` block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span>

<span data-ttu-id="3f064-122">Membros de componente, em seguida, podem ser usados como parte do componente de renderização do lógica usando C# expressões que começam com `@`.</span><span class="sxs-lookup"><span data-stu-id="3f064-122">Component members can then be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="3f064-123">Por exemplo, um C# é processado, prefixando `@` para o nome do campo.</span><span class="sxs-lookup"><span data-stu-id="3f064-123">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="3f064-124">O exemplo a seguir avalia e renderiza:</span><span class="sxs-lookup"><span data-stu-id="3f064-124">The following example evaluates and renders:</span></span>

* `_headingFontStyle` <span data-ttu-id="3f064-125">para o valor da propriedade CSS para `font-style`.</span><span class="sxs-lookup"><span data-stu-id="3f064-125">to the CSS property value for `font-style`.</span></span>
* `_headingText` <span data-ttu-id="3f064-126">para o conteúdo a `<h1>` elemento.</span><span class="sxs-lookup"><span data-stu-id="3f064-126">to the content of the `<h1>` element.</span></span>

```cshtml
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@functions {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="3f064-127">Depois que o componente inicialmente é renderizado, o componente gera novamente a sua árvore de renderização em resposta a eventos.</span><span class="sxs-lookup"><span data-stu-id="3f064-127">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> <span data-ttu-id="3f064-128">Componentes do Razor, em seguida, compara a nova árvore de renderização em relação a anterior e se aplica a todas as modificações do navegador modelo DOM (Document Object).</span><span class="sxs-lookup"><span data-stu-id="3f064-128">Razor Components then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

## <a name="integrate-components-into-razor-pages-and-mvc-apps"></a><span data-ttu-id="3f064-129">Integrar componentes em aplicativos de páginas do Razor e MVC</span><span class="sxs-lookup"><span data-stu-id="3f064-129">Integrate components into Razor Pages and MVC apps</span></span>

<span data-ttu-id="3f064-130">Use componentes com aplicativos existentes de páginas do Razor e MVC.</span><span class="sxs-lookup"><span data-stu-id="3f064-130">Use components with existing Razor Pages and MVC apps.</span></span> <span data-ttu-id="3f064-131">Não é necessário reescrever páginas existentes ou exibições a usar componentes do Razor.</span><span class="sxs-lookup"><span data-stu-id="3f064-131">There's no need to rewrite existing pages or views to use Razor Components.</span></span> <span data-ttu-id="3f064-132">Quando a página ou exibição é renderizada, componentes são pré-processadas&dagger; ao mesmo tempo.</span><span class="sxs-lookup"><span data-stu-id="3f064-132">When the page or view is rendered, components are prerendered&dagger; at the same time.</span></span> 

> [!NOTE]
> <span data-ttu-id="3f064-133">&dagger;Pré-processamento do lado do servidor está habilitado para aplicativos de componentes do Razor, por padrão.</span><span class="sxs-lookup"><span data-stu-id="3f064-133">&dagger;Server-side prerendering is enabled for Razor Components apps by default.</span></span> <span data-ttu-id="3f064-134">Aplicativos do lado do cliente Blazor suportará pré-processamento na próxima versão de visualização 4.</span><span class="sxs-lookup"><span data-stu-id="3f064-134">Client-side Blazor apps will support prerendering in the upcoming Preview 4 release.</span></span> <span data-ttu-id="3f064-135">Para obter mais informações, consulte [atualizar modelos/middleware usar MapFallbackToPage/arquivo](https://github.com/aspnet/AspNetCore/issues/8852).</span><span class="sxs-lookup"><span data-stu-id="3f064-135">For more information, see [Update templates/middleware to use MapFallbackToPage/File](https://github.com/aspnet/AspNetCore/issues/8852).</span></span>

<span data-ttu-id="3f064-136">Para renderizar um componente de uma página ou exibição, use o `RenderComponentAsync<TComponent>` método auxiliar HTML:</span><span class="sxs-lookup"><span data-stu-id="3f064-136">To render a component from a page or view, use the `RenderComponentAsync<TComponent>` HTML helper method:</span></span>

```cshtml
<div id="Counter">
    @(await Html.RenderComponentAsync<Counter>(new { IncrementAmount = 10 }))
</div>
```

<span data-ttu-id="3f064-137">Componentes processadas a partir de páginas e modos de exibição ainda não estão interativas na versão Preview 3.</span><span class="sxs-lookup"><span data-stu-id="3f064-137">Components rendered from pages and views aren't yet interactive in the Preview 3 release.</span></span> <span data-ttu-id="3f064-138">Por exemplo, a seleção de um botão não aciona uma chamada de método.</span><span class="sxs-lookup"><span data-stu-id="3f064-138">For example, selecting a button doesn't trigger a method call.</span></span> <span data-ttu-id="3f064-139">Uma visualização futura irá resolver essa limitação e adicionar suporte para componentes de processamento usando a sintaxe de elemento e atributo normal.</span><span class="sxs-lookup"><span data-stu-id="3f064-139">A future preview will address this limitation and add support for rendering components using the normal element and attribute syntax.</span></span>

<span data-ttu-id="3f064-140">Embora as páginas e modos de exibição podem usar componentes, o inverso não é true.</span><span class="sxs-lookup"><span data-stu-id="3f064-140">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="3f064-141">Componentes não é possível usar cenários de específico de página e de exibição, como exibições parciais e seções.</span><span class="sxs-lookup"><span data-stu-id="3f064-141">Components can't use view- and page-specific scenarios, such as partial views and sections.</span></span> <span data-ttu-id="3f064-142">Para usar uma lógica de exibição parcial em um componente, o fator a lógica de exibição parcial em um componente.</span><span class="sxs-lookup"><span data-stu-id="3f064-142">To use logic from partial view in a component, factor out the partial view logic into a component.</span></span>

## <a name="using-components"></a><span data-ttu-id="3f064-143">Usando componentes</span><span class="sxs-lookup"><span data-stu-id="3f064-143">Using components</span></span>

<span data-ttu-id="3f064-144">Os componentes podem incluir outros componentes, declarando-os usando a sintaxe de elemento HTML.</span><span class="sxs-lookup"><span data-stu-id="3f064-144">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="3f064-145">A marcação para uso de um componente é semelhante a uma marca HTML, em que o nome da marca é o tipo de componente.</span><span class="sxs-lookup"><span data-stu-id="3f064-145">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="3f064-146">A marcação a seguir renderiza um `HeadingComponent` (*HeadingComponent.cshtml*) instância:</span><span class="sxs-lookup"><span data-stu-id="3f064-146">The following markup renders a `HeadingComponent` (*HeadingComponent.cshtml*) instance:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/Index.cshtml?name=snippet_HeadingComponent)]

## <a name="component-parameters"></a><span data-ttu-id="3f064-147">Parâmetros do componente</span><span class="sxs-lookup"><span data-stu-id="3f064-147">Component parameters</span></span>

<span data-ttu-id="3f064-148">Componentes podem ter *parâmetros do componente*, que é definido usando *não públicos* as propriedades na classe de componente decoradas com `[Parameter]`.</span><span class="sxs-lookup"><span data-stu-id="3f064-148">Components can have *component parameters*, which are defined using *non-public* properties on the component class decorated with `[Parameter]`.</span></span> <span data-ttu-id="3f064-149">Use atributos para especificar argumentos para um componente na marcação.</span><span class="sxs-lookup"><span data-stu-id="3f064-149">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="3f064-150">No exemplo a seguir, o `ParentComponent` define o valor da `Title` propriedade do `ChildComponent`:</span><span class="sxs-lookup"><span data-stu-id="3f064-150">In the following example, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`:</span></span>

<span data-ttu-id="3f064-151">*ParentComponent.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="3f064-151">*ParentComponent.cshtml*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.cshtml?name=snippet_ParentComponent&highlight=5)]

<span data-ttu-id="3f064-152">*ChildComponent.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="3f064-152">*ChildComponent.cshtml*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ChildComponent.cshtml?highlight=7-8)]

## <a name="child-content"></a><span data-ttu-id="3f064-153">Conteúdo filho</span><span class="sxs-lookup"><span data-stu-id="3f064-153">Child content</span></span>

<span data-ttu-id="3f064-154">Componentes podem definir o conteúdo de outro componente.</span><span class="sxs-lookup"><span data-stu-id="3f064-154">Components can set the content of another component.</span></span> <span data-ttu-id="3f064-155">O componente de atribuição fornece o conteúdo entre as marcas que especificam o componente receptor.</span><span class="sxs-lookup"><span data-stu-id="3f064-155">The assigning component provides the content between the tags that specify the receiving component.</span></span> <span data-ttu-id="3f064-156">Por exemplo, uma `ParentComponent` pode fornecer conteúdo para a renderização por um componente filho, colocando o conteúdo dentro `<ChildComponent>` marcas.</span><span class="sxs-lookup"><span data-stu-id="3f064-156">For example, a `ParentComponent` can provide content for rendering by a Child component by placing the content inside `<ChildComponent>` tags.</span></span>

<span data-ttu-id="3f064-157">*ParentComponent.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="3f064-157">*ParentComponent.cshtml*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.cshtml?name=snippet_ParentComponent&highlight=6-7)]

<span data-ttu-id="3f064-158">O componente filho tem um `ChildContent` propriedade que representa um `RenderFragment`.</span><span class="sxs-lookup"><span data-stu-id="3f064-158">The Child component has a `ChildContent` property that represents a `RenderFragment`.</span></span> <span data-ttu-id="3f064-159">O valor de `ChildContent` é posicionado na marcação do componente filho em que o conteúdo deve ser renderizado.</span><span class="sxs-lookup"><span data-stu-id="3f064-159">The value of `ChildContent` is positioned in the child component's markup where the content should be rendered.</span></span> <span data-ttu-id="3f064-160">No exemplo a seguir, o valor de `ChildContent` é recebida do componente pai e renderizado dentro do painel Bootstrap `panel-body`.</span><span class="sxs-lookup"><span data-stu-id="3f064-160">In the following example, the value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="3f064-161">*ChildComponent.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="3f064-161">*ChildComponent.cshtml*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ChildComponent.cshtml?highlight=3,10-11)]

> [!NOTE]
> <span data-ttu-id="3f064-162">O recebimento de propriedade de `RenderFragment` conteúdo deve ser nomeado `ChildContent` por convenção.</span><span class="sxs-lookup"><span data-stu-id="3f064-162">The property receiving the `RenderFragment` content must be named `ChildContent` by convention.</span></span>

## <a name="data-binding"></a><span data-ttu-id="3f064-163">Associação de dados</span><span class="sxs-lookup"><span data-stu-id="3f064-163">Data binding</span></span>

<span data-ttu-id="3f064-164">Vinculação de dados a componentes e elementos DOM é feita com o `bind` atributo.</span><span class="sxs-lookup"><span data-stu-id="3f064-164">Data binding to both components and DOM elements is accomplished with the `bind` attribute.</span></span> <span data-ttu-id="3f064-165">O exemplo a seguir associa o `ItalicsCheck` estado selecionado de propriedade para a caixa de seleção:</span><span class="sxs-lookup"><span data-stu-id="3f064-165">The following example binds the `ItalicsCheck` property to the check box's checked state:</span></span>

```cshtml
<input type="checkbox" class="form-check-input" id="italicsCheck" 
    bind="@_italicsCheck">
```

<span data-ttu-id="3f064-166">Quando a caixa de seleção é marcada e desmarcada, o valor da propriedade é atualizado para `true` e `false`, respectivamente.</span><span class="sxs-lookup"><span data-stu-id="3f064-166">When the check box is selected and cleared, the property's value is updated to `true` and `false`, respectively.</span></span>

<span data-ttu-id="3f064-167">A caixa de seleção é atualizada na interface do usuário somente quando o componente é renderizado, não em resposta à alteração do valor da propriedade.</span><span class="sxs-lookup"><span data-stu-id="3f064-167">The check box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="3f064-168">Uma vez que os componentes se renderizar sozinhos após a execução de código do manipulador de eventos, as atualizações de propriedade são normalmente refletidas na interface do usuário imediatamente.</span><span class="sxs-lookup"><span data-stu-id="3f064-168">Since components render themselves after event handler code executes, property updates are usually reflected in the UI immediately.</span></span>

<span data-ttu-id="3f064-169">Usando o `bind` com um `CurrentValue` propriedade (`<input bind="@CurrentValue">`) é essencialmente equivalente ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="3f064-169">Using `bind` with a `CurrentValue` property (`<input bind="@CurrentValue">`) is essentially equivalent to the following:</span></span>

```cshtml
<input value="@CurrentValue" 
    onchange="@((UIChangeEventArgs __e) => CurrentValue = __e.Value)">
```

<span data-ttu-id="3f064-170">Quando o componente é renderizado, o `value` do elemento de entrada proveniente de `CurrentValue` propriedade.</span><span class="sxs-lookup"><span data-stu-id="3f064-170">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="3f064-171">Quando o usuário digita na caixa de texto, o `onchange` evento é acionado e o `CurrentValue` estiver definida como o valor alterado.</span><span class="sxs-lookup"><span data-stu-id="3f064-171">When the user types in the text box, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="3f064-172">Na realidade, a geração de código é um pouco mais complexo porque `bind` manipula alguns casos em que as conversões de tipo são executadas.</span><span class="sxs-lookup"><span data-stu-id="3f064-172">In reality, the code generation is a little more complex because `bind` handles a few cases where type conversions are performed.</span></span> <span data-ttu-id="3f064-173">Em princípio, `bind` associa o valor atual de uma expressão com um `value` atributo e manipula as alterações usando o manipulador registrado.</span><span class="sxs-lookup"><span data-stu-id="3f064-173">In principle, `bind` associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="3f064-174">Além `onchange`, a propriedade pode ser associada com o uso de outros eventos, como `oninput` por ser mais explícito sobre qual associar a:</span><span class="sxs-lookup"><span data-stu-id="3f064-174">In addition to `onchange`, the property can be bound using other events like `oninput` by being more explicit about what to bind to:</span></span>

```cshtml
<input type="text" bind-value-oninput="@CurrentValue">
```

<span data-ttu-id="3f064-175">Diferentemente `onchange`, `oninput` é acionado para cada caractere que é entrada na caixa de texto.</span><span class="sxs-lookup"><span data-stu-id="3f064-175">Unlike `onchange`, `oninput` fires for every character that is input into the text box.</span></span>

**<span data-ttu-id="3f064-176">Cadeias de caracteres de formato</span><span class="sxs-lookup"><span data-stu-id="3f064-176">Format strings</span></span>**

<span data-ttu-id="3f064-177">Associação de dados funciona com <xref:System.DateTime> cadeias de caracteres de formato.</span><span class="sxs-lookup"><span data-stu-id="3f064-177">Data binding works with <xref:System.DateTime> format strings.</span></span> <span data-ttu-id="3f064-178">Outras expressões de formato, como moeda ou formatos de número, não estão disponíveis no momento.</span><span class="sxs-lookup"><span data-stu-id="3f064-178">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```cshtml
<input bind="@StartDate" format-value="yyyy-MM-dd">

@functions {
    [Parameter]
    private DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="3f064-179">O `format-value` atributo especifica o formato de data para aplicar a `value` da `input` elemento.</span><span class="sxs-lookup"><span data-stu-id="3f064-179">The `format-value` attribute specifies the date format to apply to the `value` of the `input` element.</span></span> <span data-ttu-id="3f064-180">O formato também é usado para analisar o valor quando um `onchange` evento ocorre.</span><span class="sxs-lookup"><span data-stu-id="3f064-180">The format is also used to parse the value when an `onchange` event occurs.</span></span>

**<span data-ttu-id="3f064-181">Parâmetros do componente</span><span class="sxs-lookup"><span data-stu-id="3f064-181">Component parameters</span></span>**

<span data-ttu-id="3f064-182">Associação também reconhece componente parâmetros, onde `bind-{property}` pode associar um valor de propriedade entre componentes.</span><span class="sxs-lookup"><span data-stu-id="3f064-182">Binding also recognizes component parameters, where `bind-{property}` can bind a property value across components.</span></span>

<span data-ttu-id="3f064-183">O componente a seguir usa `ChildComponent` e associa o `ParentYear` parâmetro do pai para o `Year` parâmetro no componente de filho:</span><span class="sxs-lookup"><span data-stu-id="3f064-183">The following component uses `ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component:</span></span>

<span data-ttu-id="3f064-184">Componente pai:</span><span class="sxs-lookup"><span data-stu-id="3f064-184">Parent component:</span></span>

```cshtml
@page "/ParentComponent"

<h1>Parent Component</h1>

<p>ParentYear: @ParentYear</p>

<ChildComponent bind-Year="@ParentYear" />

<button class="btn btn-primary" onclick="@ChangeTheYear">
    Change Year to 1986
</button>

@functions {
    [Parameter]
    private int ParentYear { get; set; } = 1978;

    private void ChangeTheYear()
    {
        ParentYear = 1986;
    }
}
```

<span data-ttu-id="3f064-185">Componente de filho:</span><span class="sxs-lookup"><span data-stu-id="3f064-185">Child component:</span></span>

```cshtml
<h2>Child Component</h2>

<p>Year: @Year</p>

@functions {
    [Parameter]
    private int Year { get; set; }

    [Parameter]
    private Action<int> YearChanged { get; set; }
}
```

<span data-ttu-id="3f064-186">Carregando o `ParentComponent` produz a seguinte marcação:</span><span class="sxs-lookup"><span data-stu-id="3f064-186">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="3f064-187">Se o valor da `ParentYear` propriedade for alterada, selecionando o botão na `ParentComponent`, o `Year` propriedade do `ChildComponent` é atualizado.</span><span class="sxs-lookup"><span data-stu-id="3f064-187">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="3f064-188">O novo valor de `Year` é renderizado na interface do usuário quando o `ParentComponent` é renderizada:</span><span class="sxs-lookup"><span data-stu-id="3f064-188">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="3f064-189">O `Year` parâmetro é associável porque ele tem um complemento `YearChanged` que corresponda ao tipo de evento a `Year` parâmetro.</span><span class="sxs-lookup"><span data-stu-id="3f064-189">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="3f064-190">Por convenção, `<ChildComponent bind-Year="@ParentYear" />` é essencialmente equivalente ao escrito,</span><span class="sxs-lookup"><span data-stu-id="3f064-190">By convention, `<ChildComponent bind-Year="@ParentYear" />` is essentially equivalent to writing,</span></span>

```cshtml
    <ChildComponent bind-Year-YearChanged="@ParentYear" />
```

<span data-ttu-id="3f064-191">Em geral, uma propriedade pode ser associada a um correspondente manipulador de eventos usando `bind-property-event` atributo.</span><span class="sxs-lookup"><span data-stu-id="3f064-191">In general, a property can be bound to a corresponding event handler using `bind-property-event` attribute.</span></span>

## <a name="event-handling"></a><span data-ttu-id="3f064-192">Manipulação de eventos</span><span class="sxs-lookup"><span data-stu-id="3f064-192">Event handling</span></span>

<span data-ttu-id="3f064-193">Componentes do Razor fornecem recursos de manipulação de eventos.</span><span class="sxs-lookup"><span data-stu-id="3f064-193">Razor Components provide event handling features.</span></span> <span data-ttu-id="3f064-194">Para um atributo do elemento HTML denominado `on<event>` (por exemplo, `onclick`, `onsubmit`) com um valor de tipo de delegado, o Razor componentes trata o valor do atributo como um manipulador de eventos.</span><span class="sxs-lookup"><span data-stu-id="3f064-194">For an HTML element attribute named `on<event>` (for example, `onclick`, `onsubmit`) with a delegate-typed value, Razor Components treats the attribute's value as an event handler.</span></span> <span data-ttu-id="3f064-195">O nome do atributo sempre começa com `on`.</span><span class="sxs-lookup"><span data-stu-id="3f064-195">The attribute's name always starts with `on`.</span></span>

<span data-ttu-id="3f064-196">O código a seguir chama o `UpdateHeading` método quando o botão é selecionado na interface do usuário:</span><span class="sxs-lookup"><span data-stu-id="3f064-196">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

```cshtml
<button class="btn btn-primary" onclick="@UpdateHeading">
    Update heading
</button>

@functions {
    private void UpdateHeading(UIMouseEventArgs e)
    {
        ...
    }
}
```

<span data-ttu-id="3f064-197">O código a seguir chama o `CheckboxChanged` método quando a caixa de seleção é alterada na interface do usuário:</span><span class="sxs-lookup"><span data-stu-id="3f064-197">The following code calls the `CheckboxChanged` method when the check box is changed in the UI:</span></span>

```cshtml
<input type="checkbox" class="form-check-input" onchange="@CheckboxChanged">

@functions {
    private void CheckboxChanged()
    {
        ...
    }
}
```

<span data-ttu-id="3f064-198">Manipuladores de eventos também podem ser assíncrono e retornar um <xref:System.Threading.Tasks.Task>.</span><span class="sxs-lookup"><span data-stu-id="3f064-198">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="3f064-199">Não é necessário chamar manualmente `StateHasChanged()`.</span><span class="sxs-lookup"><span data-stu-id="3f064-199">There's no need to manually call `StateHasChanged()`.</span></span> <span data-ttu-id="3f064-200">As exceções são registradas quando eles ocorrerem.</span><span class="sxs-lookup"><span data-stu-id="3f064-200">Exceptions are logged when they occur.</span></span>

```cshtml
<button class="btn btn-primary" onclick="@UpdateHeading">
    Update heading
</button>

@functions {
    private async Task UpdateHeading(UIMouseEventArgs e)
    {
        ...
    }
}
```

<span data-ttu-id="3f064-201">Para alguns eventos, tipos de argumento de evento específicas de evento são permitidos.</span><span class="sxs-lookup"><span data-stu-id="3f064-201">For some events, event-specific event argument types are permitted.</span></span> <span data-ttu-id="3f064-202">Se o acesso a um desses tipos de evento não é necessário, ele não é necessário na chamada do método.</span><span class="sxs-lookup"><span data-stu-id="3f064-202">If access to one of these event types isn't necessary, it isn't required in the method call.</span></span>

<span data-ttu-id="3f064-203">A lista de argumentos de evento com suporte é:</span><span class="sxs-lookup"><span data-stu-id="3f064-203">The list of supported event arguments is:</span></span>

* <span data-ttu-id="3f064-204">UIEventArgs</span><span class="sxs-lookup"><span data-stu-id="3f064-204">UIEventArgs</span></span>
* <span data-ttu-id="3f064-205">UIChangeEventArgs</span><span class="sxs-lookup"><span data-stu-id="3f064-205">UIChangeEventArgs</span></span>
* <span data-ttu-id="3f064-206">UIKeyboardEventArgs</span><span class="sxs-lookup"><span data-stu-id="3f064-206">UIKeyboardEventArgs</span></span>
* <span data-ttu-id="3f064-207">UIMouseEventArgs</span><span class="sxs-lookup"><span data-stu-id="3f064-207">UIMouseEventArgs</span></span>

<span data-ttu-id="3f064-208">Expressões lambda também podem ser usadas:</span><span class="sxs-lookup"><span data-stu-id="3f064-208">Lambda expressions can also be used:</span></span>

```cshtml
<button onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="3f064-209">Muitas vezes é conveniente se fechar sobre valores adicionais, como ao iterar em um conjunto de elementos.</span><span class="sxs-lookup"><span data-stu-id="3f064-209">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="3f064-210">O exemplo a seguir cria três botões, cada um deles chama `UpdateHeading` passando um argumento de evento (`UIMouseEventArgs`) e seu número de botão (`buttonNumber`) quando a opção selecionada na interface do usuário:</span><span class="sxs-lookup"><span data-stu-id="3f064-210">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (`UIMouseEventArgs`) and its button number (`buttonNumber`) when selected in the UI:</span></span>

```cshtml
<h2>@message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@functions {
    private string message = "Select a button to learn its position.";

    private void UpdateHeading(UIMouseEventArgs e, int buttonNumber)
    {
        message = $"You selected Button #{buttonNumber} at " +
            "mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> <span data-ttu-id="3f064-211">Fazer **não** use a variável de loop (`i`) em um `for` loop diretamente em uma expressão lambda.</span><span class="sxs-lookup"><span data-stu-id="3f064-211">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="3f064-212">Caso contrário, a mesma variável é usada por todas as expressões lambda causando `i`do valor para ser o mesmo em todos os lambdas.</span><span class="sxs-lookup"><span data-stu-id="3f064-212">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="3f064-213">Sempre capture seu valor em uma variável local (`buttonNumber` no exemplo anterior) e, em seguida, usá-lo.</span><span class="sxs-lookup"><span data-stu-id="3f064-213">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

## <a name="capture-references-to-components"></a><span data-ttu-id="3f064-214">Capturar as referências a componentes</span><span class="sxs-lookup"><span data-stu-id="3f064-214">Capture references to components</span></span>

<span data-ttu-id="3f064-215">Referências de componentes fornecem uma maneira de obter uma referência a uma instância do componente, de modo que você pode emitir comandos para essa instância, como `Show` ou `Reset`.</span><span class="sxs-lookup"><span data-stu-id="3f064-215">Component references provide a way get a reference to a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="3f064-216">Para capturar uma referência de componente, adicione um `ref` de atributos para o componente filho e, em seguida, definir um campo com o mesmo nome e o mesmo tipo como o componente de filho.</span><span class="sxs-lookup"><span data-stu-id="3f064-216">To capture a component reference, add a `ref` attribute to the child component and then define a field with the same name and the same type as the child component.</span></span>

```cshtml
<MyLoginDialog ref="loginDialog" ... />

@functions {
    private MyLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

<span data-ttu-id="3f064-217">Quando o componente é renderizado, o `loginDialog` campo é preenchido com o `MyLoginDialog` instância do componente filho.</span><span class="sxs-lookup"><span data-stu-id="3f064-217">When the component is rendered, the `loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="3f064-218">Em seguida, você pode chamar métodos do .NET na instância do componente.</span><span class="sxs-lookup"><span data-stu-id="3f064-218">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="3f064-219">O `loginDialog` variável é populada somente depois que o componente é renderizado e sua saída inclui o `MyLoginDialog` elemento.</span><span class="sxs-lookup"><span data-stu-id="3f064-219">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="3f064-220">Até esse ponto, não há nada para fazer referência.</span><span class="sxs-lookup"><span data-stu-id="3f064-220">Until that point, there's nothing to reference.</span></span> <span data-ttu-id="3f064-221">Para manipular as referências a componentes depois que o componente tiver terminado a renderização, use o `OnAfterRenderAsync` ou `OnAfterRender` métodos.</span><span class="sxs-lookup"><span data-stu-id="3f064-221">To manipulate components references after the component has finished rendering, use the `OnAfterRenderAsync` or `OnAfterRender` methods.</span></span>

<span data-ttu-id="3f064-222">Enquanto referências de componente de captura usa uma sintaxe semelhante à [referências de elemento de captura](xref:razor-components/javascript-interop#capture-references-to-elements), não é um [JavaScript interoperabilidade](xref:razor-components/javascript-interop) recurso.</span><span class="sxs-lookup"><span data-stu-id="3f064-222">While capturing component references uses a similar syntax to [capturing element references](xref:razor-components/javascript-interop#capture-references-to-elements), it isn't a [JavaScript interop](xref:razor-components/javascript-interop) feature.</span></span> <span data-ttu-id="3f064-223">Referências de componentes não serão passadas para o código JavaScript; eles são usados apenas em código .NET.</span><span class="sxs-lookup"><span data-stu-id="3f064-223">Component references aren't passed to JavaScript code; they're only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="3f064-224">Fazer **não** usar referências de componentes para modificar o estado dos componentes de filho.</span><span class="sxs-lookup"><span data-stu-id="3f064-224">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="3f064-225">Em vez disso, use parâmetros declarativos normais para passar dados para componentes filho.</span><span class="sxs-lookup"><span data-stu-id="3f064-225">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="3f064-226">Isso faz com que os componentes de filho rerender automaticamente no horário correto.</span><span class="sxs-lookup"><span data-stu-id="3f064-226">This causes child components to rerender at the correct times automatically.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="3f064-227">Métodos de ciclo de vida</span><span class="sxs-lookup"><span data-stu-id="3f064-227">Lifecycle methods</span></span>

`OnInitAsync` <span data-ttu-id="3f064-228">e `OnInit` execute código para inicializar o componente.</span><span class="sxs-lookup"><span data-stu-id="3f064-228">and `OnInit` execute code to initialize the component.</span></span> <span data-ttu-id="3f064-229">Para executar uma operação assíncrona, use `OnInitAsync` e o `await` palavra-chave sobre a operação:</span><span class="sxs-lookup"><span data-stu-id="3f064-229">To perform an asynchronous operation, use `OnInitAsync` and the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitAsync()
{
    await ...
}
```

<span data-ttu-id="3f064-230">Para uma operação síncrona, use `OnInit`:</span><span class="sxs-lookup"><span data-stu-id="3f064-230">For a synchronous operation, use `OnInit`:</span></span>

```csharp
protected override void OnInit()
{
    ...
}
```

`OnParametersSetAsync` <span data-ttu-id="3f064-231">e `OnParametersSet` são chamados quando um componente recebeu parâmetros de seu pai e os valores são atribuídos às propriedades.</span><span class="sxs-lookup"><span data-stu-id="3f064-231">and `OnParametersSet` are called when a component has received parameters from its parent and the values are assigned to properties.</span></span> <span data-ttu-id="3f064-232">Esses métodos são executados após a inicialização do componente e, em seguida, cada vez que o componente é renderizado:</span><span class="sxs-lookup"><span data-stu-id="3f064-232">These methods are executed after component initialization and then each time the component is rendered:</span></span>

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

`OnAfterRenderAsync` <span data-ttu-id="3f064-233">e `OnAfterRender` são chamados depois que um componente tiver terminado a renderização.</span><span class="sxs-lookup"><span data-stu-id="3f064-233">and `OnAfterRender` are called after a component has finished rendering.</span></span> <span data-ttu-id="3f064-234">Referências de elemento e de componente são preenchidas no momento.</span><span class="sxs-lookup"><span data-stu-id="3f064-234">Element and component references are populated at this point.</span></span> <span data-ttu-id="3f064-235">Use esse estágio para executar etapas de inicialização adicionais usando o conteúdo renderizado, como ativar bibliotecas JavaScript de terceiros que operam em elementos DOM processados.</span><span class="sxs-lookup"><span data-stu-id="3f064-235">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

```csharp
protected override async Task OnAfterRenderAsync()
{
    await ...
}
```

```csharp
protected override void OnAfterRender()
{
    ...
}
```

`SetParameters` <span data-ttu-id="3f064-236">pode ser substituído para executar código antes que os parâmetros são definidos:</span><span class="sxs-lookup"><span data-stu-id="3f064-236">can be overridden to execute code before parameters are set:</span></span>

```csharp
public override void SetParameters(ParameterCollection parameters)
{
    ...

    base.SetParameters(parameters);
}
```

<span data-ttu-id="3f064-237">Se `base.SetParameters` não é invocado, o código personalizado pode interpretar o valor de parâmetros de entrada em qualquer forma alguma é necessária.</span><span class="sxs-lookup"><span data-stu-id="3f064-237">If `base.SetParameters` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="3f064-238">Por exemplo, os parâmetros de entrada não precisam ser atribuídos às propriedades na classe.</span><span class="sxs-lookup"><span data-stu-id="3f064-238">For example, the incoming parameters aren't required to be assigned to the properties on the class.</span></span>

`ShouldRender` <span data-ttu-id="3f064-239">pode ser substituído para suprimir a atualização da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="3f064-239">can be overridden to suppress refreshing of the UI.</span></span> <span data-ttu-id="3f064-240">Se a implementação retorna `true`, a interface do usuário é atualizada.</span><span class="sxs-lookup"><span data-stu-id="3f064-240">If the implementation returns `true`, the UI is refreshed.</span></span> <span data-ttu-id="3f064-241">Mesmo se `ShouldRender` é substituído, o componente sempre inicialmente é renderizado.</span><span class="sxs-lookup"><span data-stu-id="3f064-241">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="3f064-242">Descarte de componente com IDisposable</span><span class="sxs-lookup"><span data-stu-id="3f064-242">Component disposal with IDisposable</span></span>

<span data-ttu-id="3f064-243">Se um componente implementa <xref:System.IDisposable>, o [método Dispose](/dotnet/standard/garbage-collection/implementing-dispose) é chamado quando o componente é removido da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="3f064-243">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="3f064-244">O componente a seguir usa `@implements IDisposable` e o `Dispose` método:</span><span class="sxs-lookup"><span data-stu-id="3f064-244">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

```csharp
@using System
@implements IDisposable

...

@functions {
    public void Dispose()
    {
        ...
    }
}
```

## <a name="routing"></a><span data-ttu-id="3f064-245">Roteamento</span><span class="sxs-lookup"><span data-stu-id="3f064-245">Routing</span></span>

<span data-ttu-id="3f064-246">Roteamento em componentes do Razor é obtido, fornecendo um modelo de rota para cada componente acessível no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3f064-246">Routing in Razor Components is achieved by providing a route template to each accessible component in the app.</span></span>

<span data-ttu-id="3f064-247">Quando um *. cshtml* do arquivo com um `@page` diretiva é compilada, a classe gerada recebe um <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> especificando o modelo de rota.</span><span class="sxs-lookup"><span data-stu-id="3f064-247">When a *.cshtml* file with an `@page` directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="3f064-248">Em tempo de execução, o roteador procura por classes de componente com um `RouteAttribute` e renderiza a qualquer componente tem um modelo de rota que corresponde à URL solicitada.</span><span class="sxs-lookup"><span data-stu-id="3f064-248">At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.</span></span>

<span data-ttu-id="3f064-249">Vários modelos de rota podem ser aplicados a um componente.</span><span class="sxs-lookup"><span data-stu-id="3f064-249">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="3f064-250">O componente a seguir responde a solicitações para `/BlazorRoute` e `/DifferentBlazorRoute`:</span><span class="sxs-lookup"><span data-stu-id="3f064-250">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.cshtml?name=snippet_BlazorRoute)]

## <a name="route-parameters"></a><span data-ttu-id="3f064-251">Parâmetros de rota</span><span class="sxs-lookup"><span data-stu-id="3f064-251">Route parameters</span></span>

<span data-ttu-id="3f064-252">Componentes podem receber parâmetros de rota do modelo de rota fornecido no `@page` diretiva.</span><span class="sxs-lookup"><span data-stu-id="3f064-252">Components can receive route parameters from the route template provided in the `@page` directive.</span></span> <span data-ttu-id="3f064-253">O roteador usa parâmetros de rota para preencher os parâmetros correspondentes do componente.</span><span class="sxs-lookup"><span data-stu-id="3f064-253">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="3f064-254">*RouteParameter.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="3f064-254">*RouteParameter.cshtml*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.cshtml?name=snippet_RouteParameter)]

<span data-ttu-id="3f064-255">Não há suporte para parâmetros opcionais, portanto, dois `@page` diretivas são aplicadas no exemplo acima.</span><span class="sxs-lookup"><span data-stu-id="3f064-255">Optional parameters aren't supported, so two `@page` directives are applied in the example above.</span></span> <span data-ttu-id="3f064-256">A primeira permite a navegação para o componente sem um parâmetro.</span><span class="sxs-lookup"><span data-stu-id="3f064-256">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="3f064-257">A segunda `@page` diretiva leva a `{text}` parâmetro de rota e atribui o valor para o `Text` propriedade.</span><span class="sxs-lookup"><span data-stu-id="3f064-257">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="base-class-inheritance-for-a-code-behind-experience"></a><span data-ttu-id="3f064-258">Herança de classe base para uma experiência de "" code-behind</span><span class="sxs-lookup"><span data-stu-id="3f064-258">Base class inheritance for a "code-behind" experience</span></span>

<span data-ttu-id="3f064-259">Arquivos de componente (*. cshtml*) misturar uma marcação HTML e C# processamento de código no mesmo arquivo.</span><span class="sxs-lookup"><span data-stu-id="3f064-259">Component files (*.cshtml*) mix HTML markup and C# processing code in the same file.</span></span> <span data-ttu-id="3f064-260">O `@inherits` diretiva pode ser usada para fornecer uma experiência de "" code-behind que separa a marcação de componente de código de processamento de aplicativos de componentes do Razor.</span><span class="sxs-lookup"><span data-stu-id="3f064-260">The `@inherits` directive can be used to provide Razor Components apps with a "code-behind" experience that separates component markup from processing code.</span></span>

<span data-ttu-id="3f064-261">O [aplicativo de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-components/common/samples/) mostra como um componente pode herdar uma classe base, `BlazorRocksBase`, para fornecer métodos e propriedades do componente.</span><span class="sxs-lookup"><span data-stu-id="3f064-261">The [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-components/common/samples/) shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span>

<span data-ttu-id="3f064-262">*BlazorRocks.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="3f064-262">*BlazorRocks.cshtml*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRocks.cshtml?name=snippet_BlazorRocks)]

<span data-ttu-id="3f064-263">*BlazorRocksBase.cs*:</span><span class="sxs-lookup"><span data-stu-id="3f064-263">*BlazorRocksBase.cs*:</span></span>

[!code-csharp[](common/samples/3.x/BlazorSample/Pages/BlazorRocksBase.cs)]

<span data-ttu-id="3f064-264">A classe base deve derivar de `ComponentBase`.</span><span class="sxs-lookup"><span data-stu-id="3f064-264">The base class should derive from `ComponentBase`.</span></span>

## <a name="razor-support"></a><span data-ttu-id="3f064-265">Suporte para Razor</span><span class="sxs-lookup"><span data-stu-id="3f064-265">Razor support</span></span>

**<span data-ttu-id="3f064-266">Diretivas do Razor</span><span class="sxs-lookup"><span data-stu-id="3f064-266">Razor directives</span></span>**

<span data-ttu-id="3f064-267">Diretivas do Razor são mostradas na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="3f064-267">Razor directives are shown in the following table.</span></span>

| <span data-ttu-id="3f064-268">Diretiva</span><span class="sxs-lookup"><span data-stu-id="3f064-268">Directive</span></span> | <span data-ttu-id="3f064-269">Descrição</span><span class="sxs-lookup"><span data-stu-id="3f064-269">Description</span></span> |
| --------- | ----------- |
| [<span data-ttu-id="3f064-270">\@Funções</span><span class="sxs-lookup"><span data-stu-id="3f064-270">\@functions</span></span>](xref:mvc/views/razor#section-5) | <span data-ttu-id="3f064-271">Adiciona um C# bloco de código para um componente.</span><span class="sxs-lookup"><span data-stu-id="3f064-271">Adds a C# code block to a component.</span></span> |
| `@implements` | <span data-ttu-id="3f064-272">Implementa uma interface para a classe de componente gerado.</span><span class="sxs-lookup"><span data-stu-id="3f064-272">Implements an interface for the generated component class.</span></span> |
| [<span data-ttu-id="3f064-273">\@Herda</span><span class="sxs-lookup"><span data-stu-id="3f064-273">\@inherits</span></span>](xref:mvc/views/razor#section-3) | <span data-ttu-id="3f064-274">Fornece controle total sobre a classe que herda do componente.</span><span class="sxs-lookup"><span data-stu-id="3f064-274">Provides full control of the class that the component inherits.</span></span> |
| [<span data-ttu-id="3f064-275">\@injetar</span><span class="sxs-lookup"><span data-stu-id="3f064-275">\@inject</span></span>](xref:mvc/views/razor#section-4) | <span data-ttu-id="3f064-276">Permite a injeção de serviço a [contêiner de serviço](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="3f064-276">Enables service injection from the [service container](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="3f064-277">Para obter mais informações, consulte [Injeção de dependência em exibições](xref:mvc/views/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="3f064-277">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span> |
| `@layout` | <span data-ttu-id="3f064-278">Especifica um componente de layout.</span><span class="sxs-lookup"><span data-stu-id="3f064-278">Specifies a layout component.</span></span> <span data-ttu-id="3f064-279">Componentes de layout são usados para evitar inconsistência e a eliminação de duplicação de código.</span><span class="sxs-lookup"><span data-stu-id="3f064-279">Layout components are used to avoid code duplication and inconsistency.</span></span> |
| [<span data-ttu-id="3f064-280">\@Página</span><span class="sxs-lookup"><span data-stu-id="3f064-280">\@page</span></span>](xref:razor-pages/index#razor-pages) | <span data-ttu-id="3f064-281">Especifica que o componente deve tratar as solicitações diretamente.</span><span class="sxs-lookup"><span data-stu-id="3f064-281">Specifies that the component should handle requests directly.</span></span> <span data-ttu-id="3f064-282">O `@page` diretiva pode ser especificada com uma rota e os parâmetros opcionais.</span><span class="sxs-lookup"><span data-stu-id="3f064-282">The `@page` directive can be specified with a route and optional parameters.</span></span> <span data-ttu-id="3f064-283">Ao contrário de páginas do Razor, o `@page` diretiva não precisa ser a primeira diretiva na parte superior do arquivo.</span><span class="sxs-lookup"><span data-stu-id="3f064-283">Unlike Razor Pages, the `@page` directive doesn't need to be the first directive at the top of the file.</span></span> <span data-ttu-id="3f064-284">Para obter mais informações, consulte [Roteamento](xref:razor-components/routing).</span><span class="sxs-lookup"><span data-stu-id="3f064-284">For more information, see [Routing](xref:razor-components/routing).</span></span> |
| [<span data-ttu-id="3f064-285">\@Usando o</span><span class="sxs-lookup"><span data-stu-id="3f064-285">\@using</span></span>](xref:mvc/views/razor#using) | <span data-ttu-id="3f064-286">Adiciona o C# `using` diretriz para a classe de componente gerado.</span><span class="sxs-lookup"><span data-stu-id="3f064-286">Adds the C# `using` directive to the generated component class.</span></span> |
| [<span data-ttu-id="3f064-287">\@addTagHelper</span><span class="sxs-lookup"><span data-stu-id="3f064-287">\@addTagHelper</span></span>](xref:mvc/views/razor#tag-helpers) | <span data-ttu-id="3f064-288">Use `@addTagHelper` para usar um componente em um assembly diferente que o assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="3f064-288">Use `@addTagHelper` to use a component in a different assembly than the app's assembly.</span></span> |

**<span data-ttu-id="3f064-289">Atributos condicionais</span><span class="sxs-lookup"><span data-stu-id="3f064-289">Conditional attributes</span></span>**

<span data-ttu-id="3f064-290">Atributos condicionalmente são renderizados com base no valor do .NET.</span><span class="sxs-lookup"><span data-stu-id="3f064-290">Attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="3f064-291">Se o valor for `false` ou `null`, o atributo não é renderizado.</span><span class="sxs-lookup"><span data-stu-id="3f064-291">If the value is `false` or `null`,  the attribute isn't rendered.</span></span> <span data-ttu-id="3f064-292">Se o valor for `true`, o atributo é renderizado minimizada.</span><span class="sxs-lookup"><span data-stu-id="3f064-292">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="3f064-293">No exemplo a seguir `IsCompleted` determina se `checked` é renderizado na marcação do controle:</span><span class="sxs-lookup"><span data-stu-id="3f064-293">In the following example, `IsCompleted` determines if `checked` is rendered in the control's markup:</span></span>

```cshtml
<input type="checkbox" checked="@IsCompleted">

@functions {
    [Parameter]
    private bool IsCompleted { get; set; }
}
```

<span data-ttu-id="3f064-294">Se `IsCompleted` é `true`, a caixa de seleção é renderizada como:</span><span class="sxs-lookup"><span data-stu-id="3f064-294">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked>
```

<span data-ttu-id="3f064-295">Se `IsCompleted` é `false`, a caixa de seleção é renderizada como:</span><span class="sxs-lookup"><span data-stu-id="3f064-295">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox">
```

**<span data-ttu-id="3f064-296">Informações adicionais sobre o Razor</span><span class="sxs-lookup"><span data-stu-id="3f064-296">Additional information on Razor</span></span>**

<span data-ttu-id="3f064-297">Para obter mais informações sobre o Razor, consulte o [referência de sintaxe do Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="3f064-297">For more information on Razor, see the [Razor syntax reference](xref:mvc/views/razor).</span></span>

## <a name="raw-html"></a><span data-ttu-id="3f064-298">HTML bruto</span><span class="sxs-lookup"><span data-stu-id="3f064-298">Raw HTML</span></span>

<span data-ttu-id="3f064-299">Cadeias de caracteres normalmente são renderizadas usando nós de texto DOM, que significa que qualquer marcação, que elas podem conter é ignorada e tratada como texto literal.</span><span class="sxs-lookup"><span data-stu-id="3f064-299">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="3f064-300">Para renderizar HTML bruto, encapsular o conteúdo HTML em um `MarkupString` valor.</span><span class="sxs-lookup"><span data-stu-id="3f064-300">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="3f064-301">O valor é analisado como HTML ou SVG e inserido no DOM.</span><span class="sxs-lookup"><span data-stu-id="3f064-301">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="3f064-302">Renderização HTML bruto construído a partir de qualquer não confiáveis de origem é um **riscos de segurança** e deve ser evitado!</span><span class="sxs-lookup"><span data-stu-id="3f064-302">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="3f064-303">O exemplo a seguir mostra o uso de `MarkupString` tipo para adicionar um bloco de conteúdo do HTML estático para a saída renderizada de um componente:</span><span class="sxs-lookup"><span data-stu-id="3f064-303">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@functions {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="templated-components"></a><span data-ttu-id="3f064-304">Componentes com modelo</span><span class="sxs-lookup"><span data-stu-id="3f064-304">Templated components</span></span>

<span data-ttu-id="3f064-305">Componentes de modelo são que aceite um ou mais modelos de interface do usuário como parâmetros, que podem ser usados como parte da lógica de renderização do componente.</span><span class="sxs-lookup"><span data-stu-id="3f064-305">Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic.</span></span> <span data-ttu-id="3f064-306">Componentes com modelo permitem que você criar componentes de nível superior que são mais reutilizáveis que os componentes regulares.</span><span class="sxs-lookup"><span data-stu-id="3f064-306">Templated components allow you to author higher-level components that are more reusable than regular components.</span></span> <span data-ttu-id="3f064-307">Alguns exemplos incluem:</span><span class="sxs-lookup"><span data-stu-id="3f064-307">A couple of examples include:</span></span>

* <span data-ttu-id="3f064-308">Um componente de tabela que permite que um usuário especificar modelos de cabeçalho, linhas e rodapé da tabela.</span><span class="sxs-lookup"><span data-stu-id="3f064-308">A table component that allows a user to specify templates for the table's header, rows, and footer.</span></span>
* <span data-ttu-id="3f064-309">Um componente de lista que permite que um usuário especificar um modelo para itens de renderização em uma lista.</span><span class="sxs-lookup"><span data-stu-id="3f064-309">A list component that allows a user to specify a template for rendering items in a list.</span></span>

### <a name="template-parameters"></a><span data-ttu-id="3f064-310">Parâmetros de modelo</span><span class="sxs-lookup"><span data-stu-id="3f064-310">Template parameters</span></span>

<span data-ttu-id="3f064-311">Um componente de modelo é definido pela especificação de um ou mais parâmetros de componente do tipo `RenderFragment` ou `RenderFragment<T>`.</span><span class="sxs-lookup"><span data-stu-id="3f064-311">A templated component is defined by specifying one or more component parameters of type `RenderFragment` or `RenderFragment<T>`.</span></span> <span data-ttu-id="3f064-312">Um fragmento de renderização representa um segmento de interface do usuário que é processado pelo componente.</span><span class="sxs-lookup"><span data-stu-id="3f064-312">A render fragment represents a segment of UI that is rendered by the component.</span></span> <span data-ttu-id="3f064-313">Opcionalmente, um fragmento de renderização utiliza um parâmetro que pode ser especificado quando o fragmento de renderização é invocado.</span><span class="sxs-lookup"><span data-stu-id="3f064-313">A render fragment optionally takes a parameter that can be specified when the render fragment is invoked.</span></span>

<span data-ttu-id="3f064-314">*Components/TableTemplate.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="3f064-314">*Components/TableTemplate.cshtml*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TableTemplate.cshtml)]

<span data-ttu-id="3f064-315">Ao usar um componente de modelo, os parâmetros de modelo podem ser especificados usando os elementos filho que correspondem aos nomes dos parâmetros (`TableHeader` e `RowTemplate` no exemplo a seguir):</span><span class="sxs-lookup"><span data-stu-id="3f064-315">When using a templated component, the template parameters can be specified using child elements that match the names of the parameters (`TableHeader` and `RowTemplate` in the following example):</span></span>

```cshtml
<TableTemplate Items="@pets">
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

### <a name="template-context-parameters"></a><span data-ttu-id="3f064-316">Parâmetros de contexto do modelo</span><span class="sxs-lookup"><span data-stu-id="3f064-316">Template context parameters</span></span>

<span data-ttu-id="3f064-317">Os argumentos de tipo do componente `RenderFragment<T>` passado como elementos têm um parâmetro implícito chamado `context` (por exemplo, de exemplo de código anterior, `@context.PetId`), mas você pode alterar o nome de parâmetro usando o `Context` atributo no filho elemento.</span><span class="sxs-lookup"><span data-stu-id="3f064-317">Component arguments of type `RenderFragment<T>` passed as elements have an implicit parameter named `context` (for example from the preceding code sample, `@context.PetId`), but you can change the parameter name using the `Context` attribute on the child element.</span></span> <span data-ttu-id="3f064-318">No exemplo a seguir, o `RowTemplate` prvku `Context` atributo especifica a `pet` parâmetro:</span><span class="sxs-lookup"><span data-stu-id="3f064-318">In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:</span></span>

```cshtml
<TableTemplate Items="@pets">
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

<span data-ttu-id="3f064-319">Como alternativa, você pode especificar o `Context` atributo no elemento de componente.</span><span class="sxs-lookup"><span data-stu-id="3f064-319">Alternatively, you can specify the `Context` attribute on the component element.</span></span> <span data-ttu-id="3f064-320">Especificado `Context` atributo se aplica a todos os parâmetros de modelo especificado.</span><span class="sxs-lookup"><span data-stu-id="3f064-320">The specified `Context` attribute applies to all specified template parameters.</span></span> <span data-ttu-id="3f064-321">Isso pode ser útil quando você deseja especificar o nome do parâmetro de conteúdo para conteúdo filho implícita (sem qualquer disposição do elemento filho).</span><span class="sxs-lookup"><span data-stu-id="3f064-321">This can be useful when you want to specify the content parameter name for implicit child content (without any wrapping child element).</span></span> <span data-ttu-id="3f064-322">No exemplo a seguir, o `Context` atributo aparece no `TableTemplate` elemento e se aplica a todos os parâmetros de modelo:</span><span class="sxs-lookup"><span data-stu-id="3f064-322">In the following example, the `Context` attribute appears on the `TableTemplate` element and applies to all template parameters:</span></span>

```cshtml
<TableTemplate Items="@pets" Context="pet">
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

### <a name="generic-typed-components"></a><span data-ttu-id="3f064-323">Componentes de tipo genérico</span><span class="sxs-lookup"><span data-stu-id="3f064-323">Generic-typed components</span></span>

<span data-ttu-id="3f064-324">Componentes com modelo geralmente genericamente são tipados.</span><span class="sxs-lookup"><span data-stu-id="3f064-324">Templated components are often generically typed.</span></span> <span data-ttu-id="3f064-325">Por exemplo, um componente de modelo de exibição de lista genérico pode ser usado para renderizar `IEnumerable<T>` valores.</span><span class="sxs-lookup"><span data-stu-id="3f064-325">For example, a generic List View Template component can be used to render `IEnumerable<T>` values.</span></span> <span data-ttu-id="3f064-326">Para definir um componente genérico, use o `@typeparam` diretiva para especificar parâmetros de tipo.</span><span class="sxs-lookup"><span data-stu-id="3f064-326">To define a generic component, use the `@typeparam` directive to specify type parameters.</span></span>

<span data-ttu-id="3f064-327">*Components/ListViewTemplate.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="3f064-327">*Components/ListViewTemplate.cshtml*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ListViewTemplate.cshtml)]

<span data-ttu-id="3f064-328">Ao usar componentes de tipo genérico, o parâmetro de tipo é inferido se possível:</span><span class="sxs-lookup"><span data-stu-id="3f064-328">When using generic-typed components, the type parameter is inferred if possible:</span></span>

```cshtml
<ListViewTemplate Items="@pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

<span data-ttu-id="3f064-329">Caso contrário, o parâmetro de tipo deve ser explicitamente especificado usando um atributo que corresponde ao nome do parâmetro de tipo.</span><span class="sxs-lookup"><span data-stu-id="3f064-329">Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter.</span></span> <span data-ttu-id="3f064-330">No exemplo a seguir, `TItem="Pet"` Especifica o tipo:</span><span class="sxs-lookup"><span data-stu-id="3f064-330">In the following example, `TItem="Pet"` specifies the type:</span></span>

```cshtml
<ListViewTemplate Items="@pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

## <a name="cascading-values-and-parameters"></a><span data-ttu-id="3f064-331">Parâmetros e valores em cascata</span><span class="sxs-lookup"><span data-stu-id="3f064-331">Cascading values and parameters</span></span>

<span data-ttu-id="3f064-332">Em alguns cenários, é inconveniente para dados de fluxo de um componente de ancestral a usar um componente descendente [parâmetros do componente](#component-parameters), especialmente quando há várias camadas de componente.</span><span class="sxs-lookup"><span data-stu-id="3f064-332">In some scenarios, it's inconvenient to flow data from an ancestor component to a descendent component using [component parameters](#component-parameters), especially when there are several component layers.</span></span> <span data-ttu-id="3f064-333">Parâmetros e valores em cascata resolvem esse problema, fornecendo uma maneira conveniente de um componente do ancestral fornecer um valor para todos os seus componentes descendentes.</span><span class="sxs-lookup"><span data-stu-id="3f064-333">Cascading values and parameters solve this problem by providing a convenient way for an ancestor component to provide a value to all of its descendent components.</span></span> <span data-ttu-id="3f064-334">Parâmetros e valores em cascata também fornecem uma abordagem para componentes coordenar.</span><span class="sxs-lookup"><span data-stu-id="3f064-334">Cascading values and parameters also provide an approach for components to coordinate.</span></span>

### <a name="theme-example"></a><span data-ttu-id="3f064-335">Exemplo de tema</span><span class="sxs-lookup"><span data-stu-id="3f064-335">Theme example</span></span>

<span data-ttu-id="3f064-336">A seguir *tema* exemplo de aplicativo de exemplo, o `ThemeInfo` classe especifica as informações de tema para toda a hierarquia do componente de fluxo para que todos os botões dentro de uma determinada parte do aplicativo compartilham o mesmo estilo.</span><span class="sxs-lookup"><span data-stu-id="3f064-336">In the following *Theme* example from the sample app, the `ThemeInfo` class specifies the theme information to flow down the component hierarchy so that all of the buttons within a given part of the app share the same style.</span></span>

<span data-ttu-id="3f064-337">*UIThemeClasses/ThemeInfo.cs*:</span><span class="sxs-lookup"><span data-stu-id="3f064-337">*UIThemeClasses/ThemeInfo.cs*:</span></span>

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

<span data-ttu-id="3f064-338">Um componente de ancestral pode fornecer um valor em cascata, usando o componente de valor em cascata.</span><span class="sxs-lookup"><span data-stu-id="3f064-338">An ancestor component can provide a cascading value using the Cascading Value component.</span></span> <span data-ttu-id="3f064-339">O componente de valor em cascata encapsula uma subárvore da hierarquia do componente e fornece um valor único para todos os componentes dentro nessa subárvore.</span><span class="sxs-lookup"><span data-stu-id="3f064-339">The Cascading Value component wraps a subtree of the component hierarchy and supplies a single value to all components within that subtree.</span></span>

<span data-ttu-id="3f064-340">Por exemplo, o aplicativo de exemplo especifica informações de tema (`ThemeInfo`) em um dos layouts do aplicativo como um parâmetro em cascata para todos os componentes que compõem o corpo do layout do `@Body` propriedade.</span><span class="sxs-lookup"><span data-stu-id="3f064-340">For example, the sample app specifies theme information (`ThemeInfo`) in one of the app's layouts as a cascading parameter for all components that make up the layout body of the `@Body` property.</span></span> `ButtonClass` <span data-ttu-id="3f064-341">é atribuído um valor de `btn-success` no componente de layout.</span><span class="sxs-lookup"><span data-stu-id="3f064-341">is assigned a value of `btn-success` in the layout component.</span></span> <span data-ttu-id="3f064-342">Qualquer componente descendente pode consumir essa propriedade através de `ThemeInfo` em cascata do objeto.</span><span class="sxs-lookup"><span data-stu-id="3f064-342">Any descendent component can consume this property through the `ThemeInfo` cascading object.</span></span>

<span data-ttu-id="3f064-343">*Shared/CascadingValuesParametersLayout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="3f064-343">*Shared/CascadingValuesParametersLayout.cshtml*:</span></span>

```cshtml
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="@theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@functions {
    private ThemeInfo theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

<span data-ttu-id="3f064-344">Para fazer uso de valores em cascata, usando os parâmetros em cascata os componentes declaram o `[CascadingParameter]` de atributo ou com base em um valor de nome de cadeia de caracteres:</span><span class="sxs-lookup"><span data-stu-id="3f064-344">To make use of cascading values, components declare cascading parameters using the `[CascadingParameter]` attribute or based on a string name value:</span></span>

```cshtml
<CascadingValue Value=@PermInfo Name="UserPermissions">...</CascadingValue>

[CascadingParameter(Name = "UserPermissions")]
private PermInfo Permissions { get; set; }
```

<span data-ttu-id="3f064-345">Associação com um valor de nome de cadeia de caracteres será relevante se você tiver vários valores em cascata do mesmo tipo e precisar para diferenciá-los dentro da mesma subárvore.</span><span class="sxs-lookup"><span data-stu-id="3f064-345">Binding with a string name value is relevant if you have multiple cascading values of the same type and need to differentiate them within the same subtree.</span></span>

<span data-ttu-id="3f064-346">Valores em cascata são associados aos parâmetros em cascata por tipo.</span><span class="sxs-lookup"><span data-stu-id="3f064-346">Cascading values are bound to cascading parameters by type.</span></span>

<span data-ttu-id="3f064-347">No aplicativo de exemplo, o componente de tema de parâmetros de valores em cascata vincula o `ThemeInfo` valor em cascata a um parâmetro em cascata.</span><span class="sxs-lookup"><span data-stu-id="3f064-347">In the sample app, the Cascading Values Parameters Theme component binds to the `ThemeInfo` cascading value to a cascading parameter.</span></span> <span data-ttu-id="3f064-348">O parâmetro é usado para definir a classe CSS para um dos botões exibidos pelo componente.</span><span class="sxs-lookup"><span data-stu-id="3f064-348">The parameter is used to set the CSS class for one of the buttons displayed by the component.</span></span>

<span data-ttu-id="3f064-349">*Pages/CascadingValuesParametersTheme.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="3f064-349">*Pages/CascadingValuesParametersTheme.cshtml*:</span></span>

```cshtml
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @currentCount</p>

<p>
    <button class="btn" onclick="@IncrementCount">
        Increment Counter (Unthemed)
    </button>
</p>

<p>
    <button class="btn @ThemeInfo.ButtonClass" onclick="@IncrementCount">
        Increment Counter (Themed)
    </button>
</p>

@functions {
    private int currentCount = 0;

    [CascadingParameter] protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

### <a name="tabset-example"></a><span data-ttu-id="3f064-350">Exemplo de conjunto de guias</span><span class="sxs-lookup"><span data-stu-id="3f064-350">TabSet example</span></span>

<span data-ttu-id="3f064-351">Parâmetros em cascata também habilitar componentes colaborar em toda a hierarquia do componente.</span><span class="sxs-lookup"><span data-stu-id="3f064-351">Cascading parameters also enable components to collaborate across the component hierarchy.</span></span> <span data-ttu-id="3f064-352">Por exemplo, considere o seguinte *conjunto de guias* exemplo no aplicativo de exemplo.</span><span class="sxs-lookup"><span data-stu-id="3f064-352">For example, consider the following *TabSet* example in the sample app.</span></span>

<span data-ttu-id="3f064-353">O aplicativo de exemplo possui um `ITab` interface que implementam de guias:</span><span class="sxs-lookup"><span data-stu-id="3f064-353">The sample app has an `ITab` interface that tabs implement:</span></span>

[!code-cs[](common/samples/3.x/BlazorSample/UIInterfaces/ITab.cs)]

<span data-ttu-id="3f064-354">O componente de conjunto em cascata de parâmetros de valores abas usa o componente de conjunto de guias, que contém vários componentes do guia:</span><span class="sxs-lookup"><span data-stu-id="3f064-354">The Cascading Values Parameters TabSet component uses the Tab Set component, which contains several Tab components:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/CascadingValuesParametersTabSet.cshtml?name=snippet_TabSet)]

<span data-ttu-id="3f064-355">Os componentes de guia filho não são explicitamente passados como parâmetros para o conjunto de guias.</span><span class="sxs-lookup"><span data-stu-id="3f064-355">The child Tab components aren't explicitly passed as parameters to the Tab Set.</span></span> <span data-ttu-id="3f064-356">Em vez disso, os componentes de guia filho fazem parte do conteúdo filho do conjunto de guias.</span><span class="sxs-lookup"><span data-stu-id="3f064-356">Instead, the child Tab components are part of the child content of the Tab Set.</span></span> <span data-ttu-id="3f064-357">No entanto, o conjunto de guias ainda precisa saber sobre cada componente do guia para que ele possa processar os cabeçalhos e a guia ativa. Para habilitar essa coordenação sem a necessidade de código adicional, o componente de conjunto de guias *pode fornecer em si como um valor em cascata* que é capturado pelos componentes do guia descendentes.</span><span class="sxs-lookup"><span data-stu-id="3f064-357">However, the Tab Set still needs to know about each Tab component so that it can render the headers and the active tab. To enable this coordination without requiring additional code, the Tab Set component *can provide itself as a cascading value* that is then picked up by the descendent Tab components.</span></span>

<span data-ttu-id="3f064-358">*Components/TabSet.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="3f064-358">*Components/TabSet.cshtml*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TabSet.cshtml)]

<span data-ttu-id="3f064-359">A captura de componentes de guia descendente guia contendo definido como um parâmetro em cascata, para que os componentes de guia serem adicionados ao conjunto de guias e coordenadas na guia que está ativo.</span><span class="sxs-lookup"><span data-stu-id="3f064-359">The descendent Tab components capture the containing Tab Set as a cascading parameter, so the Tab components add themselves to the Tab Set and coordinate on which tab is active.</span></span>

<span data-ttu-id="3f064-360">*Components/Tab.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="3f064-360">*Components/Tab.cshtml*:</span></span>

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/Tab.cshtml)]

## <a name="razor-templates"></a><span data-ttu-id="3f064-361">Modelos do Razor</span><span class="sxs-lookup"><span data-stu-id="3f064-361">Razor templates</span></span>

<span data-ttu-id="3f064-362">Renderizar fragmentos podem ser definidos usando a sintaxe de modelo do Razor.</span><span class="sxs-lookup"><span data-stu-id="3f064-362">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="3f064-363">Modelos do Razor são uma maneira de definir um trecho de código da interface do usuário e supor o seguinte formato:</span><span class="sxs-lookup"><span data-stu-id="3f064-363">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```cshtml
@<tag>...</tag>
```

<span data-ttu-id="3f064-364">O exemplo a seguir ilustra como especificar `RenderFragment` e `RenderFragment<T>` valores.</span><span class="sxs-lookup"><span data-stu-id="3f064-364">The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>` values.</span></span>

<span data-ttu-id="3f064-365">*RazorTemplates.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="3f064-365">*RazorTemplates.cshtml*:</span></span>

```cshtml
@{
    RenderFragment template = @<p>The time is @DateTime.Now.</p>;
    RenderFragment<Pet> petTemplate = (pet) => @<p>Your pet's name is @pet.Name.</p>;
}
```

<span data-ttu-id="3f064-366">Renderizar fragmentos definidos usando o Razor modelos podem ser passados como argumentos para componentes com modelo ou renderizados diretamente.</span><span class="sxs-lookup"><span data-stu-id="3f064-366">Render fragments defined using Razor templates can be passed as arguments to templated components or rendered directly.</span></span> <span data-ttu-id="3f064-367">Por exemplo, os modelos anteriores diretamente são processados com a seguinte marcação Razor:</span><span class="sxs-lookup"><span data-stu-id="3f064-367">For example, the previous templates are directly rendered with the following Razor markup:</span></span>

```cshtml
@template

@petTemplate(new Pet { Name = "Rex" })
```

<span data-ttu-id="3f064-368">Saída renderizada:</span><span class="sxs-lookup"><span data-stu-id="3f064-368">Rendered output:</span></span>

```
The time is 10/04/2018 01:26:52.

Your pet's name is Rex.
```

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="3f064-369">Lógica de RenderTreeBuilder manual</span><span class="sxs-lookup"><span data-stu-id="3f064-369">Manual RenderTreeBuilder logic</span></span>

`Microsoft.AspNetCore.Components.RenderTree` <span data-ttu-id="3f064-370">fornece métodos para manipular componentes e elementos, incluindo a criação de componentes manualmente no C# código.</span><span class="sxs-lookup"><span data-stu-id="3f064-370">provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="3f064-371">Uso de `RenderTreeBuilder` criar componentes é um cenário avançado.</span><span class="sxs-lookup"><span data-stu-id="3f064-371">Use of `RenderTreeBuilder` to create components is an advanced scenario.</span></span> <span data-ttu-id="3f064-372">Um componente malformado (por exemplo, uma marca de marcação não fechada) pode resultar em comportamento indefinido.</span><span class="sxs-lookup"><span data-stu-id="3f064-372">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="3f064-373">Considere o seguinte componente animal de estimação detalhes (*PetDetails.razor* nos componentes do Razor; *PetDetails.cshtml* em Blazor), que podem ser criado manualmente em outro componente:</span><span class="sxs-lookup"><span data-stu-id="3f064-373">Consider the following Pet Details component (*PetDetails.razor* in Razor Components; *PetDetails.cshtml* in Blazor), which can be manually built into another component:</span></span>

```cshtml
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote<p>

@functions
{
    [Parameter]
    string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="3f064-374">No exemplo a seguir, o loop no `CreateComponent` método gera três componentes de detalhes de animal de estimação.</span><span class="sxs-lookup"><span data-stu-id="3f064-374">In the following example, the loop in the `CreateComponent` method generates three Pet Details components.</span></span> <span data-ttu-id="3f064-375">Ao chamar `RenderTreeBuilder` métodos para criar os componentes (`OpenComponent` e `AddAttribute`), números de sequência são números de linha de código de origem.</span><span class="sxs-lookup"><span data-stu-id="3f064-375">When calling `RenderTreeBuilder` methods to create the components (`OpenComponent` and `AddAttribute`), sequence numbers are source code line numbers.</span></span> <span data-ttu-id="3f064-376">Os componentes de Razor depende de algoritmo de diferença nos números de sequência correspondente à linhas distintas do código, não são diferentes de chamar invocações.</span><span class="sxs-lookup"><span data-stu-id="3f064-376">The Razor Components difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="3f064-377">Durante a criação de um componente com `RenderTreeBuilder` métodos, codificar os argumentos para números de sequência.</span><span class="sxs-lookup"><span data-stu-id="3f064-377">When creating a component with `RenderTreeBuilder` methods, hardcode the arguments for sequence numbers.</span></span> **<span data-ttu-id="3f064-378">Usar um cálculo ou um contador para gerar o número de sequência pode levar a um desempenho ruim.</span><span class="sxs-lookup"><span data-stu-id="3f064-378">Using a calculation or counter to generate the sequence number can lead to poor performance.</span></span>**

<span data-ttu-id="3f064-379">Incorporado componente (*BuiltContent.razor* nos componentes do Razor; *BuiltContent.cshtml* em Blazor):</span><span class="sxs-lookup"><span data-stu-id="3f064-379">Built component (*BuiltContent.razor* in Razor Components; *BuiltContent.cshtml* in Blazor):</span></span>

```cshtml
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" onclick="@RenderComponent">
    Create three Pet Details components
</button>

@functions {
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
