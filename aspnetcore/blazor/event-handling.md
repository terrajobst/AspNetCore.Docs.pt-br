---
title: Manipulação de eventos de Blazor de ASP.NET Core
author: guardrex
description: Saiba mais sobre os recursos de manipulação de eventos de Blazor, incluindo tipos de argumento de evento, retornos de chamada de evento e gerenciamento de eventos de navegador padrão.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/event-handling
ms.openlocfilehash: c144841805e07a136f153c25a78c7f9af7c5801b
ms.sourcegitcommit: d64ef143c64ee4fdade8f9ea0b753b16752c5998
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2020
ms.locfileid: "79511360"
---
# <a name="aspnet-core-blazor-event-handling"></a>ASP.NET Core manipulação de eventos mais incrivelmente

De [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)

Os componentes do Razor fornecem recursos de manipulação de eventos. Para um atributo de elemento HTML chamado [`@on{EVENT}`](xref:mvc/views/razor#onevent) (por exemplo, `@onclick`) com um valor de tipo delegado, um componente Razor trata o valor do atributo como um manipulador de eventos.

O código a seguir chama o método `UpdateHeading` quando o botão é selecionado na interface do usuário:

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

O código a seguir chama o método `CheckChanged` quando a caixa de seleção é alterada na interface do usuário:

```razor
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

Os manipuladores de eventos também podem ser assíncronos e retornar um <xref:System.Threading.Tasks.Task>. Não é necessário chamar [StateHasChanged](xref:blazor/lifecycle#state-changes)manualmente. As exceções são registradas quando ocorrem.

No exemplo a seguir, `UpdateHeading` é chamado de forma assíncrona quando o botão é selecionado:

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

## <a name="event-argument-types"></a>Tipos de argumento de evento

Para alguns eventos, são permitidos tipos de argumento de evento. A especificação de um tipo de evento na chamada do método só será necessária se o tipo de evento for usado no método.

Os `EventArgs` com suporte são mostrados na tabela a seguir.

| {1&gt;Evento&lt;1}            | Classe                | Eventos e observações do DOM |
| ---------------- | -------------------- | -------------------- |
| Área de transferência        | `ClipboardEventArgs` | `oncut`, `oncopy`, `onpaste` |
| Arraste             | `DragEventArgs`      | `ondrag`, `ondragstart`, `ondragenter`, `ondragleave`, `ondragover`, `ondrop`, `ondragend`<br><br>`DataTransfer` e `DataTransferItem` manter os dados do item arrastados. |
| Error            | `ErrorEventArgs`     | `onerror` |
| {1&gt;Evento&lt;1}            | `EventArgs`          | *Geral*<br>`onactivate`, `onbeforeactivate`, `onbeforedeactivate`, `ondeactivate`, `onended`, `onfullscreenchange`, `onfullscreenerror`, `onloadeddata`, `onloadedmetadata`, `onpointerlockchange`, `onpointerlockerror`, `onreadystatechange`, `onscroll`<br><br>*Área de transferência*<br>`onbeforecut`, `onbeforecopy`, `onbeforepaste`<br><br>*Entrada*<br>`oninvalid`, `onreset`, `onselect`, `onselectionchange`, `onselectstart`, `onsubmit`<br><br>*Mídia*<br>`oncanplay`, `oncanplaythrough`, `oncuechange`, `ondurationchange`, `onemptied`, `onpause`, `onplay`, `onplaying`, `onratechange`, `onseeked`, `onseeking`, `onstalled`, `onstop`, `onsuspend`, `ontimeupdate`, `onvolumechange`, `onwaiting` |
| Foco            | `FocusEventArgs`     | `onfocus`, `onblur`, `onfocusin`, `onfocusout`<br><br>Não inclui suporte para `relatedTarget`. |
| Entrada            | `ChangeEventArgs`    | `onchange`, `oninput` |
| Teclado         | `KeyboardEventArgs`  | `onkeydown`, `onkeypress`, `onkeyup` |
| Mouse            | `MouseEventArgs`     | `onclick`, `oncontextmenu`, `ondblclick`, `onmousedown`, `onmouseup`, `onmouseover`, `onmousemove`, `onmouseout` |
| Ponteiro do mouse    | `PointerEventArgs`   | `onpointerdown`, `onpointerup`, `onpointercancel`, `onpointermove`, `onpointerover`, `onpointerout`, `onpointerenter`, `onpointerleave`, `ongotpointercapture`, `onlostpointercapture` |
| Roda do mouse      | `WheelEventArgs`     | `onwheel`, `onmousewheel` |
| Andamento         | `ProgressEventArgs`  | `onabort`, `onload`, `onloadend`, `onloadstart`, `onprogress`, `ontimeout` |
| Toque            | `TouchEventArgs`     | `ontouchstart`, `ontouchend`, `ontouchmove`, `ontouchenter`, `ontouchleave`, `ontouchcancel`<br><br>`TouchPoint` representa um único ponto de contato em um dispositivo sensível ao toque. |

Para obter mais informações, consulte os seguintes recursos:

* [Classes EventArgs no ASP.NET Core fonte de referência (dotNet/aspnetcore versão/3.1 Branch)](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/Components/Web/src/Web).
* [MDN Web docs: GlobalEventHandlers](https://developer.mozilla.org/docs/Web/API/GlobalEventHandlers) &ndash; inclui informações sobre quais elementos HTML oferecem suporte a cada evento dom.

## <a name="lambda-expressions"></a>Expressões lambda

As [expressões lambda](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions) também podem ser usadas:

```razor
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

Geralmente, é conveniente fechar valores adicionais, como ao iterar em um conjunto de elementos. O exemplo a seguir cria três botões, cada um dos quais chamadas `UpdateHeading` passando um argumento de evento (`MouseEventArgs`) e seu número de botão (`buttonNumber`) quando selecionado na interface do usuário:

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
> **Não** use a variável de loop (`i`) em um loop de `for` diretamente em uma expressão lambda. Caso contrário, a mesma variável é usada por todas as expressões lambda, fazendo com que `i`valor seja o mesmo em todos os lambdas. Sempre Capture seu valor em uma variável local (`buttonNumber` no exemplo anterior) e, em seguida, use-o.

## <a name="eventcallback"></a>EventCallback

Um cenário comum com componentes aninhados é o desejo de executar o método de um componente pai quando ocorre um evento de componente filho&mdash;por exemplo, quando um evento de `onclick` ocorre no filho. Para expor eventos entre componentes, use um `EventCallback`. Um componente pai pode atribuir um método de retorno de chamada para o `EventCallback`de um componente filho.

O `ChildComponent` no aplicativo de exemplo (*Components/ChildComponent. Razor*) demonstra como o manipulador de `onclick` de um botão é configurado para receber um delegado de `EventCallback` da `ParentComponent`de exemplo. O `EventCallback` é digitado com `MouseEventArgs`, que é apropriado para um evento de `onclick` de um dispositivo periférico:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

O `ParentComponent` define o `EventCallback<T>` (`OnClickCallback`) do filho como seu método `ShowMessage`.

*Páginas/ParentComponent. Razor*:

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

Quando o botão estiver selecionado na `ChildComponent`:

* O método `ShowMessage` do `ParentComponent`é chamado. `_messageText` é atualizado e exibido no `ParentComponent`.
* Uma chamada para [StateHasChanged](xref:blazor/lifecycle#state-changes) não é necessária no método do retorno de chamada (`ShowMessage`). `StateHasChanged` é chamado automaticamente para renderizar novamente o `ParentComponent`, assim como os eventos filho disparam o reprocessamento de componentes em manipuladores de eventos que são executados dentro do filho.

`EventCallback` e `EventCallback<T>` permitir delegados assíncronos. `EventCallback<T>` é fortemente tipado e requer um tipo de argumento específico. `EventCallback` é digitado de forma fraca e permite qualquer tipo de argumento.

```razor
<ChildComponent 
    OnClickCallback="@(async () => { await Task.Yield(); _messageText = "Blaze It!"; })" />
```

Invoque um `EventCallback` ou `EventCallback<T>` com `InvokeAsync` e aguardar a <xref:System.Threading.Tasks.Task>:

```csharp
await callback.InvokeAsync(arg);
```

Use `EventCallback` e `EventCallback<T>` para manipulação de eventos e parâmetros de componente de associação.

Prefira a `EventCallback<T>` com rigidez de tipos sobre `EventCallback`. `EventCallback<T>` fornece melhores comentários de erro para os usuários do componente. Semelhante a outros manipuladores de eventos de interface do usuário, especificar o parâmetro de evento é opcional. Use `EventCallback` quando não houver valor passado para o retorno de chamada.

## <a name="prevent-default-actions"></a>Impedir ações padrão

Use o atributo de diretiva [`@on{EVENT}:preventDefault`](xref:mvc/views/razor#oneventpreventdefault) para evitar a ação padrão para um evento.

Quando uma chave é selecionada em um dispositivo de entrada e o foco do elemento está em uma caixa de texto, um navegador normalmente exibe o caractere da chave na caixa de texto. No exemplo a seguir, o comportamento padrão é impedido pela especificação do atributo de diretiva `@onkeypress:preventDefault`. O contador é incrementado e a chave de **+** não é capturada no valor do elemento de `<input>`:

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

Especificar o atributo `@on{EVENT}:preventDefault` sem um valor é equivalente a `@on{EVENT}:preventDefault="true"`.

O valor do atributo também pode ser uma expressão. No exemplo a seguir, `_shouldPreventDefault` é um campo de `bool` definido como `true` ou `false`:

```razor
<input @onkeypress:preventDefault="_shouldPreventDefault" />
```

Um manipulador de eventos não é necessário para impedir a ação padrão. O manipulador de eventos e a prevenção de cenários de ação padrão podem ser usados de forma independente.

## <a name="stop-event-propagation"></a>Parar a propagação do evento

Use o atributo de diretiva [`@on{EVENT}:stopPropagation`](xref:mvc/views/razor#oneventstoppropagation) para parar a propagação do evento.

No exemplo a seguir, marcar a caixa de seleção impede que eventos de clique do segundo filho `<div>` sejam propagados para o `<div>`pai:

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
