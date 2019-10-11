---
title: Criar e usar ASP.NET Core componentes do Razor
author: guardrex
description: Saiba como criar e usar componentes do Razor, incluindo como associar dados, manipular eventos e gerenciar ciclos de vida do componente.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/05/2019
uid: blazor/components
ms.openlocfilehash: 3e0966bf978c99fc00db7682bea3292306cbb03c
ms.sourcegitcommit: d81912782a8b0bd164f30a516ad80f8defb5d020
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72179033"
---
# <a name="create-and-use-aspnet-core-razor-components"></a>Criar e usar ASP.NET Core componentes do Razor

De [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)

[Exibir ou baixar código de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([como baixar](xref:index#how-to-download-a-sample))

Os aplicativos mais elaborados são criados usando *componentes*. Um componente é uma parte independente da interface do usuário (IU), como uma página, uma caixa de diálogo ou um formulário. Um componente inclui marcação HTML e a lógica de processamento necessária para injetar dados ou responder a eventos da interface do usuário. Os componentes são flexíveis e leves. Eles podem ser aninhados, reutilizados e compartilhados entre projetos.

## <a name="component-classes"></a>Classes de componente

Os componentes são implementados em arquivos de componente do [Razor](xref:mvc/views/razor) ( *. Razor*) C# usando uma combinação de marcação HTML e. Um componente no mais alto é conhecido formalmente como um *componente do Razor*.

O nome de um componente deve começar com um caractere maiúsculo. Por exemplo, *MyCoolComponent. Razor* é válido e *MyCoolComponent. Razor* é inválido.

A interface do usuário para um componente é definida usando HTML. A lógica de renderização dinâmica (por exemplo, loops, condicionais, expressões) é adicionada usando uma sintaxe de C# inserida chamada [Razor](xref:mvc/views/razor). Quando um aplicativo é compilado, a marcação HTML e C# a lógica de renderização são convertidas em uma classe de componente. O nome da classe gerada corresponde ao nome do arquivo.

Os membros da classe de componente são definidos em um bloco `@code`. No bloco `@code`, estado do componente (Propriedades, campos) é especificado com métodos para manipulação de eventos ou para definir outra lógica de componente. Mais de um bloco de `@code` é permitido.

> [!NOTE]
> Nas visualizações anteriores de ASP.NET Core 3,0, os blocos `@functions` foram usados para a mesma finalidade que os blocos `@code` nos componentes do Razor. os blocos `@functions` continuam a funcionar em componentes do Razor, mas é recomendável usar o bloco `@code` no ASP.NET Core 3,0 Preview 6 ou posterior.

Os membros do componente podem ser usados como parte da lógica de renderização do C# componente usando expressões que começam com `@`. Por exemplo, um C# campo é renderizado pela prefixação `@` para o nome do campo. O exemplo a seguir avalia e renderiza:

* `_headingFontStyle` para o valor da propriedade CSS para `font-style`.
* `_headingText` para o conteúdo do elemento `<h1>`.

```cshtml
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

Depois que o componente é processado inicialmente, o componente regenera sua árvore de renderização em resposta a eventos. Em seguida, ele compara a nova árvore de renderização com a anterior e aplica quaisquer modificações ao Modelo de Objeto do Documento do navegador (DOM).

Os componentes são C# classes comuns e podem ser colocados em qualquer lugar dentro de um projeto. Os componentes que produzem páginas da Web geralmente residem na pasta *páginas* . Os componentes que não são de página são frequentemente colocados na pasta *compartilhada* ou em uma pasta personalizada adicionada ao projeto. Para usar uma pasta personalizada, adicione o namespace da pasta personalizada ao componente pai ou ao arquivo *_Imports. Razor* do aplicativo. Por exemplo, o namespace a seguir torna os componentes em uma pasta *componentes* disponíveis quando o namespace raiz do aplicativo é `WebApplication`:

```cshtml
@using WebApplication.Components
```

## <a name="integrate-components-into-razor-pages-and-mvc-apps"></a>Integrar componentes em aplicativos Razor Pages e MVC

Use componentes com os aplicativos Razor Pages e MVC existentes. Não é necessário reescrever páginas ou exibições existentes para usar os componentes do Razor. Quando a página ou a exibição é renderizada, os componentes são renderizados ao mesmo tempo.

Para renderizar um componente de uma página ou exibição, use o método auxiliar HTML `RenderComponentAsync<TComponent>`:

```cshtml
<div id="MyComponent">
    @(await Html.RenderComponentAsync<MyComponent>(RenderMode.ServerPrerendered))
</div>
```

Embora as páginas e exibições possam usar componentes, o inverso não é verdadeiro. Os componentes não podem usar cenários específicos de exibição e de página, como exibições parciais e seções. Para usar a lógica da exibição parcial em um componente, desfatore a lógica de exibição parcial em um componente.

Para obter mais informações sobre como os componentes são renderizados e o estado do componente é gerenciado em aplicativos de servidor mais incrivelmente, consulte o artigo <xref:blazor/hosting-models>.

## <a name="use-components"></a>Usar componentes

Os componentes podem incluir outros componentes, declarando-os usando a sintaxe do elemento HTML. A marcação para uso de um componente é semelhante a uma marca HTML, em que o nome da marca é o tipo de componente.

A associação de atributo diferencia maiúsculas de minúsculas. Por exemplo, `@bind` é válido e `@Bind` é inválido.

A marcação a seguir no *index. Razor* renderiza uma instância `HeadingComponent`:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/Index.razor?name=snippet_HeadingComponent)]

*Componentes/HeadingComponent. Razor*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/HeadingComponent.razor)]

Se um componente contiver um elemento HTML com uma letra maiúscula ou minúscula que não corresponda a um nome de componente, um aviso será emitido indicando que o elemento tem um nome inesperado. A adição de uma instrução `@using` para o namespace do componente disponibiliza o componente, o que remove o aviso.

## <a name="component-parameters"></a>Parâmetros do componente

Os componentes podem ter *parâmetros de componente*, que são definidos usando propriedades públicas na classe de componente com o atributo `[Parameter]`. Use atributos para especificar argumentos para um componente na marcação.

*Componentes/ChildComponent. Razor*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=11-12)]

No exemplo a seguir, o `ParentComponent` define o valor da propriedade `Title` do `ChildComponent`.

*Páginas/ParentComponent. Razor*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=5-6)]

## <a name="child-content"></a>Conteúdo filho

Os componentes podem definir o conteúdo de outro componente. O componente de atribuição fornece o conteúdo entre as marcas que especificam o componente de recebimento.

No exemplo a seguir, o `ChildComponent` tem uma propriedade `ChildContent` que representa um `RenderFragment`, que representa um segmento de interface do usuário a ser renderizado. O valor de `ChildContent` é posicionado na marcação do componente onde o conteúdo deve ser renderizado. O valor de `ChildContent` é recebido do componente pai e renderizado no `panel-body` do painel de inicialização.

*Componentes/ChildComponent. Razor*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> A propriedade que recebe o conteúdo `RenderFragment` deve ser nomeada `ChildContent` por convenção.

O `ParentComponent` a seguir pode fornecer conteúdo para renderizar o `ChildComponent` colocando o conteúdo dentro das marcas `<ChildComponent>`.

*Páginas/ParentComponent. Razor*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a>Atributo nivelamento e parâmetros arbitrários

Os componentes podem capturar e renderizar atributos adicionais além dos parâmetros declarados do componente. Atributos adicionais podem ser capturados em um dicionário e, em seguida, *splatted* em um elemento quando o componente é renderizado usando a diretiva [@attributes](xref:mvc/views/razor#attributes) Razor. Esse cenário é útil ao definir um componente que produz um elemento de marcação que dá suporte a uma variedade de personalizações. Por exemplo, pode ser entediante definir atributos separadamente para um `<input>` que ofereça suporte a muitos parâmetros.

No exemplo a seguir, o primeiro elemento `<input>` (`id="useIndividualParams"`) usa parâmetros de componente individuais, enquanto o segundo elemento `<input>` (`id="useAttributesDict"`) usa o atributo nivelamento:

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

O tipo do parâmetro deve implementar `IEnumerable<KeyValuePair<string, object>>` com chaves de cadeia de caracteres. Usar `IReadOnlyDictionary<string, object>` também é uma opção nesse cenário.

Os elementos `<input>` renderizados usando ambas as abordagens são idênticos:

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

Para aceitar atributos arbitrários, defina um parâmetro de componente usando o atributo `[Parameter]` com a propriedade `CaptureUnmatchedValues` definida como `true`:

```cshtml
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

A propriedade `CaptureUnmatchedValues` em `[Parameter]` permite que o parâmetro corresponda a todos os atributos que não correspondem a nenhum outro parâmetro. Um componente só pode definir um único parâmetro com `CaptureUnmatchedValues`. O tipo de propriedade usado com `CaptureUnmatchedValues` deve ser atribuível de `Dictionary<string, object>` com chaves de cadeia de caracteres. `IEnumerable<KeyValuePair<string, object>>` ou `IReadOnlyDictionary<string, object>` também são opções neste cenário.

## <a name="data-binding"></a>Associação de dados

A vinculação de dados para os componentes e os elementos DOM é realizada com o atributo [@bind](xref:mvc/views/razor#bind) . O exemplo a seguir associa uma propriedade `CurrentValue` ao valor da caixa de texto:

```cshtml
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

Quando a caixa de texto perde o foco, o valor da propriedade é atualizado.

A caixa de texto é atualizada na interface do usuário somente quando o componente é renderizado, não em resposta à alteração do valor da propriedade. Como os componentes são renderizados após a execução do código do manipulador de eventos, as atualizações de propriedade *geralmente* são refletidas na interface do usuário imediatamente após um manipulador de eventos ser disparado.

O uso de `@bind` com a propriedade `CurrentValue` (`<input @bind="CurrentValue" />`) é essencialmente equivalente ao seguinte:

```cshtml
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

Quando o componente é renderizado, o `value` do elemento input é proveniente da propriedade `CurrentValue`. Quando o usuário digita na caixa de texto e altera o foco do elemento, o evento `onchange` é acionado e a propriedade `CurrentValue` é definida como o valor alterado. Na realidade, a geração de código é mais complexa porque `@bind` trata casos em que as conversões de tipo são executadas. Em princípio, `@bind` associa o valor atual de uma expressão a um atributo `value` e manipula as alterações usando o manipulador registrado.

Além de manipular eventos `onchange` com a sintaxe `@bind`, uma propriedade ou um campo pode ser associado usando outros eventos, especificando um atributo [@bind-value](xref:mvc/views/razor#bind) com um parâmetro `event` ([@bind-value:event](xref:mvc/views/razor#bind)). O exemplo a seguir associa a propriedade `CurrentValue` para o evento `oninput`:

```cshtml
<input @bind-value="CurrentValue" @bind-value:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

Ao contrário de `onchange`, que é disparado quando o elemento perde o foco, `oninput` é acionado quando o valor da caixa de texto é alterado.

**Valores não analisáveis**

Quando um usuário fornece um valor não analisável para um elemento de ligação de valores, o valor não analisável é revertido automaticamente para seu valor anterior quando o evento de ligação é disparado.

Considere o seguinte cenário:

* Um elemento `<input>` está associado a um tipo `int` com um valor inicial de `123`:

  ```cshtml
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* O usuário atualiza o valor do elemento para `123.45` na página e altera o foco do elemento.

No cenário anterior, o valor do elemento é revertido para `123`. Quando o valor `123.45` é rejeitado em favor do valor original de `123`, o usuário entende que seu valor não foi aceito.

Por padrão, a associação aplica-se ao evento `onchange` do elemento (`@bind="{PROPERTY OR FIELD}"`). Use `@bind-value="{PROPERTY OR FIELD}" @bind-value:event={EVENT}` para definir um evento diferente. Para o evento `oninput` (`@bind-value:event="oninput"`), a reversão ocorre após qualquer pressionamento de tecla que introduz um valor não analisável. Ao direcionar o evento `oninput` com um tipo de-1 @no__t, um usuário é impedido de digitar um caractere `.`. Um caractere `.` é removido imediatamente e, portanto, o usuário recebe comentários imediatos de que apenas números inteiros são permitidos. Há cenários em que a reversão do valor no evento `oninput` não é ideal, por exemplo, quando o usuário deve ter permissão para limpar um valor de `<input>` não analisável. As alternativas incluem:

* Não use o evento `oninput`. Use o evento `onchange` padrão (`@bind="{PROPERTY OR FIELD}"`), em que um valor inválido não é revertido até que o elemento perca o foco.
* Associar a um tipo anulável, como `int?` ou `string`, e fornecer uma lógica personalizada para manipular entradas inválidas.
* Use um [componente de validação de formulário](xref:blazor/forms-validation), como `InputNumber` ou `InputDate`. Os componentes de validação de formulário têm suporte interno para gerenciar entradas inválidas. Componentes de validação de formulário:
  * Permitir que o usuário forneça erros de entrada e de validação inválidos no `EditContext` associado.
  * Exibir erros de validação na interface de usuário sem interferir no usuário inserindo dados adicionais do WebForms.

**Globalização**

os valores de `@bind` são formatados para exibição e analisados usando as regras da cultura atual.

A cultura atual pode ser acessada a partir da propriedade <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName>.

[CultureInfo. InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) é usado para os seguintes tipos de campo (`<input type="{TYPE}" />`):

* `date`
* `number`

Os tipos de campo anteriores:

* São exibidos usando suas regras de formatação baseadas em navegador apropriadas.
* Não pode conter texto de forma livre.
* Fornecer características de interação do usuário com base na implementação do navegador.

Os seguintes tipos de campo têm requisitos de formatação específicos e atualmente não são compatíveis com o mais grande, pois não têm suporte de todos os principais navegadores:

* `datetime-local`
* `month`
* `week`

`@bind` dá suporte ao parâmetro `@bind:culture` para fornecer um <xref:System.Globalization.CultureInfo?displayProperty=fullName> para análise e formatação de um valor. Não é recomendável especificar uma cultura ao usar os tipos de campo `date` e `number`. `date` e `number` têm suporte interno de mais alto que fornece a cultura necessária.

Para obter informações sobre como definir a cultura do usuário, consulte a seção [localização](#localization) .

**Formatar cadeias de caracteres**

A vinculação de dados funciona com cadeias de caracteres de formato <xref:System.DateTime> usando [@bind:format](xref:mvc/views/razor#bind). Outras expressões de formato, como formatos de moeda ou número, não estão disponíveis no momento.

```cshtml
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

No código anterior, o tipo de campo do elemento `<input>` (`type`) usa como padrão `text`. `@bind:format` tem suporte para ligar os seguintes tipos .NET:

* <xref:System.DateTime?displayProperty=fullName>
* <xref:System.DateTime?displayProperty=fullName>?
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <xref:System.DateTimeOffset?displayProperty=fullName>?

O atributo `@bind:format` especifica o formato de data a ser aplicado ao `value` do elemento `<input>`. O formato também é usado para analisar o valor quando ocorre um evento `onchange`.

Não é recomendável especificar um formato para o tipo de campo `date` porque o mais alto tem suporte interno para formatar datas.

**Parâmetros do componente**

A associação reconhece os parâmetros do componente, em que `@bind-{property}` pode associar um valor de propriedade entre componentes.

O componente filho a seguir (`ChildComponent`) tem um parâmetro de componente `Year` e um retorno de chamada `YearChanged`:

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

`EventCallback<T>` é explicado na seção [EventCallback](#eventcallback) .

O componente pai a seguir usa `ChildComponent` e associa o parâmetro `ParentYear` do pai ao parâmetro `Year` no componente filho:

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

Carregar o `ParentComponent` produz a seguinte marcação:

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

Se o valor da propriedade `ParentYear` for alterado selecionando o botão no `ParentComponent`, a propriedade `Year` do `ChildComponent` será atualizada. O novo valor de `Year` é renderizado na interface do usuário quando o `ParentComponent` é rerenderizado:

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

O parâmetro `Year` é acoplável porque ele tem um evento complementar `YearChanged` que corresponde ao tipo do parâmetro `Year`.

Por convenção, `<ChildComponent @bind-Year="ParentYear" />` é essencialmente equivalente a escrever:

```cshtml
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

Em geral, uma propriedade pode ser associada a um manipulador de eventos correspondente usando o atributo `@bind-property:event`. Por exemplo, a propriedade `MyProp` pode ser associada a `MyEventHandler` usando os dois atributos a seguir:

```cshtml
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="event-handling"></a>Manipulação de eventos

Os componentes do Razor fornecem recursos de manipulação de eventos. Para um atributo de elemento HTML chamado `on{event}` (por exemplo, `onclick` e `onsubmit`) com um valor de tipo delegado, os componentes do Razor tratam o valor do atributo como um manipulador de eventos. O nome do atributo é sempre formatado [@on {Event}](xref:mvc/views/razor#onevent).

O código a seguir chama o método `UpdateHeading` quando o botão é selecionado na interface do usuário:

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

O código a seguir chama o método `CheckChanged` quando a caixa de seleção é alterada na interface do usuário:

```cshtml
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

Os manipuladores de eventos também podem ser assíncronos e retornar um <xref:System.Threading.Tasks.Task>. Não é necessário chamar `StateHasChanged()` manualmente. As exceções são registradas quando ocorrem.

No exemplo a seguir, `UpdateHeading` é chamado de forma assíncrona quando o botão é selecionado:

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

### <a name="event-argument-types"></a>Tipos de argumento de evento

Para alguns eventos, são permitidos tipos de argumento de evento. Se o acesso a um desses tipos de evento não for necessário, ele não será necessário na chamada do método.

@No__t-0 com suporte são mostrados na tabela a seguir.

| evento | Classe |
| ----- | ----- |
| Área de Transferência        | `ClipboardEventArgs` |
| Arraste             | `DragEventArgs` &ndash; `DataTransfer` e `DataTransferItem` mantêm os dados do item arrastados. |
| Erro            | `ErrorEventArgs` |
| Foco            | `FocusEventArgs` &ndash; não inclui suporte para `relatedTarget`. |
| alteração `<input>` | `ChangeEventArgs` |
| Teclado         | `KeyboardEventArgs` |
| Mouse            | `MouseEventArgs` |
| Ponteiro do mouse    | `PointerEventArgs` |
| Roda do mouse      | `WheelEventArgs` |
| Progresso         | `ProgressEventArgs` |
| Toque            | `TouchEventArgs` &ndash; `TouchPoint` representa um único ponto de contato em um dispositivo sensível ao toque. |

Para obter informações sobre as propriedades e o comportamento de manipulação de eventos dos eventos na tabela anterior, consulte [classes EventArgs na fonte de referência (ramificação ASPNET/AspNetCore Release/3.0)](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Components/Web/src/Web).

### <a name="lambda-expressions"></a>Expressões lambda

As expressões lambda também podem ser usadas:

```cshtml
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

Geralmente, é conveniente fechar valores adicionais, como ao iterar em um conjunto de elementos. O exemplo a seguir cria três botões, cada um dos quais chamadas `UpdateHeading` passando um argumento de evento (`MouseEventArgs`) e seu número de botão (`buttonNumber`) quando selecionado na interface do usuário:

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
> **Não** use a variável de loop (`i`) em um loop `for` diretamente em uma expressão lambda. Caso contrário, a mesma variável é usada por todas as expressões lambda que fazem com que @no__t valor de 0 seja o mesmo em todos os lambdas. Sempre Capture seu valor em uma variável local (`buttonNumber` no exemplo anterior) e, em seguida, use-o.

### <a name="eventcallback"></a>EventCallback

Um cenário comum com componentes aninhados é o desejo de executar o método de um componente pai quando um evento de componente filho ocorre no exemplo @ no__t-0for, quando um evento `onclick` ocorre no filho. Para expor eventos entre componentes, use um `EventCallback`. Um componente pai pode atribuir um método de retorno de chamada para o `EventCallback` de um componente filho.

O `ChildComponent` no aplicativo de exemplo demonstra como o manipulador de `onclick` do botão é configurado para receber um delegado `EventCallback` da @no__t de exemplo-3. O `EventCallback` é digitado com `MouseEventArgs`, que é apropriado para um evento `onclick` de um dispositivo periférico:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=5-7,17-18)]

O `ParentComponent` define o `EventCallback<T>` do filho para seu método `ShowMessage`:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=6,16-19)]

Quando o botão for selecionado na `ChildComponent`:

* O método `ShowMessage` do `ParentComponent` é chamado. `messageText` é atualizado e exibido no `ParentComponent`.
* Uma chamada para `StateHasChanged` não é necessária no método do retorno de chamada (`ShowMessage`). `StateHasChanged` é chamado automaticamente para renderizar novamente o `ParentComponent`, assim como os eventos filho disparam o reprocessamento de componentes em manipuladores de eventos que são executados dentro do filho.

`EventCallback` e `EventCallback<T>` permitem delegados assíncronos. `EventCallback<T>` é fortemente tipado e requer um tipo de argumento específico. `EventCallback` é digitado de forma fraca e permite qualquer tipo de argumento.

```cshtml
<p><b>@messageText</b></p>

@{ var message = "Default Text"; }

<ChildComponent 
    OnClick="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />

@code {
    private string messageText;
}
```

Invoque um `EventCallback` ou `EventCallback<T>` com `InvokeAsync` e aguardar o <xref:System.Threading.Tasks.Task>:

```csharp
await callback.InvokeAsync(arg);
```

Use `EventCallback` e `EventCallback<T>` para manipulação de eventos e parâmetros de componente de associação.

Prefira o `EventCallback<T>` com rigidez de tipos sobre `EventCallback`. `EventCallback<T>` fornece melhores comentários de erro para os usuários do componente. Semelhante a outros manipuladores de eventos de interface do usuário, especificar o parâmetro de evento é opcional. Use `EventCallback` quando não houver valor passado para o retorno de chamada.

## <a name="chained-bind"></a>Associação encadeada

Um cenário comum é encadear um parâmetro de associação de dados a um elemento de página na saída do componente. Esse cenário é chamado de *Associação encadeada* porque vários níveis de associação ocorrem simultaneamente.

Uma associação encadeada não pode ser implementada com a sintaxe `@bind` no elemento da página. O manipulador de eventos e o valor devem ser especificados separadamente. Um componente pai, no entanto, pode usar a sintaxe `@bind` com o parâmetro do componente.

O seguinte componente `PasswordField` (*passwordField. Razor*):

* Define o valor de um elemento `<input>` para uma propriedade `Password`.
* Expõe as alterações da propriedade `Password` em um componente pai com um [EventCallback](#eventcallback).

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

O componente `PasswordField` é usado em outro componente:

```cshtml
<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

Para executar verificações ou interceptar erros na senha no exemplo anterior:

* Crie um campo de backup para `Password` (`password` no código de exemplo a seguir).
* Execute os erros de verificação ou interceptação no setter `Password`.

O exemplo a seguir fornecerá comentários imediatos para o usuário se um espaço for usado no valor da senha:

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

## <a name="capture-references-to-components"></a>Capturar referências a componentes

As referências de componente fornecem uma maneira de fazer referência a uma instância de componente para que você possa emitir comandos para essa instância, como `Show` ou `Reset`. Para capturar uma referência de componente:

* Adicione um atributo [@ref](xref:mvc/views/razor#ref) ao componente filho.
* Defina um campo com o mesmo tipo do componente filho.

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

Quando o componente é renderizado, o campo `loginDialog` é populado com a instância de componente filho `MyLoginDialog`. Em seguida, você pode invocar os métodos .NET na instância do componente.

> [!IMPORTANT]
> A variável `loginDialog` só é populada depois que o componente é renderizado e sua saída inclui o elemento `MyLoginDialog`. Até esse ponto, não há nada a fazer referência. Para manipular referências de componentes após a conclusão da renderização do componente, use os métodos `OnAfterRenderAsync` ou `OnAfterRender`.

Embora a captura de referências de componente use uma sintaxe semelhante à [captura de referências de elemento](xref:blazor/javascript-interop#capture-references-to-elements), ela não é um recurso de [interoperabilidade do JavaScript](xref:blazor/javascript-interop) . As referências de componente não são passadas para o código JavaScript @ no__t-0they're usado somente no código .NET.

> [!NOTE]
> Não **use referências** de componente para converter o estado dos componentes filho. Em vez disso, use parâmetros declarativos normais para passar dados para componentes filho. O uso de parâmetros declarativos normais resulta em componentes filho que são reprocessados nos horários corretos automaticamente.

## <a name="invoke-component-methods-externally-to-update-state"></a>Invocar métodos de componente externamente para atualizar o estado

O mais alto uso de um `SynchronizationContext` para impor um único thread lógico de execução. Os métodos de ciclo de vida de um componente e quaisquer retornos de chamada de evento que são gerados pelo mais alto são executados nesse `SynchronizationContext`. No caso de um componente precisar ser atualizado com base em um evento externo, como um temporizador ou outras notificações, use o método `InvokeAsync`, que será redespachado para o `SynchronizationContext` de mais Altova.

Por exemplo, considere um *serviço de notificação* que pode notificar qualquer componente de escuta do estado atualizado:

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

Uso do `NotifierService` para atualizar um componente:

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

No exemplo anterior, `NotifierService` invoca o método `OnNotify` do componente fora do @no__t do mais alto-2. `InvokeAsync` é usado para alternar para o contexto correto e enfileirar uma renderização.

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a>Use o \@key para controlar a preservação de elementos e componentes

Ao renderizar uma lista de elementos ou componentes e, subsequentemente, os elementos ou componentes são alterados, o algoritmo de diferenciação do mais claro deve decidir quais elementos ou componentes anteriores podem ser mantidos e como os objetos de modelo devem ser mapeados para eles. Normalmente, esse processo é automático e pode ser ignorado, mas há casos em que você talvez queira controlar o processo.

Considere o exemplo a seguir:

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

O conteúdo da coleção `People` pode ser alterado com entradas inseridas, excluídas ou reordenadas. Quando o componente é rerenderizado, o componente `<DetailsEditor>` pode ser alterado para receber diferentes valores de parâmetro `Details`. Isso pode causar um reprocessamento mais complexo do que o esperado. Em alguns casos, a rerenderização pode levar a diferenças de comportamento visíveis, como o foco de elemento perdido.

O processo de mapeamento pode ser controlado com o atributo de diretiva `@key`. `@key` faz com que o algoritmo diff garanta a preservação de elementos ou componentes com base no valor da chave:

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

Quando a coleção `People` é alterada, o algoritmo diff mantém a associação entre as instâncias `<DetailsEditor>` e `person`:

* Se um `Person` for excluído da lista de `People`, somente a instância `<DetailsEditor>` correspondente será removida da interface do usuário. Outras instâncias permanecem inalteradas.
* Se um `Person` for inserido em alguma posição na lista, uma nova instância de `<DetailsEditor>` será inserida na posição correspondente. Outras instâncias permanecem inalteradas.
* Se as entradas `Person` forem reordenadas, as instâncias `<DetailsEditor>` correspondentes serão preservadas e reordenadas na interface do usuário.

Em alguns cenários, o uso de `@key` minimiza a complexidade de rerenderização e evita possíveis problemas com partes com estado da alteração do DOM, como posição de foco.

> [!IMPORTANT]
> As chaves são locais para cada elemento ou componente de contêiner. As chaves não são comparadas globalmente ao longo do documento.

### <a name="when-to-use-key"></a>Quando usar o \@key

Normalmente, faz sentido usar `@key` sempre que uma lista é renderizada (por exemplo, em um bloco `@foreach`) e um valor adequado existe para definir o `@key`.

Você também pode usar `@key` para impedir que o mais alto de preservar uma subárvore de elemento ou componente quando um objeto for alterado:

```cshtml
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

Se `@currentPerson` for alterado, a diretiva de atributo `@key` forçará a descartar todo o `<div>` e seus descendentes e recriará a subárvore na interface do usuário com novos elementos e componentes. Isso pode ser útil se você precisar garantir que nenhum estado da interface do usuário seja preservado quando `@currentPerson` for alterado.

### <a name="when-not-to-use-key"></a>Quando não usar \@key

Há um custo de desempenho ao comparar com `@key`. O custo de desempenho não é grande, mas só especifique `@key` se controlar as regras de preservação de elemento ou componente beneficiarem o aplicativo.

Mesmo que `@key` não seja usado, o mais alto preserva as instâncias de elemento filho e de componente o máximo possível. A única vantagem de usar `@key` é o controle sobre *como* as instâncias de modelo são mapeadas para as instâncias de componente preservadas, em vez do algoritmo diff, selecionando o mapeamento.

### <a name="what-values-to-use-for-key"></a>Quais valores usar para \@key

Geralmente, faz sentido fornecer um dos seguintes tipos de valor para `@key`:

* Instâncias de objeto de modelo (por exemplo, uma instância `Person` como no exemplo anterior). Isso garante a preservação com base na igualdade de referência de objeto.
* Identificadores exclusivos (por exemplo, valores de chave primária do tipo `int`, `string` ou `Guid`).

Verifique se os valores usados para `@key` não estão em conflito. Se os valores conflitantes forem detectados no mesmo elemento pai, o mais velho lançará uma exceção porque não pode mapear determinísticamente elementos ou componentes antigos para novos elementos ou componentes. Use apenas valores distintos, como instâncias de objeto ou valores de chave primária.

## <a name="lifecycle-methods"></a>Métodos de ciclo de vida

`OnInitializedAsync` e `OnInitialized` executam o código para inicializar o componente. Para executar uma operação assíncrona, use `OnInitializedAsync` e a palavra-chave `await` na operação:

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

Para uma operação síncrona, use `OnInitialized`:

```csharp
protected override void OnInitialized()
{
    ...
}
```

`OnParametersSetAsync` e `OnParametersSet` são chamados quando um componente recebe parâmetros de seu pai e os valores são atribuídos às propriedades. Esses métodos são executados após a inicialização do componente e cada vez que o componente é renderizado:

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

`OnAfterRenderAsync` e `OnAfterRender` são chamados após a conclusão da renderização de um componente. Referências de elemento e componente são preenchidas neste ponto. Use este estágio para executar etapas de inicialização adicionais usando o conteúdo renderizado, como a ativação de bibliotecas JavaScript de terceiros que operam nos elementos DOM renderizados.

`OnAfterRender` *não é chamado durante o pré-processamento no servidor.*

O parâmetro `firstRender` para `OnAfterRenderAsync` e `OnAfterRender` é:

* Defina como `true` na primeira vez que a instância do componente for invocada.
* Garante que o trabalho de inicialização seja executado apenas uma vez.

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

### <a name="handle-incomplete-async-actions-at-render"></a>Tratar ações assíncronas incompletas no processamento

Ações assíncronas executadas em eventos de ciclo de vida podem não ter sido concluídas antes que o componente seja renderizado. Os objetos podem ser `null` ou preenchidos incompletamente com dados enquanto o método de ciclo de vida está em execução. Forneça a lógica de renderização para confirmar que os objetos são inicializados. Renderizar elementos de interface do usuário de espaço reservado (por exemplo, uma mensagem de carregamento) enquanto os objetos são `null`.

No componente `FetchData` dos modelos mais fáceis, `OnInitializedAsync` é substituído para Asychronously receber dados de previsão (`forecasts`). Quando `forecasts` é `null`, é exibida uma mensagem de carregamento para o usuário. Depois que o `Task` retornado por `OnInitializedAsync` for concluído, o componente será rerenderizado com o estado atualizado.

*Pages/FetchData.razor*:

[!code-cshtml[](components/samples_snapshot/3.x/FetchData.razor?highlight=9)]

### <a name="execute-code-before-parameters-are-set"></a>Executar código antes que os parâmetros sejam definidos

`SetParameters` pode ser substituído para executar o código antes de os parâmetros serem definidos:

```csharp
public override void SetParameters(ParameterView parameters)
{
    ...

    base.SetParameters(parameters);
}
```

Se `base.SetParameters` não for invocado, o código personalizado poderá interpretar o valor dos parâmetros de entrada de qualquer forma necessária. Por exemplo, os parâmetros de entrada não precisam ser atribuídos às propriedades na classe.

### <a name="suppress-refreshing-of-the-ui"></a>Suprimir a atualização da interface do usuário

`ShouldRender` pode ser substituído para suprimir a atualização da interface do usuário. Se a implementação retornar `true`, a interface do usuário será atualizada. Mesmo se `ShouldRender` for substituído, o componente sempre será renderizado inicialmente.

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

## <a name="component-disposal-with-idisposable"></a>Descarte de componentes com IDisposable

Se um componente implementar <xref:System.IDisposable>, o [método Dispose](/dotnet/standard/garbage-collection/implementing-dispose) será chamado quando o componente for removido da interface do usuário. O componente a seguir usa `@implements IDisposable` e o método `Dispose`:

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

## <a name="routing"></a>Roteamento

O roteamento no mais fácil é obtido fornecendo um modelo de rota para cada componente acessível no aplicativo.

Quando um arquivo Razor com uma diretiva `@page` é compilado, a classe gerada recebe um <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> especificando o modelo de rota. Em tempo de execução, o roteador procura classes de componentes com um `RouteAttribute` e renderiza qualquer componente que tenha um modelo de rota que corresponda à URL solicitada.

Vários modelos de rota podem ser aplicados a um componente. O componente a seguir responde às solicitações de `/BlazorRoute` e `/DifferentBlazorRoute`:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

## <a name="route-parameters"></a>Parâmetros de rota

Os componentes podem receber parâmetros de rota do modelo de rota fornecido na diretiva `@page`. O roteador usa parâmetros de rota para preencher os parâmetros de componente correspondentes.

*Componente de parâmetro de rota*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter)]

Não há suporte para parâmetros opcionais, portanto, duas diretivas `@page` são aplicadas no exemplo acima. O primeiro permite a navegação para o componente sem um parâmetro. A segunda diretiva `@page` usa o parâmetro de rota `{text}` e atribui o valor à propriedade `Text`.

## <a name="base-class-inheritance-for-a-code-behind-experience"></a>Herança de classe base para uma experiência de "code-behind"

Os arquivos de componente misturam C# o código de marcação e processamento HTML no mesmo arquivo. A diretiva `@inherits` pode ser usada para fornecer aos aplicativos mais incrivelmente uma experiência "code-behind" que separa a marcação de componente do código de processamento.

O [aplicativo de exemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) mostra como um componente pode herdar uma classe base, `BlazorRocksBase`, para fornecer as propriedades e os métodos do componente.

*Páginas/BlazorRocks. Razor*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRocks.razor?name=snippet_BlazorRocks)]

*BlazorRocksBase.cs*:

[!code-csharp[](common/samples/3.x/BlazorSample/Pages/BlazorRocksBase.cs)]

A classe base deve derivar de `ComponentBase`.

## <a name="import-components"></a>Importar componentes

O namespace de um componente criado com o Razor baseia-se em (em ordem de prioridade):

* designação [@namespace](xref:mvc/views/razor#namespace) na marcação de arquivo Razor ( *. Razor*) (`@namespace BlazorSample.MyNamespace`).
* O `RootNamespace` do projeto no arquivo de projeto (`<RootNamespace>BlazorSample</RootNamespace>`).
* O nome do projeto, obtido do nome do arquivo do projeto ( *. csproj*) e o caminho da raiz do projeto para o componente. Por exemplo, a estrutura resolve *{raiz do projeto}/pages/index.Razor* (*BlazorSample. csproj*) para o namespace `BlazorSample.Pages`. Os componentes C# seguem regras de associação de nome. Para o componente `Index` neste exemplo, os componentes no escopo são todos os componentes:
  * Na mesma pasta, *páginas*.
  * Os componentes na raiz do projeto que não especificam explicitamente um namespace diferente.

Os componentes definidos em um namespace diferente são trazidos para o escopo usando [a diretiva @using](xref:mvc/views/razor#using) do Razor.

Se outro componente, `NavMenu.razor`, existir na pasta *BlazorSample/Shared/* , o componente poderá ser usado no `Index.razor` com a seguinte instrução `@using`:

```cshtml
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

Os componentes também podem ser referenciados usando seus nomes totalmente qualificados, o que não requer a diretiva [@using](xref:mvc/views/razor#using) :

```cshtml
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> Não há suporte para a qualificação `global::`.
>
> Não há suporte para a importação de componentes com instruções `using` com alias (por exemplo, `@using Foo = Bar`).
>
> Não há suporte para nomes parcialmente qualificados. Por exemplo, não há suporte para a adição de `@using BlazorSample` e referência a `NavMenu.razor` com `<Shared.NavMenu></Shared.NavMenu>`.

## <a name="conditional-html-element-attributes"></a>Atributos de elemento HTML condicional

Os atributos de elemento HTML são processados condicionalmente com base no valor do .NET. Se o valor for `false` ou `null`, o atributo não será renderizado. Se o valor é `true`, o atributo é processado minimizado.

No exemplo a seguir, `IsCompleted` determina se `checked` é renderizado na marcação do elemento:

```cshtml
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

Se `IsCompleted` for `true`, a caixa de seleção será renderizada como:

```html
<input type="checkbox" checked />
```

Se `IsCompleted` for `false`, a caixa de seleção será renderizada como:

```html
<input type="checkbox" />
```

Para obter mais informações, consulte <xref:mvc/views/razor>.

> [!WARNING]
> Alguns atributos HTML, como [pressionados pelo Aria](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), não funcionam corretamente quando o tipo .net é um `bool`. Nesses casos, use um tipo `string` em vez de um `bool`.

## <a name="raw-html"></a>HTML bruto

Normalmente, as cadeias de caracteres são renderizadas usando nós de texto DOM, o que significa que qualquer marcação que ela possa conter será ignorada e tratada como texto literal. Para renderizar HTML bruto, empacote o conteúdo HTML em um valor `MarkupString`. O valor é analisado como HTML ou SVG e inserido no DOM.

> [!WARNING]
> O processamento de HTML bruto construído a partir de qualquer fonte não confiável é um **risco à segurança** e deve ser evitado!

O exemplo a seguir mostra o uso do tipo `MarkupString` para adicionar um bloco de conteúdo HTML estático à saída renderizada de um componente:

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="templated-components"></a>Componentes de modelo

Componentes modelo são componentes que aceitam um ou mais modelos de interface do usuário como parâmetros, que podem ser usados como parte da lógica de renderização do componente. Os componentes de modelo permitem que você crie componentes de nível superior que são mais reutilizáveis do que os componentes normais. Alguns exemplos incluem:

* Um componente de tabela que permite que um usuário especifique modelos para o cabeçalho, as linhas e o rodapé da tabela.
* Um componente de lista que permite que um usuário especifique um modelo para renderizar itens em uma lista.

### <a name="template-parameters"></a>Parâmetros do modelo

Um componente modelo é definido especificando um ou mais parâmetros de componente do tipo `RenderFragment` ou `RenderFragment<T>`. Um fragmento de renderização representa um segmento de interface do usuário a ser renderizado. `RenderFragment<T>` usa um parâmetro de tipo que pode ser especificado quando o fragmento de renderização é invocado.

componente `TableTemplate`:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TableTemplate.razor)]

Ao usar um componente modelo, os parâmetros do modelo podem ser especificados usando elementos filho que correspondem aos nomes dos parâmetros (`TableHeader` e `RowTemplate` no exemplo a seguir):

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

### <a name="template-context-parameters"></a>Parâmetros de contexto de modelo

Os argumentos de componente do tipo `RenderFragment<T>` passados como elementos têm um parâmetro implícito denominado `context` (por exemplo, do exemplo de código anterior, `@context.PetId`), mas você pode alterar o nome do parâmetro usando o atributo `Context` no elemento filho. No exemplo a seguir, o atributo `Context` do elemento `RowTemplate` especifica o parâmetro `pet`:

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

Como alternativa, você pode especificar o atributo `Context` no elemento Component. O atributo `Context` especificado se aplica a todos os parâmetros de modelo especificados. Isso pode ser útil quando você deseja especificar o nome do parâmetro de conteúdo para conteúdo filho implícito (sem qualquer elemento filho de disposição). No exemplo a seguir, o atributo `Context` aparece no elemento `TableTemplate` e se aplica a todos os parâmetros de modelo:

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

### <a name="generic-typed-components"></a>Componentes de tipo genérico

Os componentes modelo são geralmente digitados genericamente. Por exemplo, um componente `ListViewTemplate` genérico pode ser usado para renderizar valores `IEnumerable<T>`. Para definir um componente genérico, use a diretiva [@typeparam](xref:mvc/views/razor#typeparam) para especificar parâmetros de tipo:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ListViewTemplate.razor)]

Ao usar componentes de tipos genéricos, o parâmetro de tipo é inferido, se possível:

```cshtml
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

Caso contrário, o parâmetro de tipo deve ser especificado explicitamente usando um atributo que corresponda ao nome do parâmetro de tipo. No exemplo a seguir, `TItem="Pet"` especifica o tipo:

```cshtml
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

## <a name="cascading-values-and-parameters"></a>Valores e parâmetros em cascata

Em alguns cenários, é inconveniente fluir dados de um componente ancestral para um componente descendente usando [parâmetros de componente](#component-parameters), especialmente quando há várias camadas de componente. Valores e parâmetros em cascata resolvem esse problema fornecendo uma maneira conveniente para um componente ancestral fornecer um valor para todos os seus componentes descendentes. Valores e parâmetros em cascata também fornecem uma abordagem para que os componentes sejam coordenados.

### <a name="theme-example"></a>Exemplo de tema

No exemplo a seguir do aplicativo de exemplo, a classe `ThemeInfo` especifica as informações do tema para fluir para baixo na hierarquia do componente para que todos os botões dentro de uma determinada parte do aplicativo compartilhem o mesmo estilo.

*UIThemeClasses/ThemeInfo.cs*:

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

Um componente ancestral pode fornecer um valor em cascata usando o componente de valor em cascata. O componente `CascadingValue` encapsula uma subárvore da hierarquia do componente e fornece um único valor para todos os componentes dentro dessa subárvore.

Por exemplo, o aplicativo de exemplo especifica informações de tema (`ThemeInfo`) em um dos layouts do aplicativo como um parâmetro em cascata para todos os componentes que compõem o corpo do layout da propriedade `@Body`. `ButtonClass` recebe um valor de `btn-success` no componente de layout. Qualquer componente descendente pode consumir essa propriedade por meio do objeto em cascata `ThemeInfo`.

componente `CascadingValuesParametersLayout`:

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

Para fazer uso de valores em cascata, os componentes declaram parâmetros em cascata usando o atributo `[CascadingParameter]`. Os valores em cascata são associados a parâmetros em cascata por tipo.

No aplicativo de exemplo, o componente `CascadingValuesParametersTheme` associa o valor em cascata `ThemeInfo` a um parâmetro em cascata. O parâmetro é usado para definir a classe CSS para um dos botões exibidos pelo componente.

componente `CascadingValuesParametersTheme`:

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

Para propagar vários valores do mesmo tipo dentro da mesma subárvore, forneça uma cadeia de caracteres `Name` exclusiva para cada componente `CascadingValue` e seu `CascadingParameter` correspondente. No exemplo a seguir, dois componentes `CascadingValue` em cascata diferentes instâncias de `MyCascadingType` por nome:

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

Em um componente descendente, os parâmetros em cascata recebem seus valores dos valores em cascata correspondentes no componente ancestral por nome:

```cshtml
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a>Exemplo de TabSet

Os parâmetros em cascata também permitem que os componentes colaborem na hierarquia do componente. Por exemplo, considere o exemplo de *TabSet* a seguir no aplicativo de exemplo.

O aplicativo de exemplo tem uma interface `ITab` que as guias implementam:

[!code-csharp[](common/samples/3.x/BlazorSample/UIInterfaces/ITab.cs)]

O componente `CascadingValuesParametersTabSet` usa o componente `TabSet`, que contém vários componentes `Tab`:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/CascadingValuesParametersTabSet.razor?name=snippet_TabSet)]

Os componentes filho `Tab` não são passados explicitamente como parâmetros para o `TabSet`. Em vez disso, os componentes filho `Tab` fazem parte do conteúdo filho do `TabSet`. No entanto, o `TabSet` ainda precisa saber sobre cada componente `Tab` para que ele possa renderizar os cabeçalhos e a guia ativa. Para habilitar essa coordenação sem a necessidade de código adicional, o componente `TabSet` *pode fornecer a si mesmo como um valor em cascata* que é então coletado pelos componentes do `Tab` descendentes.

componente `TabSet`:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TabSet.razor)]

Os componentes descendentes `Tab` capturam o `TabSet` que o contém como um parâmetro em cascata, para que os componentes `Tab` se adicionem ao `TabSet` e coordenada em qual guia está ativa.

componente `Tab`:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/Tab.razor)]

## <a name="razor-templates"></a>Modelos do Razor

Os fragmentos de renderização podem ser definidos usando a sintaxe de modelo Razor. Os modelos Razor são uma maneira de definir um trecho de interface do usuário e assumir o seguinte formato:

```cshtml
@<{HTML tag}>...</{HTML tag}>
```

O exemplo a seguir ilustra como especificar valores `RenderFragment` e `RenderFragment<T>` e renderizar modelos diretamente em um componente. Os fragmentos de renderização também podem ser passados como argumentos para [componentes de modelo](#templated-components).

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

Saída renderizada do código anterior:

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Your pet's name is Rex.</p>
```

## <a name="manual-rendertreebuilder-logic"></a>Lógica RenderTreeBuilder manual

`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` fornece métodos para manipular componentes e elementos, incluindo a criação manual de componentes C# no código.

> [!NOTE]
> O uso de `RenderTreeBuilder` para criar componentes é um cenário avançado. Um componente malformado (por exemplo, uma marca de marcação não fechada) pode resultar em um comportamento indefinido.

Considere o seguinte componente `PetDetails`, que pode ser compilado manualmente em outro componente:

```cshtml
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

No exemplo a seguir, o loop no método `CreateComponent` gera três componentes `PetDetails`. Ao chamar os métodos `RenderTreeBuilder` para criar os componentes (`OpenComponent` e `AddAttribute`), os números de sequência são números de linha de código-fonte. O algoritmo de diferença mais grande do que se baseia nos números de sequência correspondentes a linhas distintas de código, não a invocações de chamada distintas. Ao criar um componente com os métodos `RenderTreeBuilder`, codifique os argumentos para números de sequência. **O uso de um cálculo ou contador para gerar o número de sequência pode levar a um desempenho insatisfatório.** Para obter mais informações, consulte os [números de sequência relacionados à seção números de linha de código e não ordem de execução](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) .

componente `BuiltContent`:

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

> ! ALERTA Os tipos no `Microsoft.AspNetCore.Components.RenderTree` permitem o processamento dos *resultados* das operações de renderização. Esses são detalhes internos da implementação da estrutura mais incrivelmente. Esses tipos devem ser considerados *instáveis* e sujeitos a alterações em versões futuras.

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a>Números de sequência se relacionam a números de linha de código e não a ordem de execução

Arquivos de mais de `.razor` são sempre compilados. Isso é potencialmente uma grande vantagem para `.razor` porque a etapa de compilação pode ser usada para injetar informações que melhoram o desempenho do aplicativo em tempo de execução.

Um exemplo importante desses aprimoramentos envolve *números de sequência*. Os números de sequência indicam ao tempo de execução que as saídas vieram de quais linhas de código distintas e ordenadas. O tempo de execução usa essas informações para gerar comparações de árvore eficientes em tempo linear, o que é muito mais rápido do que normalmente é possível para um algoritmo de comparação de árvore geral.

Considere o seguinte arquivo simples `.razor`:

```cshtml
@if (someFlag)
{
    <text>First</text>
}

Second
```

O código anterior é compilado para algo semelhante ao seguinte:

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

Quando o código é executado pela primeira vez, se `someFlag` for `true`, o Construtor receberá:

| Sequência | type      | Dados   |
| :------: | --------- | :----: |
| 0        | Nó de texto | First  |
| 1        | Nó de texto | Segundo |

Imagine que `someFlag` se torna `false` e a marcação é renderizada novamente. Desta vez, o Construtor recebe:

| Sequência | type       | Dados   |
| :------: | ---------- | :----: |
| 1        | Nó de texto  | Segundo |

Quando o tempo de execução executa uma comparação, ele vê que o item na sequência `0` foi removido e, portanto, gera o seguinte *script de edição*trivial:

* Remova o primeiro nó de texto.

#### <a name="what-goes-wrong-if-you-generate-sequence-numbers-programmatically"></a>O que vai errado se você gerar números de sequência programaticamente

Imagine, em vez disso, que você escreveu a seguinte lógica do construtor de árvore de renderização:

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

Agora, a primeira saída é:

| Sequência | Tipo      | Dados   |
| :------: | --------- | :----: |
| 0        | Nó de texto | First  |
| 1        | Nó de texto | Segundo |

Esse resultado é idêntico ao caso anterior, portanto, não existem problemas negativos. `someFlag` é `false` no segundo processamento e a saída é:

| Sequência | Tipo      | Dados   |
| :------: | --------- | ------ |
| 0        | Nó de texto | Segundo |

Desta vez, o algoritmo diff vê que *duas* alterações ocorreram e o algoritmo gera o seguinte script de edição:

* Altere o valor do primeiro nó de texto para `Second`.
* Remova o segundo nó de texto.

A geração dos números de sequência perdeu todas as informações úteis sobre onde as ramificações `if/else` e os loops estavam presentes no código original. Isso resulta em uma comparação **duas vezes mais longa** do que antes.

Esse é um exemplo trivial. Em casos mais realistas com estruturas complexas e profundamente aninhadas, e especialmente com loops, o custo de desempenho é mais grave. Em vez de identificar imediatamente quais blocos de loop ou ramificações foram inseridos ou removidos, o algoritmo diff precisa recorrer profundamente nas árvores de renderização e geralmente criar scripts de edição muito mais, pois ele é informado indiretamente sobre como as estruturas antigas e novas relacionar entre si.

#### <a name="guidance-and-conclusions"></a>Diretrizes e conclusões

* O desempenho do aplicativo será afetado se os números de sequência forem gerados dinamicamente.
* A estrutura não pode criar seus próprios números de sequência automaticamente em tempo de execução porque as informações necessárias não existem, a menos que sejam capturadas no momento da compilação.
* Não grave blocos longos de lógica `RenderTreeBuilder` implementadas manualmente. Prefira arquivos `.razor` e permita que o compilador lide com os números de sequência. Se não for possível evitar a lógica `RenderTreeBuilder` manual, divida blocos longos de código em partes menores encapsuladas em chamadas `OpenRegion` @ no__t-2 @ no__t-3. Cada região tem seu próprio espaço separado de números de sequência, para que você possa reiniciar de zero (ou qualquer outro número arbitrário) dentro de cada região.
* Se os números de sequência forem codificados, o algoritmo diff só exigirá que os números de sequência aumentem de valor. O valor inicial e as lacunas são irrelevantes. Uma opção legítima é usar o número de linha de código como o número de sequência, ou começar de zero e aumentar por um ou centenas (ou qualquer intervalo preferencial). 
* O mais alto número de seqüências usa números de sequência, enquanto outras estruturas de interface do usuário de diferenciação de árvore não as usam. A comparação é muito mais rápida quando os números de sequência são usados, e o mais vantajoso tem a vantagem de uma etapa de compilação que lida com números de sequência automaticamente para desenvolvedores que criam arquivos `.razor`.

## <a name="localization"></a>Localização

Os aplicativos de servidor mais incrivelmente são localizados usando o [middleware de localização](xref:fundamentals/localization#localization-middleware). O middleware seleciona a cultura apropriada para os usuários que solicitam recursos do aplicativo.

A cultura pode ser definida usando uma das seguintes abordagens:

* [Cookies](#cookies)
* [Fornecer interface do usuário para escolher a cultura](#provide-ui-to-choose-the-culture)

Para obter mais informações e exemplos, consulte <xref:fundamentals/localization>.

### <a name="cookies"></a>Cookies

Um cookie de cultura de localização pode persistir a cultura do usuário. O cookie é criado pelo método `OnGet` da página host do aplicativo (*pages/host. cshtml. cs*). O middleware de localização lê o cookie em solicitações subsequentes para definir a cultura do usuário. 

O uso de um cookie garante que a conexão WebSocket possa propagar corretamente a cultura. Se os esquemas de localização forem baseados no caminho da URL ou na cadeia de caracteres de consulta, o esquema pode não ser capaz de trabalhar com WebSockets, portanto, falha ao persistir a cultura. Portanto, o uso de um cookie de cultura de localização é a abordagem recomendada.

Qualquer técnica pode ser usada para atribuir uma cultura se a cultura persistir em um cookie de localização. Se o aplicativo já tiver um esquema de localização estabelecido para ASP.NET Core do lado do servidor, continue a usar a infraestrutura de localização existente do aplicativo e defina o cookie de cultura de localização no esquema do aplicativo.

O exemplo a seguir mostra como definir a cultura atual em um cookie que pode ser lido pelo middleware de localização. Crie um arquivo *pages/host. cshtml. cs* com o seguinte conteúdo no aplicativo de servidor mais incrivelmente:

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

A localização é manipulada no aplicativo:

1. O navegador envia uma solicitação HTTP inicial para o aplicativo.
1. A cultura é atribuída pelo middleware de localização.
1. O método `OnGet` em *_Host. cshtml. cs* persiste a cultura em um cookie como parte da resposta.
1. O navegador abre uma conexão WebSocket para criar uma sessão de servidor mais incrivelmente interativa.
1. O middleware de localização lê o cookie e atribui a cultura.
1. A sessão de servidor mais incrivelmente começa com a cultura correta.

## <a name="provide-ui-to-choose-the-culture"></a>Fornecer interface do usuário para escolher a cultura

Para fornecer à interface do usuário a fim de permitir a seleção de uma cultura, é recomendável uma *abordagem baseada em redirecionamento* . O processo é semelhante ao que acontece em um aplicativo Web quando um usuário tenta acessar um recurso seguro @ no__t-0the usuário é redirecionado para uma página de entrada e, em seguida, Redirecionado para o recurso original. 

O aplicativo persiste a cultura selecionada do usuário por meio de um redirecionamento para um controlador. O controlador define a cultura selecionada do usuário em um cookie e redireciona o usuário de volta para o URI original.

Estabeleça um ponto de extremidade HTTP no servidor para definir a cultura selecionada do usuário em um cookie e execute o redirecionamento de volta para o URI original:

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
> Use o resultado da ação `LocalRedirect` para evitar ataques de redirecionamento abertos. Para obter mais informações, consulte <xref:security/preventing-open-redirects>.

O componente a seguir mostra um exemplo de como executar o redirecionamento inicial quando o usuário seleciona uma cultura:

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

### <a name="use-net-localization-scenarios-in-blazor-apps"></a>Usar cenários de localização do .NET em aplicativos mais Incrivelmenteos

Nos aplicativos mais poseriais, os seguintes cenários de localização e globalização do .NET estão disponíveis:

* . Sistema de recursos da rede
* Formatação de número e data específicos da cultura

A funcionalidade `@bind` de mais alto desempenho executa a globalização com base na cultura atual do usuário. Para obter mais informações, consulte a seção [ligação de dados](#data-binding) .

No momento, há suporte para um conjunto limitado de cenários de localização de ASP.NET Core:

* `IStringLocalizer<>` tem *suporte* em aplicativos mais incrivelmenteos.
* `IHtmlLocalizer<>`, `IViewLocalizer<>` e a localização de anotações de dados são ASP.NET Core cenários MVC e **não têm suporte** em aplicativos mais incrivelmenteos.

Para obter mais informações, consulte <xref:fundamentals/localization>.

## <a name="scalable-vector-graphics-svg-images"></a>Imagens SVG (gráficos vetoriais escaláveis)

Como o mais alto é renderizado em HTML, as imagens com suporte para navegadores, incluindo imagens SVG (gráficos vetoriais escalonáveis) ( *. svg*), têm suporte por meio da marca `<img>`:

```html
<img alt="Example image" src="some-image.svg" />
```

Da mesma forma, as imagens SVG têm suporte nas regras de CSS de um arquivo de folha de estilos ( *. css*):

```css
.my-element {
    background-image: url("some-image.svg");
}
```

No entanto, a marcação SVG embutida não tem suporte em todos os cenários. Se você posicionar uma marca `<svg>` diretamente em um arquivo de componente ( *. Razor*), a renderização de imagem básica terá suporte, mas muitos cenários avançados ainda não têm suporte. Por exemplo, as marcas `<use>` não são respeitadas atualmente e `@bind` não podem ser usadas com algumas marcas SVG. Esperamos abordar essas limitações em uma versão futura.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:security/blazor/server> &ndash; inclui orientações sobre a criação de aplicativos de servidor mais elaborados que devem combater o esgotamento de recursos.
