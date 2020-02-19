---
title: Manipulação de eventos de Blazor de ASP.NET Core
author: guardrex
description: Saiba mais sobre os cenários de manipulação de eventos de Blazor, incluindo tipos de argumento de evento, retornos de chamada de evento e gerenciamento de eventos de navegador padrão.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/event-handling
ms.openlocfilehash: 25844ef39aee849072d16f3d73eda0a1c20ee788
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77453236"
---
# <a name="aspnet-core-blazor-event-handling"></a><span data-ttu-id="781d9-103">ASP.NET Core manipulação de eventos mais incrivelmente</span><span class="sxs-lookup"><span data-stu-id="781d9-103">ASP.NET Core Blazor event handling</span></span>

<span data-ttu-id="781d9-104">De [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="781d9-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="781d9-105">Os componentes do Razor fornecem recursos de manipulação de eventos.</span><span class="sxs-lookup"><span data-stu-id="781d9-105">Razor components provide event handling features.</span></span> <span data-ttu-id="781d9-106">Para um atributo de elemento HTML chamado `on{EVENT}` (por exemplo, `onclick` e `onsubmit`) com um valor de tipo delegado, os componentes do Razor tratam o valor do atributo como um manipulador de eventos.</span><span class="sxs-lookup"><span data-stu-id="781d9-106">For an HTML element attribute named `on{EVENT}` (for example, `onclick` and `onsubmit`) with a delegate-typed value, Razor components treats the attribute's value as an event handler.</span></span> <span data-ttu-id="781d9-107">O nome do atributo é sempre formatado [`@on{EVENT}`](xref:mvc/views/razor#onevent).</span><span class="sxs-lookup"><span data-stu-id="781d9-107">The attribute's name is always formatted [`@on{EVENT}`](xref:mvc/views/razor#onevent).</span></span>

<span data-ttu-id="781d9-108">O código a seguir chama o método `UpdateHeading` quando o botão é selecionado na interface do usuário:</span><span class="sxs-lookup"><span data-stu-id="781d9-108">The following code calls the `UpdateHeading` method when the button is selected in the UI:</span></span>

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

<span data-ttu-id="781d9-109">O código a seguir chama o método `CheckChanged` quando a caixa de seleção é alterada na interface do usuário:</span><span class="sxs-lookup"><span data-stu-id="781d9-109">The following code calls the `CheckChanged` method when the check box is changed in the UI:</span></span>

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

<span data-ttu-id="781d9-110">Os manipuladores de eventos também podem ser assíncronos e retornar um <xref:System.Threading.Tasks.Task>.</span><span class="sxs-lookup"><span data-stu-id="781d9-110">Event handlers can also be asynchronous and return a <xref:System.Threading.Tasks.Task>.</span></span> <span data-ttu-id="781d9-111">Não é necessário chamar [StateHasChanged](xref:blazor/lifecycle#state-changes)manualmente.</span><span class="sxs-lookup"><span data-stu-id="781d9-111">There's no need to manually call [StateHasChanged](xref:blazor/lifecycle#state-changes).</span></span> <span data-ttu-id="781d9-112">As exceções são registradas quando ocorrem.</span><span class="sxs-lookup"><span data-stu-id="781d9-112">Exceptions are logged when they occur.</span></span>

<span data-ttu-id="781d9-113">No exemplo a seguir, `UpdateHeading` é chamado de forma assíncrona quando o botão é selecionado:</span><span class="sxs-lookup"><span data-stu-id="781d9-113">In the following example, `UpdateHeading` is called asynchronously when the button is selected:</span></span>

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

## <a name="event-argument-types"></a><span data-ttu-id="781d9-114">Tipos de argumento de evento</span><span class="sxs-lookup"><span data-stu-id="781d9-114">Event argument types</span></span>

<span data-ttu-id="781d9-115">Para alguns eventos, são permitidos tipos de argumento de evento.</span><span class="sxs-lookup"><span data-stu-id="781d9-115">For some events, event argument types are permitted.</span></span> <span data-ttu-id="781d9-116">A especificação de um tipo de evento na chamada do método só será necessária se o tipo de evento for usado no método.</span><span class="sxs-lookup"><span data-stu-id="781d9-116">Specifying an event type in the method call is only necessary if the event type is used in the method.</span></span>

<span data-ttu-id="781d9-117">Os `EventArgs` com suporte são mostrados na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="781d9-117">Supported `EventArgs` are shown in the following table.</span></span>

| <span data-ttu-id="781d9-118">Evento</span><span class="sxs-lookup"><span data-stu-id="781d9-118">Event</span></span>            | <span data-ttu-id="781d9-119">Classe</span><span class="sxs-lookup"><span data-stu-id="781d9-119">Class</span></span>                | <span data-ttu-id="781d9-120">Eventos e observações do DOM</span><span class="sxs-lookup"><span data-stu-id="781d9-120">DOM events and notes</span></span> |
| ---------------- | -------------------- | -------------------- |
| <span data-ttu-id="781d9-121">Área de transferência</span><span class="sxs-lookup"><span data-stu-id="781d9-121">Clipboard</span></span>        | `ClipboardEventArgs` | <span data-ttu-id="781d9-122">`oncut`, `oncopy`, `onpaste`</span><span class="sxs-lookup"><span data-stu-id="781d9-122">`oncut`, `oncopy`, `onpaste`</span></span> |
| <span data-ttu-id="781d9-123">Arraste</span><span class="sxs-lookup"><span data-stu-id="781d9-123">Drag</span></span>             | `DragEventArgs`      | <span data-ttu-id="781d9-124">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span><span class="sxs-lookup"><span data-stu-id="781d9-124">`ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`</span></span><br><br><span data-ttu-id="781d9-125">`DataTransfer` e `DataTransferItem` manter os dados do item arrastados.</span><span class="sxs-lookup"><span data-stu-id="781d9-125">`DataTransfer` and `DataTransferItem` hold dragged item data.</span></span> |
| <span data-ttu-id="781d9-126">Erro</span><span class="sxs-lookup"><span data-stu-id="781d9-126">Error</span></span>            | `ErrorEventArgs`     | `onerror` |
| <span data-ttu-id="781d9-127">Evento</span><span class="sxs-lookup"><span data-stu-id="781d9-127">Event</span></span>            | `EventArgs`          | <span data-ttu-id="781d9-128">*Geral*</span><span class="sxs-lookup"><span data-stu-id="781d9-128">*General*</span></span><br><span data-ttu-id="781d9-129">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span><span class="sxs-lookup"><span data-stu-id="781d9-129">`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`</span></span><br><br><span data-ttu-id="781d9-130">*Área de transferência*</span><span class="sxs-lookup"><span data-stu-id="781d9-130">*Clipboard*</span></span><br><span data-ttu-id="781d9-131">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span><span class="sxs-lookup"><span data-stu-id="781d9-131">`onbeforecut`, `onbeforecopy`, `onbeforepaste`</span></span><br><br><span data-ttu-id="781d9-132">*Entrada*</span><span class="sxs-lookup"><span data-stu-id="781d9-132">*Input*</span></span><br><span data-ttu-id="781d9-133">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span><span class="sxs-lookup"><span data-stu-id="781d9-133">`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`</span></span><br><br><span data-ttu-id="781d9-134">*Mídia*</span><span class="sxs-lookup"><span data-stu-id="781d9-134">*Media*</span></span><br><span data-ttu-id="781d9-135">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span><span class="sxs-lookup"><span data-stu-id="781d9-135">`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting`</span></span> |
| <span data-ttu-id="781d9-136">Focus</span><span class="sxs-lookup"><span data-stu-id="781d9-136">Focus</span></span>            | `FocusEventArgs`     | <span data-ttu-id="781d9-137">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span><span class="sxs-lookup"><span data-stu-id="781d9-137">`onfocus`, `onblur`, `onfocusin`, `onfocusout`</span></span><br><br><span data-ttu-id="781d9-138">Não inclui suporte para `relatedTarget`.</span><span class="sxs-lookup"><span data-stu-id="781d9-138">Doesn't include support for `relatedTarget`.</span></span> |
| <span data-ttu-id="781d9-139">Entrada</span><span class="sxs-lookup"><span data-stu-id="781d9-139">Input</span></span>            | `ChangeEventArgs`    | <span data-ttu-id="781d9-140">`onchange`, `oninput`</span><span class="sxs-lookup"><span data-stu-id="781d9-140">`onchange`, `oninput`</span></span> |
| <span data-ttu-id="781d9-141">Teclado</span><span class="sxs-lookup"><span data-stu-id="781d9-141">Keyboard</span></span>         | `KeyboardEventArgs`  | <span data-ttu-id="781d9-142">`onkeydown`, `onkeypress`, `onkeyup`</span><span class="sxs-lookup"><span data-stu-id="781d9-142">`onkeydown`, `onkeypress`, `onkeyup`</span></span> |
| <span data-ttu-id="781d9-143">Mouse</span><span class="sxs-lookup"><span data-stu-id="781d9-143">Mouse</span></span>            | `MouseEventArgs`     | <span data-ttu-id="781d9-144">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span><span class="sxs-lookup"><span data-stu-id="781d9-144">`onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout`</span></span> |
| <span data-ttu-id="781d9-145">Ponteiro do mouse</span><span class="sxs-lookup"><span data-stu-id="781d9-145">Mouse pointer</span></span>    | `PointerEventArgs`   | <span data-ttu-id="781d9-146">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span><span class="sxs-lookup"><span data-stu-id="781d9-146">`onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture`</span></span> |
| <span data-ttu-id="781d9-147">Roda do mouse</span><span class="sxs-lookup"><span data-stu-id="781d9-147">Mouse wheel</span></span>      | `WheelEventArgs`     | <span data-ttu-id="781d9-148">`onwheel`, `onmousewheel`</span><span class="sxs-lookup"><span data-stu-id="781d9-148">`onwheel`, `onmousewheel`</span></span> |
| <span data-ttu-id="781d9-149">Andamento</span><span class="sxs-lookup"><span data-stu-id="781d9-149">Progress</span></span>         | `ProgressEventArgs`  | <span data-ttu-id="781d9-150">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span><span class="sxs-lookup"><span data-stu-id="781d9-150">`onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout`</span></span> |
| <span data-ttu-id="781d9-151">Toque</span><span class="sxs-lookup"><span data-stu-id="781d9-151">Touch</span></span>            | `TouchEventArgs`     | <span data-ttu-id="781d9-152">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span><span class="sxs-lookup"><span data-stu-id="781d9-152">`ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`</span></span><br><br><span data-ttu-id="781d9-153">`TouchPoint` representa um único ponto de contato em um dispositivo sensível ao toque.</span><span class="sxs-lookup"><span data-stu-id="781d9-153">`TouchPoint` represents a single contact point on a touch-sensitive device.</span></span> |

<span data-ttu-id="781d9-154">Para saber mais, consulte os recursos a seguir:</span><span class="sxs-lookup"><span data-stu-id="781d9-154">For more information, see the following resources:</span></span>

* <span data-ttu-id="781d9-155">[Classes EventArgs no ASP.NET Core fonte de referência (dotNet/aspnetcore versão/3.1 Branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span><span class="sxs-lookup"><span data-stu-id="781d9-155">[EventArgs classes in the ASP.NET Core reference source (dotnet/aspnetcore release/3.1 branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).</span></span>
* <span data-ttu-id="781d9-156">[MDN Web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; inclui informações sobre quais elementos HTML oferecem suporte a cada evento dom.</span><span class="sxs-lookup"><span data-stu-id="781d9-156">[MDN web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; Includes information on which HTML elements support each DOM event.</span></span>

## <a name="lambda-expressions"></a><span data-ttu-id="781d9-157">Expressões lambda</span><span class="sxs-lookup"><span data-stu-id="781d9-157">Lambda expressions</span></span>

<span data-ttu-id="781d9-158">As [expressões lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) também podem ser usadas:</span><span class="sxs-lookup"><span data-stu-id="781d9-158">[Lambda expressions](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) can also be used:</span></span>

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

<span data-ttu-id="781d9-159">Geralmente, é conveniente fechar valores adicionais, como ao iterar em um conjunto de elementos.</span><span class="sxs-lookup"><span data-stu-id="781d9-159">It's often convenient to close over additional values, such as when iterating over a set of elements.</span></span> <span data-ttu-id="781d9-160">O exemplo a seguir cria três botões, cada um dos quais chamadas `UpdateHeading` passando um argumento de evento (`MouseEventArgs`) e seu número de botão (`buttonNumber`) quando selecionado na interface do usuário:</span><span class="sxs-lookup"><span data-stu-id="781d9-160">The following example creates three buttons, each of which calls `UpdateHeading` passing an event argument (`MouseEventArgs`) and its button number (`buttonNumber`) when selected in the UI:</span></span>

```razor
<h2>@_message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string _message = "Select a button to learn its position.";

    private void UpdateHeading(MouseEventArgs e, int buttonNumber)
    {
        _message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> <span data-ttu-id="781d9-161">**Não** use a variável de loop (`i`) em um loop de `for` diretamente em uma expressão lambda.</span><span class="sxs-lookup"><span data-stu-id="781d9-161">Do **not** use the loop variable (`i`) in a `for` loop directly in a lambda expression.</span></span> <span data-ttu-id="781d9-162">Caso contrário, a mesma variável é usada por todas as expressões lambda, fazendo com que `i`valor seja o mesmo em todos os lambdas.</span><span class="sxs-lookup"><span data-stu-id="781d9-162">Otherwise the same variable is used by all lambda expressions causing `i`'s value to be the same in all lambdas.</span></span> <span data-ttu-id="781d9-163">Sempre Capture seu valor em uma variável local (`buttonNumber` no exemplo anterior) e, em seguida, use-o.</span><span class="sxs-lookup"><span data-stu-id="781d9-163">Always capture its value in a local variable (`buttonNumber` in the preceding example) and then use it.</span></span>

## <a name="eventcallback"></a><span data-ttu-id="781d9-164">EventCallback</span><span class="sxs-lookup"><span data-stu-id="781d9-164">EventCallback</span></span>

<span data-ttu-id="781d9-165">Um cenário comum com componentes aninhados é o desejo de executar o método de um componente pai quando ocorre um evento de componente filho&mdash;por exemplo, quando um evento de `onclick` ocorre no filho.</span><span class="sxs-lookup"><span data-stu-id="781d9-165">A common scenario with nested components is the desire to run a parent component's method when a child component event occurs&mdash;for example, when an `onclick` event occurs in the child.</span></span> <span data-ttu-id="781d9-166">Para expor eventos entre componentes, use um `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="781d9-166">To expose events across components, use an `EventCallback`.</span></span> <span data-ttu-id="781d9-167">Um componente pai pode atribuir um método de retorno de chamada para o `EventCallback`de um componente filho.</span><span class="sxs-lookup"><span data-stu-id="781d9-167">A parent component can assign a callback method to a child component's `EventCallback`.</span></span>

<span data-ttu-id="781d9-168">O `ChildComponent` no aplicativo de exemplo (*Components/ChildComponent. Razor*) demonstra como o manipulador de `onclick` de um botão é configurado para receber um delegado de `EventCallback` da `ParentComponent`de exemplo.</span><span class="sxs-lookup"><span data-stu-id="781d9-168">The `ChildComponent` in the sample app (*Components/ChildComponent.razor*) demonstrates how a button's `onclick` handler is set up to receive an `EventCallback` delegate from the sample's `ParentComponent`.</span></span> <span data-ttu-id="781d9-169">O `EventCallback` é digitado com `MouseEventArgs`, que é apropriado para um evento de `onclick` de um dispositivo periférico:</span><span class="sxs-lookup"><span data-stu-id="781d9-169">The `EventCallback` is typed with `MouseEventArgs`, which is appropriate for an `onclick` event from a peripheral device:</span></span>

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

<span data-ttu-id="781d9-170">O `ParentComponent` define o `EventCallback<T>` (`OnClickCallback`) do filho como seu método `ShowMessage`.</span><span class="sxs-lookup"><span data-stu-id="781d9-170">The `ParentComponent` sets the child's `EventCallback<T>` (`OnClickCallback`) to its `ShowMessage` method.</span></span>

<span data-ttu-id="781d9-171">*Páginas/ParentComponent. Razor*:</span><span class="sxs-lookup"><span data-stu-id="781d9-171">*Pages/ParentComponent.razor*:</span></span>

```razor
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent"
                OnClickCallback="@ShowMessage">
    Content of the child component is supplied
    by the parent component.
</ChildComponent>

<p><b>@_messageText</b></p>

@code {
    private string _messageText;

    private void ShowMessage(MouseEventArgs e)
    {
        _messageText = $"Blaze a new trail with Blazor! ({e.ScreenX}, {e.ScreenY})";
    }
}
```

<span data-ttu-id="781d9-172">Quando o botão estiver selecionado na `ChildComponent`:</span><span class="sxs-lookup"><span data-stu-id="781d9-172">When the button is selected in the `ChildComponent`:</span></span>

* <span data-ttu-id="781d9-173">O método `ShowMessage` do `ParentComponent`é chamado.</span><span class="sxs-lookup"><span data-stu-id="781d9-173">The `ParentComponent`'s `ShowMessage` method is called.</span></span> <span data-ttu-id="781d9-174">`_messageText` é atualizado e exibido no `ParentComponent`.</span><span class="sxs-lookup"><span data-stu-id="781d9-174">`_messageText` is updated and displayed in the `ParentComponent`.</span></span>
* <span data-ttu-id="781d9-175">Uma chamada para [StateHasChanged](xref:blazor/lifecycle#state-changes) não é necessária no método do retorno de chamada (`ShowMessage`).</span><span class="sxs-lookup"><span data-stu-id="781d9-175">A call to [StateHasChanged](xref:blazor/lifecycle#state-changes) isn't required in the callback's method (`ShowMessage`).</span></span> <span data-ttu-id="781d9-176">`StateHasChanged` é chamado automaticamente para renderizar novamente o `ParentComponent`, assim como os eventos filho disparam o reprocessamento de componentes em manipuladores de eventos que são executados dentro do filho.</span><span class="sxs-lookup"><span data-stu-id="781d9-176">`StateHasChanged` is called automatically to rerender the `ParentComponent`, just as child events trigger component rerendering in event handlers that execute within the child.</span></span>

<span data-ttu-id="781d9-177">`EventCallback` e `EventCallback<T>` permitir delegados assíncronos.</span><span class="sxs-lookup"><span data-stu-id="781d9-177">`EventCallback` and `EventCallback<T>` permit asynchronous delegates.</span></span> <span data-ttu-id="781d9-178">`EventCallback<T>` é fortemente tipado e requer um tipo de argumento específico.</span><span class="sxs-lookup"><span data-stu-id="781d9-178">`EventCallback<T>` is strongly typed and requires a specific argument type.</span></span> <span data-ttu-id="781d9-179">`EventCallback` é digitado de forma fraca e permite qualquer tipo de argumento.</span><span class="sxs-lookup"><span data-stu-id="781d9-179">`EventCallback` is weakly typed and allows any argument type.</span></span>

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); _messageText = "Blaze It!"; })" />
```

<span data-ttu-id="781d9-180">Invoque um `EventCallback` ou `EventCallback<T>` com `InvokeAsync` e aguardar a <xref:System.Threading.Tasks.Task>:</span><span class="sxs-lookup"><span data-stu-id="781d9-180">Invoke an `EventCallback` or `EventCallback<T>` with `InvokeAsync` and await the <xref:System.Threading.Tasks.Task>:</span></span>

```csharp
await callback.InvokeAsync(arg);
```

<span data-ttu-id="781d9-181">Use `EventCallback` e `EventCallback<T>` para manipulação de eventos e parâmetros de componente de associação.</span><span class="sxs-lookup"><span data-stu-id="781d9-181">Use `EventCallback` and `EventCallback<T>` for event handling and binding component parameters.</span></span>

<span data-ttu-id="781d9-182">Prefira a `EventCallback<T>` com rigidez de tipos sobre `EventCallback`.</span><span class="sxs-lookup"><span data-stu-id="781d9-182">Prefer the strongly typed `EventCallback<T>` over `EventCallback`.</span></span> <span data-ttu-id="781d9-183">`EventCallback<T>` fornece melhores comentários de erro para os usuários do componente.</span><span class="sxs-lookup"><span data-stu-id="781d9-183">`EventCallback<T>` provides better error feedback to users of the component.</span></span> <span data-ttu-id="781d9-184">Semelhante a outros manipuladores de eventos de interface do usuário, especificar o parâmetro de evento é opcional.</span><span class="sxs-lookup"><span data-stu-id="781d9-184">Similar to other UI event handlers, specifying the event parameter is optional.</span></span> <span data-ttu-id="781d9-185">Use `EventCallback` quando não houver valor passado para o retorno de chamada.</span><span class="sxs-lookup"><span data-stu-id="781d9-185">Use `EventCallback` when there's no value passed to the callback.</span></span>

## <a name="prevent-default-actions"></a><span data-ttu-id="781d9-186">Impedir ações padrão</span><span class="sxs-lookup"><span data-stu-id="781d9-186">Prevent default actions</span></span>

<span data-ttu-id="781d9-187">Use o atributo de diretiva [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) para evitar a ação padrão para um evento.</span><span class="sxs-lookup"><span data-stu-id="781d9-187">Use the [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) directive attribute to prevent the default action for an event.</span></span>

<span data-ttu-id="781d9-188">Quando uma chave é selecionada em um dispositivo de entrada e o foco do elemento está em uma caixa de texto, um navegador normalmente exibe o caractere da chave na caixa de texto.</span><span class="sxs-lookup"><span data-stu-id="781d9-188">When a key is selected on an input device and the element focus is on a text box, a browser normally displays the key's character in the text box.</span></span> <span data-ttu-id="781d9-189">No exemplo a seguir, o comportamento padrão é impedido pela especificação do atributo de diretiva `@onkeypress:preventDefault`.</span><span class="sxs-lookup"><span data-stu-id="781d9-189">In the following example, the default behavior is prevented by specifying the `@onkeypress:preventDefault` directive attribute.</span></span> <span data-ttu-id="781d9-190">O contador é incrementado e a chave de **+** não é capturada no valor do elemento de `<input>`:</span><span class="sxs-lookup"><span data-stu-id="781d9-190">The counter increments, and the **+** key isn't captured into the `<input>` element's value:</span></span>

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

<span data-ttu-id="781d9-191">Especificar o atributo `@on{EVENT}:preventDefault` sem um valor é equivalente a `@on{EVENT}:preventDefault="true"`.</span><span class="sxs-lookup"><span data-stu-id="781d9-191">Specifying the `@on{EVENT}:preventDefault` attribute without a value is equivalent to `@on{EVENT}:preventDefault="true"`.</span></span>

<span data-ttu-id="781d9-192">O valor do atributo também pode ser uma expressão.</span><span class="sxs-lookup"><span data-stu-id="781d9-192">The value of the attribute can also be an expression.</span></span> <span data-ttu-id="781d9-193">No exemplo a seguir, `_shouldPreventDefault` é um campo de `bool` definido como `true` ou `false`:</span><span class="sxs-lookup"><span data-stu-id="781d9-193">In the following example, `_shouldPreventDefault` is a `bool` field set to either `true` or `false`:</span></span>

```razor
<input @onkeypress:preventDefault="_shouldPreventDefault" />
```

<span data-ttu-id="781d9-194">Um manipulador de eventos não é necessário para impedir a ação padrão.</span><span class="sxs-lookup"><span data-stu-id="781d9-194">An event handler isn't required to prevent the default action.</span></span> <span data-ttu-id="781d9-195">O manipulador de eventos e a prevenção de cenários de ação padrão podem ser usados de forma independente.</span><span class="sxs-lookup"><span data-stu-id="781d9-195">The event handler and prevent default action scenarios can be used independently.</span></span>

## <a name="stop-event-propagation"></a><span data-ttu-id="781d9-196">Parar a propagação do evento</span><span class="sxs-lookup"><span data-stu-id="781d9-196">Stop event propagation</span></span>

<span data-ttu-id="781d9-197">Use o atributo de diretiva [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) para parar a propagação do evento.</span><span class="sxs-lookup"><span data-stu-id="781d9-197">Use the [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) directive attribute to stop event propagation.</span></span>

<span data-ttu-id="781d9-198">No exemplo a seguir, marcar a caixa de seleção impede que eventos de clique do segundo filho `<div>` sejam propagados para o `<div>`pai:</span><span class="sxs-lookup"><span data-stu-id="781d9-198">In the following example, selecting the check box prevents click events from the second child `<div>` from propagating to the parent `<div>`:</span></span>

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
