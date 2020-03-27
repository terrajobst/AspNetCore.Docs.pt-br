---
title: ASP.NET Core Blazor Associação de dados
author: guardrex
description: Saiba mais sobre os recursos de associação de dados para componentes e elementos DOM em aplicativos Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: blazor/data-binding
ms.openlocfilehash: a7b3730dad48b5bbb6134dab181051da4e3651b4
ms.sourcegitcommit: f3b1bcfd108e5d53f73abc0bf2555890869d953b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2020
ms.locfileid: "80320960"
---
# <a name="aspnet-core-opno-locblazor-data-binding"></a>ASP.NET Core Blazor Associação de dados

De [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)

Os componentes do Razor fornecem recursos de vinculação de dados por meio de um atributo de elemento HTML chamado [`@bind`](xref:mvc/views/razor#bind) com um campo, propriedade ou valor de expressão Razor.

O exemplo a seguir associa a propriedade `CurrentValue` ao valor da caixa de texto:

```razor
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

Quando a caixa de texto perde o foco, o valor da propriedade é atualizado.

A caixa de texto é atualizada na interface do usuário somente quando o componente é renderizado, não em resposta à alteração do valor da propriedade. Como os componentes são renderizados após a execução do código do manipulador de eventos, as atualizações de propriedade *geralmente* são refletidas na interface do usuário imediatamente após um manipulador de eventos ser disparado.

O uso de `@bind` com a propriedade `CurrentValue` (`<input @bind="CurrentValue" />`) é essencialmente equivalente ao seguinte:

```razor
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

Quando o componente é renderizado, a `value` do elemento input é proveniente da propriedade `CurrentValue`. Quando o usuário digita na caixa de texto e altera o foco do elemento, o evento `onchange` é acionado e a propriedade `CurrentValue` é definida como o valor alterado. Na realidade, a geração de código é mais complexa porque `@bind` trata casos em que conversões de tipo são executadas. Em princípio, `@bind` associa o valor atual de uma expressão a um atributo `value` e manipula as alterações usando o manipulador registrado.

Associe uma propriedade ou um campo a outros eventos, incluindo também um atributo de `@bind:event` com um parâmetro `event`. O exemplo a seguir associa a propriedade `CurrentValue` no evento `oninput`:

```razor
<input @bind="CurrentValue" @bind:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

Ao contrário de `onchange`, que é disparado quando o elemento perde o foco, `oninput` é acionado quando o valor da caixa de texto é alterado.

Use `@bind-{ATTRIBUTE}` com a sintaxe `@bind-{ATTRIBUTE}:event` para associar atributos de elemento diferentes de `value`. No exemplo a seguir, o estilo do parágrafo é atualizado quando o valor de `_paragraphStyle` é alterado:

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

A associação de atributo diferencia maiúsculas de minúsculas. Por exemplo, `@bind` é válido e `@Bind` é inválido.

## <a name="unparsable-values"></a>Valores não analisáveis

Quando um usuário fornece um valor não analisável para um elemento de ligação de valores, o valor não analisável é revertido automaticamente para seu valor anterior quando o evento de ligação é disparado.

Considere o seguinte cenário:

* Um elemento `<input>` está associado a um tipo de `int` com um valor inicial de `123`:

  ```razor
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* O usuário atualiza o valor do elemento para `123.45` na página e altera o foco do elemento.

No cenário anterior, o valor do elemento é revertido para `123`. Quando o valor `123.45` é rejeitado em favor do valor original de `123`, o usuário entende que seu valor não foi aceito.

Por padrão, a associação aplica-se ao evento de `onchange` do elemento (`@bind="{PROPERTY OR FIELD}"`). Use `@bind="{PROPERTY OR FIELD}" @bind:event={EVENT}` para disparar a associação em um evento diferente. Para o evento de `oninput` (`@bind:event="oninput"`), a reversão ocorre após qualquer pressionamento de tecla que introduz um valor não analisável. Ao direcionar o evento de `oninput` com um tipo de associação de `int`, um usuário é impedido de digitar um caractere `.`. Um caractere `.` é removido imediatamente e, portanto, o usuário recebe comentários imediatos de que apenas números inteiros são permitidos. Há cenários em que a reversão do valor no evento `oninput` não é ideal, por exemplo, quando o usuário deve ter permissão para limpar um valor de `<input>` não analisável. As alternativas incluem:

* Não use o evento `oninput`. Use o evento de `onchange` padrão (especifique apenas `@bind="{PROPERTY OR FIELD}"`), em que um valor inválido não é revertido até que o elemento perca o foco.
* Associar a um tipo anulável, como `int?` ou `string`, e fornecer uma lógica personalizada para manipular entradas inválidas.
* Use um [componente de validação de formulário](xref:blazor/forms-validation), como `InputNumber` ou `InputDate`. Os componentes de validação de formulário têm suporte interno para gerenciar entradas inválidas. Componentes de validação de formulário:
  * Permitir que o usuário forneça erros de entrada e de validação inválidos no `EditContext`associado.
  * Exibir erros de validação na interface de usuário sem interferir no usuário inserindo dados adicionais do WebForms.

## <a name="format-strings"></a>Formatar cadeias de caracteres

A vinculação de dados funciona com <xref:System.DateTime> cadeias de caracteres de formato usando [`@bind:format`](xref:mvc/views/razor#bind). Outras expressões de formato, como formatos de moeda ou número, não estão disponíveis no momento.

```razor
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

No código anterior, o tipo de campo (`type`) do elemento `<input>` tem como padrão `text`. `@bind:format` tem suporte para ligar os seguintes tipos .NET:

* <xref:System.DateTime?displayProperty=fullName>
* <xref:System.DateTime?displayProperty=fullName>?
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <xref:System.DateTimeOffset?displayProperty=fullName>?

O atributo `@bind:format` especifica o formato de data a ser aplicado ao `value` do elemento `<input>`. O formato também é usado para analisar o valor quando ocorre um evento `onchange`.

Não é recomendável especificar um formato para o tipo de campo `date` porque Blazor tem suporte interno para formatar datas. Apesar da recomendação, use apenas o formato de data `yyyy-MM-dd` para que a associação funcione corretamente se um formato for fornecido com o tipo de campo `date`:

```razor
<input type="date" @bind="StartDate" @bind:format="yyyy-MM-dd">
```

## <a name="parent-to-child-binding-with-component-parameters"></a>Associação de pai para filho com parâmetros de componente

A associação reconhece os parâmetros do componente, em que `@bind-{PROPERTY}` pode associar um valor de propriedade de um componente pai a um componente filho. A associação de um filho a um pai é abordada na [Associação filho para pai com a seção de ligação encadeada](#child-to-parent-binding-with-chained-bind) .

O componente filho a seguir (`ChildComponent`) tem um parâmetro de componente `Year` e `YearChanged` retorno de chamada:

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

`EventCallback<T>` é explicado em <xref:blazor/event-handling#eventcallback>.

O seguinte componente pai usa:

* `ChildComponent` e associa o parâmetro `ParentYear` do pai ao parâmetro `Year` no componente filho.
* O evento `onclick` é usado para disparar o método `ChangeTheYear`. Para obter mais informações, consulte <xref:blazor/event-handling>.

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

Carregar o `ParentComponent` produz a seguinte marcação:

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

Se o valor da propriedade `ParentYear` for alterado selecionando o botão na `ParentComponent`, a propriedade `Year` do `ChildComponent` será atualizada. O novo valor de `Year` é renderizado na interface do usuário quando a `ParentComponent` é reprocessada:

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

O parâmetro `Year` é acoplável porque tem um evento complementar `YearChanged` que corresponde ao tipo do parâmetro `Year`.

Por convenção, `<ChildComponent @bind-Year="ParentYear" />` é essencialmente equivalente a escrever:

```razor
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

Em geral, uma propriedade pode ser associada a um manipulador de eventos correspondente, incluindo um atributo `@bind-{PROPRETY}:event`. Por exemplo, a propriedade `MyProp` pode ser associada a `MyEventHandler` usando os dois atributos a seguir:

```razor
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="child-to-parent-binding-with-chained-bind"></a>Associação de filho para pai com associação encadeada

Um cenário comum é encadear um parâmetro de associação de dados a um elemento de página na saída do componente. Esse cenário é chamado de *Associação encadeada* porque vários níveis de associação ocorrem simultaneamente.

Uma associação encadeada não pode ser implementada com `@bind` sintaxe no elemento da página. O manipulador de eventos e o valor devem ser especificados separadamente. Um componente pai, no entanto, pode usar `@bind` sintaxe com o parâmetro do componente.

O componente `PasswordField` a seguir (*passwordField. Razor*):

* Define um valor de elemento de `<input>` para uma propriedade `Password`.
* Expõe as alterações da propriedade `Password` para um componente pai com um [EventCallback](xref:blazor/event-handling#eventcallback).
* Usa o evento `onclick` é usado para disparar o método `ToggleShowPassword`. Para obter mais informações, consulte <xref:blazor/event-handling>.

```razor
<h1>Child Component</h1>

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

O componente `PasswordField` é usado em outro componente:

```razor
@page "/ParentComponent"

<h1>Parent Component</h1>

<PasswordField @bind-Password="_password" />

@code {
    private string _password;
}
```

Para executar verificações ou interceptar erros na senha no exemplo anterior:

* Crie um campo de backup para `Password` (`_password` no código de exemplo a seguir).
* Execute os erros de verificação ou interceptação no setter de `Password`.

O exemplo a seguir fornecerá comentários imediatos para o usuário se um espaço for usado no valor da senha:

```razor
<h1>Child Component</h1>

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

## <a name="radio-buttons"></a>Botões de opção

Para obter informações sobre a associação a botões de opção em um formulário, consulte <xref:blazor/forms-validation#work-with-radio-buttons>.
