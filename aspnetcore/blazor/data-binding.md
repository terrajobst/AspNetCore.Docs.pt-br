---
title: ASP.NET Core Blazor Associação de dados
author: guardrex
description: Saiba mais sobre cenários de ligação de dados para componentes e elementos DOM em aplicativos Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/24/2020
no-loc:
- Blazor
- SignalR
uid: blazor/data-binding
ms.openlocfilehash: 92377730b9d353a507ffd384710fb979affe7265
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78661283"
---
# <a name="aspnet-core-opno-locblazor-data-binding"></a><span data-ttu-id="064d8-103">ASP.NET Core Blazor Associação de dados</span><span class="sxs-lookup"><span data-stu-id="064d8-103">ASP.NET Core Blazor data binding</span></span>

<span data-ttu-id="064d8-104">De [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="064d8-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="064d8-105">A vinculação de dados para os componentes e os elementos DOM é realizada com o atributo [`@bind`](xref:mvc/views/razor#bind) .</span><span class="sxs-lookup"><span data-stu-id="064d8-105">Data binding to both components and DOM elements is accomplished with the [`@bind`](xref:mvc/views/razor#bind) attribute.</span></span> <span data-ttu-id="064d8-106">O exemplo a seguir associa uma propriedade `CurrentValue` ao valor da caixa de texto:</span><span class="sxs-lookup"><span data-stu-id="064d8-106">The following example binds a `CurrentValue` property to the text box's value:</span></span>

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="064d8-107">Quando a caixa de texto perde o foco, o valor da propriedade é atualizado.</span><span class="sxs-lookup"><span data-stu-id="064d8-107">When the text box loses focus, the property's value is updated.</span></span>

<span data-ttu-id="064d8-108">A caixa de texto é atualizada na interface do usuário somente quando o componente é renderizado, não em resposta à alteração do valor da propriedade.</span><span class="sxs-lookup"><span data-stu-id="064d8-108">The text box is updated in the UI only when the component is rendered, not in response to changing the property's value.</span></span> <span data-ttu-id="064d8-109">Como os componentes são renderizados após a execução do código do manipulador de eventos, as atualizações de propriedade *geralmente* são refletidas na interface do usuário imediatamente após um manipulador de eventos ser disparado.</span><span class="sxs-lookup"><span data-stu-id="064d8-109">Since components render themselves after event handler code executes, property updates are *usually* reflected in the UI immediately after an event handler is triggered.</span></span>

<span data-ttu-id="064d8-110">O uso de `@bind` com a propriedade `CurrentValue` (`<input @bind="CurrentValue" />`) é essencialmente equivalente ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="064d8-110">Using `@bind` with the `CurrentValue` property (`<input @bind="CurrentValue" />`) is essentially equivalent to the following:</span></span>

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="064d8-111">Quando o componente é renderizado, a `value` do elemento input é proveniente da propriedade `CurrentValue`.</span><span class="sxs-lookup"><span data-stu-id="064d8-111">When the component is rendered, the `value` of the input element comes from the `CurrentValue` property.</span></span> <span data-ttu-id="064d8-112">Quando o usuário digita na caixa de texto e altera o foco do elemento, o evento `onchange` é acionado e a propriedade `CurrentValue` é definida como o valor alterado.</span><span class="sxs-lookup"><span data-stu-id="064d8-112">When the user types in the text box and changes element focus, the `onchange` event is fired and the `CurrentValue` property is set to the changed value.</span></span> <span data-ttu-id="064d8-113">Na realidade, a geração de código é mais complexa porque `@bind` trata casos em que conversões de tipo são executadas.</span><span class="sxs-lookup"><span data-stu-id="064d8-113">In reality, the code generation is more complex because `@bind` handles cases where type conversions are performed.</span></span> <span data-ttu-id="064d8-114">Em princípio, `@bind` associa o valor atual de uma expressão a um atributo `value` e manipula as alterações usando o manipulador registrado.</span><span class="sxs-lookup"><span data-stu-id="064d8-114">In principle, `@bind` associates the current value of an expression with a `value` attribute and handles changes using the registered handler.</span></span>

<span data-ttu-id="064d8-115">Associe uma propriedade ou um campo a outros eventos, incluindo também um atributo de `@bind:event` com um parâmetro `event`.</span><span class="sxs-lookup"><span data-stu-id="064d8-115">Bind a property or field on other events by also including an `@bind:event` attribute with an `event` parameter.</span></span> <span data-ttu-id="064d8-116">O exemplo a seguir associa a propriedade `CurrentValue` no evento `oninput`:</span><span class="sxs-lookup"><span data-stu-id="064d8-116">The following example binds the `CurrentValue` property on the `oninput` event:</span></span>

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

<span data-ttu-id="064d8-117">Ao contrário de `onchange`, que é disparado quando o elemento perde o foco, `oninput` é acionado quando o valor da caixa de texto é alterado.</span><span class="sxs-lookup"><span data-stu-id="064d8-117">Unlike `onchange`, which fires when the element loses focus, `oninput` fires when the value of the text box changes.</span></span>

<span data-ttu-id="064d8-118">Use `@bind-{ATTRIBUTE}` com a sintaxe `@bind-{ATTRIBUTE}:event` para associar atributos de elemento diferentes de `value`.</span><span class="sxs-lookup"><span data-stu-id="064d8-118">Use `@bind-{ATTRIBUTE}` with `@bind-{ATTRIBUTE}:event` syntax to bind element attributes other than `value`.</span></span> <span data-ttu-id="064d8-119">No exemplo a seguir, o estilo do parágrafo é atualizado quando o valor de `_paragraphStyle` é alterado:</span><span class="sxs-lookup"><span data-stu-id="064d8-119">In the following example, the paragraph's style is updated when the `_paragraphStyle` value changes:</span></span>

```razor
@page "/binding-example"

<p>
    <input type="text" @bind="_paragraphStyle" />
</p>

<p @bind-style="_paragraphStyle" @bind-style:event="onchange">
    Blazorify the app!
</p>

@code {
    private string _paragraphStyle = "color:red";
}
```

## <a name="unparsable-values"></a><span data-ttu-id="064d8-120">Valores não analisáveis</span><span class="sxs-lookup"><span data-stu-id="064d8-120">Unparsable values</span></span>

<span data-ttu-id="064d8-121">Quando um usuário fornece um valor não analisável para um elemento de ligação de valores, o valor não analisável é revertido automaticamente para seu valor anterior quando o evento de ligação é disparado.</span><span class="sxs-lookup"><span data-stu-id="064d8-121">When a user provides an unparsable value to a databound element, the unparsable value is automatically reverted to its previous value when the bind event is triggered.</span></span>

<span data-ttu-id="064d8-122">Considere o seguinte cenário:</span><span class="sxs-lookup"><span data-stu-id="064d8-122">Consider the following scenario:</span></span>

* <span data-ttu-id="064d8-123">Um elemento `<input>` está associado a um tipo de `int` com um valor inicial de `123`:</span><span class="sxs-lookup"><span data-stu-id="064d8-123">An `<input>` element is bound to an `int` type with an initial value of `123`:</span></span>

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* <span data-ttu-id="064d8-124">O usuário atualiza o valor do elemento para `123.45` na página e altera o foco do elemento.</span><span class="sxs-lookup"><span data-stu-id="064d8-124">The user updates the value of the element to `123.45` in the page and changes the element focus.</span></span>

<span data-ttu-id="064d8-125">No cenário anterior, o valor do elemento é revertido para `123`.</span><span class="sxs-lookup"><span data-stu-id="064d8-125">In the preceding scenario, the element's value is reverted to `123`.</span></span> <span data-ttu-id="064d8-126">Quando o valor `123.45` é rejeitado em favor do valor original de `123`, o usuário entende que seu valor não foi aceito.</span><span class="sxs-lookup"><span data-stu-id="064d8-126">When the value `123.45` is rejected in favor of the original value of `123`, the user understands that their value wasn't accepted.</span></span>

<span data-ttu-id="064d8-127">Por padrão, a associação aplica-se ao evento de `onchange` do elemento (`@bind="{PROPERTY OR FIELD}"`).</span><span class="sxs-lookup"><span data-stu-id="064d8-127">By default, binding applies to the element's `onchange` event (`@bind="{PROPERTY OR FIELD}"`).</span></span> <span data-ttu-id="064d8-128">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` para disparar a associação em um evento diferente.</span><span class="sxs-lookup"><span data-stu-id="064d8-128">Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` to trigger binding on a different event.</span></span> <span data-ttu-id="064d8-129">Para o evento de `oninput` (`@bind:event="oninput"`), a reversão ocorre após qualquer pressionamento de tecla que introduz um valor não analisável.</span><span class="sxs-lookup"><span data-stu-id="064d8-129">For the `oninput` event (`@bind:event="oninput"`), the reversion occurs after any keystroke that introduces an unparsable value.</span></span> <span data-ttu-id="064d8-130">Ao direcionar o evento de `oninput` com um tipo de associação de `int`, um usuário é impedido de digitar um caractere `.`.</span><span class="sxs-lookup"><span data-stu-id="064d8-130">When targeting the `oninput` event with an `int`-bound type, a user is prevented from typing a `.` character.</span></span> <span data-ttu-id="064d8-131">Um caractere `.` é removido imediatamente e, portanto, o usuário recebe comentários imediatos de que apenas números inteiros são permitidos.</span><span class="sxs-lookup"><span data-stu-id="064d8-131">A `.` character is immediately removed, so the user receives immediate feedback that only whole numbers are permitted.</span></span> <span data-ttu-id="064d8-132">Há cenários em que a reversão do valor no evento `oninput` não é ideal, por exemplo, quando o usuário deve ter permissão para limpar um valor de `<input>` não analisável.</span><span class="sxs-lookup"><span data-stu-id="064d8-132">There are scenarios where reverting the value on the `oninput` event isn't ideal, such as when the user should be allowed to clear an unparsable `<input>` value.</span></span> <span data-ttu-id="064d8-133">As alternativas incluem:</span><span class="sxs-lookup"><span data-stu-id="064d8-133">Alternatives include:</span></span>

* <span data-ttu-id="064d8-134">Não use o evento `oninput`.</span><span class="sxs-lookup"><span data-stu-id="064d8-134">Don't use the `oninput` event.</span></span> <span data-ttu-id="064d8-135">Use o evento de `onchange` padrão (especifique apenas `@bind="{PROPERTY OR FIELD}"`), em que um valor inválido não é revertido até que o elemento perca o foco.</span><span class="sxs-lookup"><span data-stu-id="064d8-135">Use the default `onchange` event (only specify `@bind="{PROPERTY OR FIELD}"`), where an invalid value isn't reverted until the element loses focus.</span></span>
* <span data-ttu-id="064d8-136">Associar a um tipo anulável, como `int?` ou `string`, e fornecer uma lógica personalizada para manipular entradas inválidas.</span><span class="sxs-lookup"><span data-stu-id="064d8-136">Bind to a nullable type, such as `int?` or `string`, and provide custom logic to handle invalid entries.</span></span>
* <span data-ttu-id="064d8-137">Use um [componente de validação de formulário](xref:blazor/forms-validation), como `InputNumber` ou `InputDate`.</span><span class="sxs-lookup"><span data-stu-id="064d8-137">Use a [form validation component](xref:blazor/forms-validation), such as `InputNumber` or `InputDate`.</span></span> <span data-ttu-id="064d8-138">Os componentes de validação de formulário têm suporte interno para gerenciar entradas inválidas.</span><span class="sxs-lookup"><span data-stu-id="064d8-138">Form validation components have built-in support to manage invalid inputs.</span></span> <span data-ttu-id="064d8-139">Componentes de validação de formulário:</span><span class="sxs-lookup"><span data-stu-id="064d8-139">Form validation components:</span></span>
  * <span data-ttu-id="064d8-140">Permitir que o usuário forneça erros de entrada e de validação inválidos no `EditContext`associado.</span><span class="sxs-lookup"><span data-stu-id="064d8-140">Permit the user to provide invalid input and receive validation errors on the associated `EditContext`.</span></span>
  * <span data-ttu-id="064d8-141">Exibir erros de validação na interface de usuário sem interferir no usuário inserindo dados adicionais do WebForms.</span><span class="sxs-lookup"><span data-stu-id="064d8-141">Display validation errors in the UI without interfering with the user entering additional webform data.</span></span>

## <a name="format-strings"></a><span data-ttu-id="064d8-142">Formatar cadeias de caracteres</span><span class="sxs-lookup"><span data-stu-id="064d8-142">Format strings</span></span>

<span data-ttu-id="064d8-143">A vinculação de dados funciona com <xref:System.DateTime> cadeias de caracteres de formato usando [`@bind:format`](xref:mvc/views/razor#bind).</span><span class="sxs-lookup"><span data-stu-id="064d8-143">Data binding works with <xref:System.DateTime> format strings using [`@bind:format`](xref:mvc/views/razor#bind).</span></span> <span data-ttu-id="064d8-144">Outras expressões de formato, como formatos de moeda ou número, não estão disponíveis no momento.</span><span class="sxs-lookup"><span data-stu-id="064d8-144">Other format expressions, such as currency or number formats, aren't available at this time.</span></span>

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

<span data-ttu-id="064d8-145">No código anterior, o tipo de campo (`type`) do elemento `<input>` tem como padrão `text`.</span><span class="sxs-lookup"><span data-stu-id="064d8-145">In the preceding code, the `<input>` element's field type (`type`) defaults to `text`.</span></span> <span data-ttu-id="064d8-146">`@bind:format` tem suporte para ligar os seguintes tipos .NET:</span><span class="sxs-lookup"><span data-stu-id="064d8-146">`@bind:format` is supported for binding the following .NET types:</span></span>

* <xref:System.DateTime?displayProperty=fullName>
* <span data-ttu-id="064d8-147"><xref:System.DateTime?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="064d8-147"><xref:System.DateTime?displayProperty=fullName>?</span></span>
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <span data-ttu-id="064d8-148"><xref:System.DateTimeOffset?displayProperty=fullName>?</span><span class="sxs-lookup"><span data-stu-id="064d8-148"><xref:System.DateTimeOffset?displayProperty=fullName>?</span></span>

<span data-ttu-id="064d8-149">O atributo `@bind:format` especifica o formato de data a ser aplicado ao `value` do elemento `<input>`.</span><span class="sxs-lookup"><span data-stu-id="064d8-149">The `@bind:format` attribute specifies the date format to apply to the `value` of the `<input>` element.</span></span> <span data-ttu-id="064d8-150">O formato também é usado para analisar o valor quando ocorre um evento `onchange`.</span><span class="sxs-lookup"><span data-stu-id="064d8-150">The format is also used to parse the value when an `onchange` event occurs.</span></span>

<span data-ttu-id="064d8-151">Não é recomendável especificar um formato para o tipo de campo `date` porque Blazor tem suporte interno para formatar datas.</span><span class="sxs-lookup"><span data-stu-id="064d8-151">Specifying a format for the `date` field type isn't recommended because Blazor has built-in support to format dates.</span></span> <span data-ttu-id="064d8-152">Apesar da recomendação, use apenas o formato de data `yyyy-MM-dd` para que a associação funcione corretamente se um formato for fornecido com o tipo de campo `date`:</span><span class="sxs-lookup"><span data-stu-id="064d8-152">In spite of the recommendation, only use the `yyyy-MM-dd` date format for binding to work correctly if a format is supplied with the `date` field type:</span></span>

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a><span data-ttu-id="064d8-153">Associação de pai para filho com parâmetros de componente</span><span class="sxs-lookup"><span data-stu-id="064d8-153">Parent-to-child binding with component parameters</span></span>

<span data-ttu-id="064d8-154">A associação reconhece os parâmetros do componente, em que `@bind-{PROPERTY}` pode associar um valor de propriedade de um componente pai a um componente filho.</span><span class="sxs-lookup"><span data-stu-id="064d8-154">Binding recognizes component parameters, where `@bind-{PROPERTY}` can bind a property value from a parent component down to a child component.</span></span> <span data-ttu-id="064d8-155">A associação de um filho a um pai é abordada na [Associação filho para pai com a seção de ligação encadeada](#child-to-parent-binding-with-chained-bind) .</span><span class="sxs-lookup"><span data-stu-id="064d8-155">Binding from a child to a parent is covered in the [Child-to-parent binding with chained bind](#child-to-parent-binding-with-chained-bind) section.</span></span>

<span data-ttu-id="064d8-156">O componente filho a seguir (`ChildComponent`) tem um parâmetro de componente `Year` e `YearChanged` retorno de chamada:</span><span class="sxs-lookup"><span data-stu-id="064d8-156">The following child component (`ChildComponent`) has a `Year` component parameter and `YearChanged` callback:</span></span>

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

<span data-ttu-id="064d8-157">`EventCallback<T>` é explicado em <xref:blazor/event-handling#eventcallback>.</span><span class="sxs-lookup"><span data-stu-id="064d8-157">`EventCallback<T>` is explained in <xref:blazor/event-handling#eventcallback>.</span></span>

<span data-ttu-id="064d8-158">O seguinte componente pai usa:</span><span class="sxs-lookup"><span data-stu-id="064d8-158">The following parent component uses:</span></span>

* <span data-ttu-id="064d8-159">`ChildComponent` e associa o parâmetro `ParentYear` do pai ao parâmetro `Year` no componente filho.</span><span class="sxs-lookup"><span data-stu-id="064d8-159">`ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component.</span></span>
* <span data-ttu-id="064d8-160">O evento `onclick` é usado para disparar o método `ChangeTheYear`.</span><span class="sxs-lookup"><span data-stu-id="064d8-160">The `onclick` event is used to trigger the `ChangeTheYear` method.</span></span> <span data-ttu-id="064d8-161">Para obter mais informações, consulte <xref:blazor/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="064d8-161">For more information, see <xref:blazor/event-handling>.</span></span>

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

<span data-ttu-id="064d8-162">Carregar o `ParentComponent` produz a seguinte marcação:</span><span class="sxs-lookup"><span data-stu-id="064d8-162">Loading the `ParentComponent` produces the following markup:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

<span data-ttu-id="064d8-163">Se o valor da propriedade `ParentYear` for alterado selecionando o botão na `ParentComponent`, a propriedade `Year` do `ChildComponent` será atualizada.</span><span class="sxs-lookup"><span data-stu-id="064d8-163">If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated.</span></span> <span data-ttu-id="064d8-164">O novo valor de `Year` é renderizado na interface do usuário quando a `ParentComponent` é reprocessada:</span><span class="sxs-lookup"><span data-stu-id="064d8-164">The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:</span></span>

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

<span data-ttu-id="064d8-165">O parâmetro `Year` é acoplável porque tem um evento complementar `YearChanged` que corresponde ao tipo do parâmetro `Year`.</span><span class="sxs-lookup"><span data-stu-id="064d8-165">The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.</span></span>

<span data-ttu-id="064d8-166">Por convenção, `<ChildComponent @bind-Year="ParentYear" />` é essencialmente equivalente a escrever:</span><span class="sxs-lookup"><span data-stu-id="064d8-166">By convention, `<ChildComponent @bind-Year="ParentYear" />` is essentially equivalent to writing:</span></span>

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

<span data-ttu-id="064d8-167">Em geral, uma propriedade pode ser associada a um manipulador de eventos correspondente, incluindo um atributo `@bind-{PROPRETY}:event`.</span><span class="sxs-lookup"><span data-stu-id="064d8-167">In general, a property can be bound to a corresponding event handler by including an `@bind-{PROPRETY}:event` attribute.</span></span> <span data-ttu-id="064d8-168">Por exemplo, a propriedade `MyProp` pode ser associada a `MyEventHandler` usando os dois atributos a seguir:</span><span class="sxs-lookup"><span data-stu-id="064d8-168">For example, the property `MyProp` can be bound to `MyEventHandler` using the following two attributes:</span></span>

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a><span data-ttu-id="064d8-169">Associação de filho para pai com associação encadeada</span><span class="sxs-lookup"><span data-stu-id="064d8-169">Child-to-parent binding with chained bind</span></span>

<span data-ttu-id="064d8-170">Um cenário comum é encadear um parâmetro de associação de dados a um elemento de página na saída do componente.</span><span class="sxs-lookup"><span data-stu-id="064d8-170">A common scenario is chaining a data-bound parameter to a page element in the component's output.</span></span> <span data-ttu-id="064d8-171">Esse cenário é chamado de *Associação encadeada* porque vários níveis de associação ocorrem simultaneamente.</span><span class="sxs-lookup"><span data-stu-id="064d8-171">This scenario is called a *chained bind* because multiple levels of binding occur simultaneously.</span></span>

<span data-ttu-id="064d8-172">Uma associação encadeada não pode ser implementada com `@bind` sintaxe no elemento da página.</span><span class="sxs-lookup"><span data-stu-id="064d8-172">A chained bind can't be implemented with `@bind` syntax in the page's element.</span></span> <span data-ttu-id="064d8-173">O manipulador de eventos e o valor devem ser especificados separadamente.</span><span class="sxs-lookup"><span data-stu-id="064d8-173">The event handler and value must be specified separately.</span></span> <span data-ttu-id="064d8-174">Um componente pai, no entanto, pode usar `@bind` sintaxe com o parâmetro do componente.</span><span class="sxs-lookup"><span data-stu-id="064d8-174">A parent component, however, can use `@bind` syntax with the component's parameter.</span></span>

<span data-ttu-id="064d8-175">O componente `PasswordField` a seguir (*passwordField. Razor*):</span><span class="sxs-lookup"><span data-stu-id="064d8-175">The following `PasswordField` component (*PasswordField.razor*):</span></span>

* <span data-ttu-id="064d8-176">Define um valor de elemento de `<input>` para uma propriedade `Password`.</span><span class="sxs-lookup"><span data-stu-id="064d8-176">Sets an `<input>` element's value to a `Password` property.</span></span>
* <span data-ttu-id="064d8-177">Expõe as alterações da propriedade `Password` para um componente pai com um [EventCallback](xref:blazor/event-handling#eventcallback).</span><span class="sxs-lookup"><span data-stu-id="064d8-177">Exposes changes of the `Password` property to a parent component with an [EventCallback](xref:blazor/event-handling#eventcallback).</span></span>
* <span data-ttu-id="064d8-178">Usa o evento `onclick` é usado para disparar o método `ToggleShowPassword`.</span><span class="sxs-lookup"><span data-stu-id="064d8-178">Uses the `onclick` event is used to trigger the `ToggleShowPassword` method.</span></span> <span data-ttu-id="064d8-179">Para obter mais informações, consulte <xref:blazor/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="064d8-179">For more information, see <xref:blazor/event-handling>.</span></span>

```razor
<h1>Child Component</h2>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(_showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool _showPassword;

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
        _showPassword = !_showPassword;
    }
}
```

<span data-ttu-id="064d8-180">O componente `PasswordField` é usado em outro componente:</span><span class="sxs-lookup"><span data-stu-id="064d8-180">The `PasswordField` component is used in another component:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="_password" />

@code {
    private string _password;
}
```

<span data-ttu-id="064d8-181">Para executar verificações ou interceptar erros na senha no exemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="064d8-181">To perform checks or trap errors on the password in the preceding example:</span></span>

* <span data-ttu-id="064d8-182">Crie um campo de backup para `Password` (`_password` no código de exemplo a seguir).</span><span class="sxs-lookup"><span data-stu-id="064d8-182">Create a backing field for `Password` (`_password` in the following example code).</span></span>
* <span data-ttu-id="064d8-183">Execute os erros de verificação ou interceptação no setter de `Password`.</span><span class="sxs-lookup"><span data-stu-id="064d8-183">Perform the checks or trap errors in the `Password` setter.</span></span>

<span data-ttu-id="064d8-184">O exemplo a seguir fornecerá comentários imediatos para o usuário se um espaço for usado no valor da senha:</span><span class="sxs-lookup"><span data-stu-id="064d8-184">The following example provides immediate feedback to the user if a space is used in the password's value:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(_showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@_validationMessage</span>

@code {
    private bool _showPassword;
    private string _password;
    private string _validationMessage;

    [Parameter]
    public string Password
    {
        get { return _password ?? string.Empty; }
        set
        {
            if (_password != value)
            {
                if (value.Contains(' '))
                {
                    _validationMessage = "Spaces not allowed!";
                }
                else
                {
                    _password = value;
                    _validationMessage = string.Empty;
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
        _showPassword = !_showPassword;
    }
}
```

## <a name="radio-buttons"></a><span data-ttu-id="064d8-185">Botões de opção</span><span class="sxs-lookup"><span data-stu-id="064d8-185">Radio buttons</span></span>

<span data-ttu-id="064d8-186">Para obter informações sobre a associação a botões de opção em um formulário, consulte <xref:blazor/forms-validation#work-with-radio-buttons>.</span><span class="sxs-lookup"><span data-stu-id="064d8-186">For information on binding to radio buttons in a form, see <xref:blazor/forms-validation#work-with-radio-buttons>.</span></span>
