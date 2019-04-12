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
# <a name="create-and-use-razor-components"></a>Criar e usar componentes do Razor

Por [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), e [Morné Zaayman](https://github.com/MorneZaayman)

[Exibir ou baixar um código de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-components/common/samples/) ([como baixar](xref:index#how-to-download-a-sample)). Veja o tópico [Introdução](xref:razor-components/get-started) para conhecer os pré-requisitos.

Aplicativos de componentes do Razor são criados usando *componentes*. Um componente é uma parte independente da interface de usuário (UI), como uma página, caixa de diálogo ou formulário. Um componente inclui a marcação HTML e a lógica de processamento necessário para injetar dados ou responder a eventos de interface do usuário. Componentes são simples e flexível. Eles podem ser aninhados, reutilizados e compartilhados entres projetos.

## <a name="component-classes"></a>Classes de componentes

Componentes são normalmente implementados em arquivos de componente do Razor (*.razor*) usando uma combinação de C# e a marcação HTML (*. cshtml* arquivos são usados em aplicativos Blazor).

Componentes que podem ser criados em aplicativos de componentes do Razor usando o *. cshtml* extensão de arquivo, desde que os arquivos são identificados como arquivos de componente do Razor usando o `_RazorComponentInclude` propriedade do MSBuild. Por exemplo, um aplicativo criado usando o modelo de componente do Razor Especifica que todos os *. cshtml* arquivos sob o *componentes* pasta deve ser tratada como arquivos de componentes do Razor:

```xml
<_RazorComponentInclude>Components\**\*.cshtml</_RazorComponentInclude>
```

A interface do usuário para um componente é definida usando HTML. A lógica de renderização dinâmica (por exemplo, loops, condicionais, expressões) é adicionada usando uma sintaxe de C# inserida chamada [Razor](xref:mvc/views/razor). Quando um aplicativo de componentes do Razor é compilado, a marcação HTML e C# lógica de renderização são convertidas em uma classe de componente. O nome da classe gerada corresponde ao nome do arquivo.

Os membros da classe de componente são definidos em uma `@functions` bloco (mais de um `@functions` bloco é permitido). No `@functions` bloco, o estado do componente (propriedades, campos) é especificado com métodos para manipulação de eventos ou para definir outra lógica do componente.

Membros de componente, em seguida, podem ser usados como parte do componente de renderização do lógica usando C# expressões que começam com `@`. Por exemplo, um C# é processado, prefixando `@` para o nome do campo. O exemplo a seguir avalia e renderiza:

* `_headingFontStyle` para o valor da propriedade CSS para `font-style`.
* `_headingText` para o conteúdo a `<h1>` elemento.

```cshtml
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@functions {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

Depois que o componente inicialmente é renderizado, o componente gera novamente a sua árvore de renderização em resposta a eventos. Componentes do Razor, em seguida, compara a nova árvore de renderização em relação a anterior e se aplica a todas as modificações do navegador modelo DOM (Document Object).

## <a name="integrate-components-into-razor-pages-and-mvc-apps"></a>Integrar componentes em aplicativos de páginas do Razor e MVC

Use componentes com aplicativos existentes de páginas do Razor e MVC. Não é necessário reescrever páginas existentes ou exibições a usar componentes do Razor. Quando a página ou exibição é renderizada, componentes são pré-processadas&dagger; ao mesmo tempo. 

> [!NOTE]
> &dagger;Pré-processamento do lado do servidor está habilitado para aplicativos de componentes do Razor, por padrão. Aplicativos do lado do cliente Blazor suportará pré-processamento na próxima versão de visualização 4. Para obter mais informações, consulte [atualizar modelos/middleware usar MapFallbackToPage/arquivo](https://github.com/aspnet/AspNetCore/issues/8852).

Para renderizar um componente de uma página ou exibição, use o `RenderComponentAsync<TComponent>` método auxiliar HTML:

```cshtml
<div id="Counter">
    @(await Html.RenderComponentAsync<Counter>(new { IncrementAmount = 10 }))
</div>
```

Componentes processadas a partir de páginas e modos de exibição ainda não estão interativas na versão Preview 3. Por exemplo, a seleção de um botão não aciona uma chamada de método. Uma visualização futura irá resolver essa limitação e adicionar suporte para componentes de processamento usando a sintaxe de elemento e atributo normal.

Embora as páginas e modos de exibição podem usar componentes, o inverso não é true. Componentes não é possível usar cenários de específico de página e de exibição, como exibições parciais e seções. Para usar uma lógica de exibição parcial em um componente, o fator a lógica de exibição parcial em um componente.

## <a name="using-components"></a>Usando componentes

Os componentes podem incluir outros componentes, declarando-os usando a sintaxe de elemento HTML. A marcação para uso de um componente é semelhante a uma marca HTML, em que o nome da marca é o tipo de componente.

A marcação a seguir renderiza um `HeadingComponent` (*HeadingComponent.cshtml*) instância:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/Index.cshtml?name=snippet_HeadingComponent)]

## <a name="component-parameters"></a>Parâmetros do componente

Componentes podem ter *parâmetros do componente*, que é definido usando *não públicos* as propriedades na classe de componente decoradas com `[Parameter]`. Use atributos para especificar argumentos para um componente na marcação.

No exemplo a seguir, o `ParentComponent` define o valor da `Title` propriedade do `ChildComponent`:

*ParentComponent.cshtml*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.cshtml?name=snippet_ParentComponent&highlight=5)]

*ChildComponent.cshtml*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ChildComponent.cshtml?highlight=7-8)]

## <a name="child-content"></a>Conteúdo filho

Componentes podem definir o conteúdo de outro componente. O componente de atribuição fornece o conteúdo entre as marcas que especificam o componente receptor. Por exemplo, uma `ParentComponent` pode fornecer conteúdo para a renderização por um componente filho, colocando o conteúdo dentro `<ChildComponent>` marcas.

*ParentComponent.cshtml*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.cshtml?name=snippet_ParentComponent&highlight=6-7)]

O componente filho tem um `ChildContent` propriedade que representa um `RenderFragment`. O valor de `ChildContent` é posicionado na marcação do componente filho em que o conteúdo deve ser renderizado. No exemplo a seguir, o valor de `ChildContent` é recebida do componente pai e renderizado dentro do painel Bootstrap `panel-body`.

*ChildComponent.cshtml*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ChildComponent.cshtml?highlight=3,10-11)]

> [!NOTE]
> O recebimento de propriedade de `RenderFragment` conteúdo deve ser nomeado `ChildContent` por convenção.

## <a name="data-binding"></a>Associação de dados

Vinculação de dados a componentes e elementos DOM é feita com o `bind` atributo. O exemplo a seguir associa o `ItalicsCheck` estado selecionado de propriedade para a caixa de seleção:

```cshtml
<input type="checkbox" class="form-check-input" id="italicsCheck" 
    bind="@_italicsCheck">
```

Quando a caixa de seleção é marcada e desmarcada, o valor da propriedade é atualizado para `true` e `false`, respectivamente.

A caixa de seleção é atualizada na interface do usuário somente quando o componente é renderizado, não em resposta à alteração do valor da propriedade. Uma vez que os componentes se renderizar sozinhos após a execução de código do manipulador de eventos, as atualizações de propriedade são normalmente refletidas na interface do usuário imediatamente.

Usando o `bind` com um `CurrentValue` propriedade (`<input bind="@CurrentValue">`) é essencialmente equivalente ao seguinte:

```cshtml
<input value="@CurrentValue" 
    onchange="@((UIChangeEventArgs __e) => CurrentValue = __e.Value)">
```

Quando o componente é renderizado, o `value` do elemento de entrada proveniente de `CurrentValue` propriedade. Quando o usuário digita na caixa de texto, o `onchange` evento é acionado e o `CurrentValue` estiver definida como o valor alterado. Na realidade, a geração de código é um pouco mais complexo porque `bind` manipula alguns casos em que as conversões de tipo são executadas. Em princípio, `bind` associa o valor atual de uma expressão com um `value` atributo e manipula as alterações usando o manipulador registrado.

Além `onchange`, a propriedade pode ser associada com o uso de outros eventos, como `oninput` por ser mais explícito sobre qual associar a:

```cshtml
<input type="text" bind-value-oninput="@CurrentValue">
```

Diferentemente `onchange`, `oninput` é acionado para cada caractere que é entrada na caixa de texto.

**Cadeias de caracteres de formato**

Associação de dados funciona com <xref:System.DateTime> cadeias de caracteres de formato. Outras expressões de formato, como moeda ou formatos de número, não estão disponíveis no momento.

```cshtml
<input bind="@StartDate" format-value="yyyy-MM-dd">

@functions {
    [Parameter]
    private DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

O `format-value` atributo especifica o formato de data para aplicar a `value` da `input` elemento. O formato também é usado para analisar o valor quando um `onchange` evento ocorre.

**Parâmetros do componente**

Associação também reconhece componente parâmetros, onde `bind-{property}` pode associar um valor de propriedade entre componentes.

O componente a seguir usa `ChildComponent` e associa o `ParentYear` parâmetro do pai para o `Year` parâmetro no componente de filho:

Componente pai:

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

Componente de filho:

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

Carregando o `ParentComponent` produz a seguinte marcação:

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

Se o valor da `ParentYear` propriedade for alterada, selecionando o botão na `ParentComponent`, o `Year` propriedade do `ChildComponent` é atualizado. O novo valor de `Year` é renderizado na interface do usuário quando o `ParentComponent` é renderizada:

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

O `Year` parâmetro é associável porque ele tem um complemento `YearChanged` que corresponda ao tipo de evento a `Year` parâmetro.

Por convenção, `<ChildComponent bind-Year="@ParentYear" />` é essencialmente equivalente ao escrito,

```cshtml
    <ChildComponent bind-Year-YearChanged="@ParentYear" />
```

Em geral, uma propriedade pode ser associada a um correspondente manipulador de eventos usando `bind-property-event` atributo.

## <a name="event-handling"></a>Manipulação de eventos

Componentes do Razor fornecem recursos de manipulação de eventos. Para um atributo do elemento HTML denominado `on<event>` (por exemplo, `onclick`, `onsubmit`) com um valor de tipo de delegado, o Razor componentes trata o valor do atributo como um manipulador de eventos. O nome do atributo sempre começa com `on`.

O código a seguir chama o `UpdateHeading` método quando o botão é selecionado na interface do usuário:

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

O código a seguir chama o `CheckboxChanged` método quando a caixa de seleção é alterada na interface do usuário:

```cshtml
<input type="checkbox" class="form-check-input" onchange="@CheckboxChanged">

@functions {
    private void CheckboxChanged()
    {
        ...
    }
}
```

Manipuladores de eventos também podem ser assíncrono e retornar um <xref:System.Threading.Tasks.Task>. Não é necessário chamar manualmente `StateHasChanged()`. As exceções são registradas quando eles ocorrerem.

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

Para alguns eventos, tipos de argumento de evento específicas de evento são permitidos. Se o acesso a um desses tipos de evento não é necessário, ele não é necessário na chamada do método.

A lista de argumentos de evento com suporte é:

* UIEventArgs
* UIChangeEventArgs
* UIKeyboardEventArgs
* UIMouseEventArgs

Expressões lambda também podem ser usadas:

```cshtml
<button onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

Muitas vezes é conveniente se fechar sobre valores adicionais, como ao iterar em um conjunto de elementos. O exemplo a seguir cria três botões, cada um deles chama `UpdateHeading` passando um argumento de evento (`UIMouseEventArgs`) e seu número de botão (`buttonNumber`) quando a opção selecionada na interface do usuário:

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
> Fazer **não** use a variável de loop (`i`) em um `for` loop diretamente em uma expressão lambda. Caso contrário, a mesma variável é usada por todas as expressões lambda causando `i`do valor para ser o mesmo em todos os lambdas. Sempre capture seu valor em uma variável local (`buttonNumber` no exemplo anterior) e, em seguida, usá-lo.

## <a name="capture-references-to-components"></a>Capturar as referências a componentes

Referências de componentes fornecem uma maneira de obter uma referência a uma instância do componente, de modo que você pode emitir comandos para essa instância, como `Show` ou `Reset`. Para capturar uma referência de componente, adicione um `ref` de atributos para o componente filho e, em seguida, definir um campo com o mesmo nome e o mesmo tipo como o componente de filho.

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

Quando o componente é renderizado, o `loginDialog` campo é preenchido com o `MyLoginDialog` instância do componente filho. Em seguida, você pode chamar métodos do .NET na instância do componente.

> [!IMPORTANT]
> O `loginDialog` variável é populada somente depois que o componente é renderizado e sua saída inclui o `MyLoginDialog` elemento. Até esse ponto, não há nada para fazer referência. Para manipular as referências a componentes depois que o componente tiver terminado a renderização, use o `OnAfterRenderAsync` ou `OnAfterRender` métodos.

Enquanto referências de componente de captura usa uma sintaxe semelhante à [referências de elemento de captura](xref:razor-components/javascript-interop#capture-references-to-elements), não é um [JavaScript interoperabilidade](xref:razor-components/javascript-interop) recurso. Referências de componentes não serão passadas para o código JavaScript; eles são usados apenas em código .NET.

> [!NOTE]
> Fazer **não** usar referências de componentes para modificar o estado dos componentes de filho. Em vez disso, use parâmetros declarativos normais para passar dados para componentes filho. Isso faz com que os componentes de filho rerender automaticamente no horário correto.

## <a name="lifecycle-methods"></a>Métodos de ciclo de vida

`OnInitAsync` e `OnInit` execute código para inicializar o componente. Para executar uma operação assíncrona, use `OnInitAsync` e o `await` palavra-chave sobre a operação:

```csharp
protected override async Task OnInitAsync()
{
    await ...
}
```

Para uma operação síncrona, use `OnInit`:

```csharp
protected override void OnInit()
{
    ...
}
```

`OnParametersSetAsync` e `OnParametersSet` são chamados quando um componente recebeu parâmetros de seu pai e os valores são atribuídos às propriedades. Esses métodos são executados após a inicialização do componente e, em seguida, cada vez que o componente é renderizado:

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

`OnAfterRenderAsync` e `OnAfterRender` são chamados depois que um componente tiver terminado a renderização. Referências de elemento e de componente são preenchidas no momento. Use esse estágio para executar etapas de inicialização adicionais usando o conteúdo renderizado, como ativar bibliotecas JavaScript de terceiros que operam em elementos DOM processados.

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

`SetParameters` pode ser substituído para executar código antes que os parâmetros são definidos:

```csharp
public override void SetParameters(ParameterCollection parameters)
{
    ...

    base.SetParameters(parameters);
}
```

Se `base.SetParameters` não é invocado, o código personalizado pode interpretar o valor de parâmetros de entrada em qualquer forma alguma é necessária. Por exemplo, os parâmetros de entrada não precisam ser atribuídos às propriedades na classe.

`ShouldRender` pode ser substituído para suprimir a atualização da interface do usuário. Se a implementação retorna `true`, a interface do usuário é atualizada. Mesmo se `ShouldRender` é substituído, o componente sempre inicialmente é renderizado.

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

## <a name="component-disposal-with-idisposable"></a>Descarte de componente com IDisposable

Se um componente implementa <xref:System.IDisposable>, o [método Dispose](/dotnet/standard/garbage-collection/implementing-dispose) é chamado quando o componente é removido da interface do usuário. O componente a seguir usa `@implements IDisposable` e o `Dispose` método:

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

## <a name="routing"></a>Roteamento

Roteamento em componentes do Razor é obtido, fornecendo um modelo de rota para cada componente acessível no aplicativo.

Quando um *. cshtml* do arquivo com um `@page` diretiva é compilada, a classe gerada recebe um <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> especificando o modelo de rota. Em tempo de execução, o roteador procura por classes de componente com um `RouteAttribute` e renderiza a qualquer componente tem um modelo de rota que corresponde à URL solicitada.

Vários modelos de rota podem ser aplicados a um componente. O componente a seguir responde a solicitações para `/BlazorRoute` e `/DifferentBlazorRoute`:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.cshtml?name=snippet_BlazorRoute)]

## <a name="route-parameters"></a>Parâmetros de rota

Componentes podem receber parâmetros de rota do modelo de rota fornecido no `@page` diretiva. O roteador usa parâmetros de rota para preencher os parâmetros correspondentes do componente.

*RouteParameter.cshtml*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.cshtml?name=snippet_RouteParameter)]

Não há suporte para parâmetros opcionais, portanto, dois `@page` diretivas são aplicadas no exemplo acima. A primeira permite a navegação para o componente sem um parâmetro. A segunda `@page` diretiva leva a `{text}` parâmetro de rota e atribui o valor para o `Text` propriedade.

## <a name="base-class-inheritance-for-a-code-behind-experience"></a>Herança de classe base para uma experiência de "" code-behind

Arquivos de componente (*. cshtml*) misturar uma marcação HTML e C# processamento de código no mesmo arquivo. O `@inherits` diretiva pode ser usada para fornecer uma experiência de "" code-behind que separa a marcação de componente de código de processamento de aplicativos de componentes do Razor.

O [aplicativo de exemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-components/common/samples/) mostra como um componente pode herdar uma classe base, `BlazorRocksBase`, para fornecer métodos e propriedades do componente.

*BlazorRocks.cshtml*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRocks.cshtml?name=snippet_BlazorRocks)]

*BlazorRocksBase.cs*:

[!code-csharp[](common/samples/3.x/BlazorSample/Pages/BlazorRocksBase.cs)]

A classe base deve derivar de `ComponentBase`.

## <a name="razor-support"></a>Suporte para Razor

**Diretivas do Razor**

Diretivas do Razor são mostradas na tabela a seguir.

| Diretiva | Descrição |
| --------- | ----------- |
| [\@Funções](xref:mvc/views/razor#section-5) | Adiciona um C# bloco de código para um componente. |
| `@implements` | Implementa uma interface para a classe de componente gerado. |
| [\@Herda](xref:mvc/views/razor#section-3) | Fornece controle total sobre a classe que herda do componente. |
| [\@injetar](xref:mvc/views/razor#section-4) | Permite a injeção de serviço a [contêiner de serviço](xref:fundamentals/dependency-injection). Para obter mais informações, consulte [Injeção de dependência em exibições](xref:mvc/views/dependency-injection). |
| `@layout` | Especifica um componente de layout. Componentes de layout são usados para evitar inconsistência e a eliminação de duplicação de código. |
| [\@Página](xref:razor-pages/index#razor-pages) | Especifica que o componente deve tratar as solicitações diretamente. O `@page` diretiva pode ser especificada com uma rota e os parâmetros opcionais. Ao contrário de páginas do Razor, o `@page` diretiva não precisa ser a primeira diretiva na parte superior do arquivo. Para obter mais informações, consulte [Roteamento](xref:razor-components/routing). |
| [\@Usando o](xref:mvc/views/razor#using) | Adiciona o C# `using` diretriz para a classe de componente gerado. |
| [\@addTagHelper](xref:mvc/views/razor#tag-helpers) | Use `@addTagHelper` para usar um componente em um assembly diferente que o assembly do aplicativo. |

**Atributos condicionais**

Atributos condicionalmente são renderizados com base no valor do .NET. Se o valor for `false` ou `null`, o atributo não é renderizado. Se o valor for `true`, o atributo é renderizado minimizada.

No exemplo a seguir `IsCompleted` determina se `checked` é renderizado na marcação do controle:

```cshtml
<input type="checkbox" checked="@IsCompleted">

@functions {
    [Parameter]
    private bool IsCompleted { get; set; }
}
```

Se `IsCompleted` é `true`, a caixa de seleção é renderizada como:

```html
<input type="checkbox" checked>
```

Se `IsCompleted` é `false`, a caixa de seleção é renderizada como:

```html
<input type="checkbox">
```

**Informações adicionais sobre o Razor**

Para obter mais informações sobre o Razor, consulte o [referência de sintaxe do Razor](xref:mvc/views/razor).

## <a name="raw-html"></a>HTML bruto

Cadeias de caracteres normalmente são renderizadas usando nós de texto DOM, que significa que qualquer marcação, que elas podem conter é ignorada e tratada como texto literal. Para renderizar HTML bruto, encapsular o conteúdo HTML em um `MarkupString` valor. O valor é analisado como HTML ou SVG e inserido no DOM.

> [!WARNING]
> Renderização HTML bruto construído a partir de qualquer não confiáveis de origem é um **riscos de segurança** e deve ser evitado!

O exemplo a seguir mostra o uso de `MarkupString` tipo para adicionar um bloco de conteúdo do HTML estático para a saída renderizada de um componente:

```html
@((MarkupString)myMarkup)

@functions {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="templated-components"></a>Componentes com modelo

Componentes de modelo são que aceite um ou mais modelos de interface do usuário como parâmetros, que podem ser usados como parte da lógica de renderização do componente. Componentes com modelo permitem que você criar componentes de nível superior que são mais reutilizáveis que os componentes regulares. Alguns exemplos incluem:

* Um componente de tabela que permite que um usuário especificar modelos de cabeçalho, linhas e rodapé da tabela.
* Um componente de lista que permite que um usuário especificar um modelo para itens de renderização em uma lista.

### <a name="template-parameters"></a>Parâmetros de modelo

Um componente de modelo é definido pela especificação de um ou mais parâmetros de componente do tipo `RenderFragment` ou `RenderFragment<T>`. Um fragmento de renderização representa um segmento de interface do usuário que é processado pelo componente. Opcionalmente, um fragmento de renderização utiliza um parâmetro que pode ser especificado quando o fragmento de renderização é invocado.

*Components/TableTemplate.cshtml*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TableTemplate.cshtml)]

Ao usar um componente de modelo, os parâmetros de modelo podem ser especificados usando os elementos filho que correspondem aos nomes dos parâmetros (`TableHeader` e `RowTemplate` no exemplo a seguir):

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

### <a name="template-context-parameters"></a>Parâmetros de contexto do modelo

Os argumentos de tipo do componente `RenderFragment<T>` passado como elementos têm um parâmetro implícito chamado `context` (por exemplo, de exemplo de código anterior, `@context.PetId`), mas você pode alterar o nome de parâmetro usando o `Context` atributo no filho elemento. No exemplo a seguir, o `RowTemplate` prvku `Context` atributo especifica a `pet` parâmetro:

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

Como alternativa, você pode especificar o `Context` atributo no elemento de componente. Especificado `Context` atributo se aplica a todos os parâmetros de modelo especificado. Isso pode ser útil quando você deseja especificar o nome do parâmetro de conteúdo para conteúdo filho implícita (sem qualquer disposição do elemento filho). No exemplo a seguir, o `Context` atributo aparece no `TableTemplate` elemento e se aplica a todos os parâmetros de modelo:

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

### <a name="generic-typed-components"></a>Componentes de tipo genérico

Componentes com modelo geralmente genericamente são tipados. Por exemplo, um componente de modelo de exibição de lista genérico pode ser usado para renderizar `IEnumerable<T>` valores. Para definir um componente genérico, use o `@typeparam` diretiva para especificar parâmetros de tipo.

*Components/ListViewTemplate.cshtml*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ListViewTemplate.cshtml)]

Ao usar componentes de tipo genérico, o parâmetro de tipo é inferido se possível:

```cshtml
<ListViewTemplate Items="@pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

Caso contrário, o parâmetro de tipo deve ser explicitamente especificado usando um atributo que corresponde ao nome do parâmetro de tipo. No exemplo a seguir, `TItem="Pet"` Especifica o tipo:

```cshtml
<ListViewTemplate Items="@pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

## <a name="cascading-values-and-parameters"></a>Parâmetros e valores em cascata

Em alguns cenários, é inconveniente para dados de fluxo de um componente de ancestral a usar um componente descendente [parâmetros do componente](#component-parameters), especialmente quando há várias camadas de componente. Parâmetros e valores em cascata resolvem esse problema, fornecendo uma maneira conveniente de um componente do ancestral fornecer um valor para todos os seus componentes descendentes. Parâmetros e valores em cascata também fornecem uma abordagem para componentes coordenar.

### <a name="theme-example"></a>Exemplo de tema

A seguir *tema* exemplo de aplicativo de exemplo, o `ThemeInfo` classe especifica as informações de tema para toda a hierarquia do componente de fluxo para que todos os botões dentro de uma determinada parte do aplicativo compartilham o mesmo estilo.

*UIThemeClasses/ThemeInfo.cs*:

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

Um componente de ancestral pode fornecer um valor em cascata, usando o componente de valor em cascata. O componente de valor em cascata encapsula uma subárvore da hierarquia do componente e fornece um valor único para todos os componentes dentro nessa subárvore.

Por exemplo, o aplicativo de exemplo especifica informações de tema (`ThemeInfo`) em um dos layouts do aplicativo como um parâmetro em cascata para todos os componentes que compõem o corpo do layout do `@Body` propriedade. `ButtonClass` é atribuído um valor de `btn-success` no componente de layout. Qualquer componente descendente pode consumir essa propriedade através de `ThemeInfo` em cascata do objeto.

*Shared/CascadingValuesParametersLayout.cshtml*:

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

Para fazer uso de valores em cascata, usando os parâmetros em cascata os componentes declaram o `[CascadingParameter]` de atributo ou com base em um valor de nome de cadeia de caracteres:

```cshtml
<CascadingValue Value=@PermInfo Name="UserPermissions">...</CascadingValue>

[CascadingParameter(Name = "UserPermissions")]
private PermInfo Permissions { get; set; }
```

Associação com um valor de nome de cadeia de caracteres será relevante se você tiver vários valores em cascata do mesmo tipo e precisar para diferenciá-los dentro da mesma subárvore.

Valores em cascata são associados aos parâmetros em cascata por tipo.

No aplicativo de exemplo, o componente de tema de parâmetros de valores em cascata vincula o `ThemeInfo` valor em cascata a um parâmetro em cascata. O parâmetro é usado para definir a classe CSS para um dos botões exibidos pelo componente.

*Pages/CascadingValuesParametersTheme.cshtml*:

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

### <a name="tabset-example"></a>Exemplo de conjunto de guias

Parâmetros em cascata também habilitar componentes colaborar em toda a hierarquia do componente. Por exemplo, considere o seguinte *conjunto de guias* exemplo no aplicativo de exemplo.

O aplicativo de exemplo possui um `ITab` interface que implementam de guias:

[!code-cs[](common/samples/3.x/BlazorSample/UIInterfaces/ITab.cs)]

O componente de conjunto em cascata de parâmetros de valores abas usa o componente de conjunto de guias, que contém vários componentes do guia:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/CascadingValuesParametersTabSet.cshtml?name=snippet_TabSet)]

Os componentes de guia filho não são explicitamente passados como parâmetros para o conjunto de guias. Em vez disso, os componentes de guia filho fazem parte do conteúdo filho do conjunto de guias. No entanto, o conjunto de guias ainda precisa saber sobre cada componente do guia para que ele possa processar os cabeçalhos e a guia ativa. Para habilitar essa coordenação sem a necessidade de código adicional, o componente de conjunto de guias *pode fornecer em si como um valor em cascata* que é capturado pelos componentes do guia descendentes.

*Components/TabSet.cshtml*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TabSet.cshtml)]

A captura de componentes de guia descendente guia contendo definido como um parâmetro em cascata, para que os componentes de guia serem adicionados ao conjunto de guias e coordenadas na guia que está ativo.

*Components/Tab.cshtml*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/Tab.cshtml)]

## <a name="razor-templates"></a>Modelos do Razor

Renderizar fragmentos podem ser definidos usando a sintaxe de modelo do Razor. Modelos do Razor são uma maneira de definir um trecho de código da interface do usuário e supor o seguinte formato:

```cshtml
@<tag>...</tag>
```

O exemplo a seguir ilustra como especificar `RenderFragment` e `RenderFragment<T>` valores.

*RazorTemplates.cshtml*:

```cshtml
@{
    RenderFragment template = @<p>The time is @DateTime.Now.</p>;
    RenderFragment<Pet> petTemplate = (pet) => @<p>Your pet's name is @pet.Name.</p>;
}
```

Renderizar fragmentos definidos usando o Razor modelos podem ser passados como argumentos para componentes com modelo ou renderizados diretamente. Por exemplo, os modelos anteriores diretamente são processados com a seguinte marcação Razor:

```cshtml
@template

@petTemplate(new Pet { Name = "Rex" })
```

Saída renderizada:

```
The time is 10/04/2018 01:26:52.

Your pet's name is Rex.
```

## <a name="manual-rendertreebuilder-logic"></a>Lógica de RenderTreeBuilder manual

`Microsoft.AspNetCore.Components.RenderTree` fornece métodos para manipular componentes e elementos, incluindo a criação de componentes manualmente no C# código.

> [!NOTE]
> Uso de `RenderTreeBuilder` criar componentes é um cenário avançado. Um componente malformado (por exemplo, uma marca de marcação não fechada) pode resultar em comportamento indefinido.

Considere o seguinte componente animal de estimação detalhes (*PetDetails.razor* nos componentes do Razor; *PetDetails.cshtml* em Blazor), que podem ser criado manualmente em outro componente:

```cshtml
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote<p>

@functions
{
    [Parameter]
    string PetDetailsQuote { get; set; }
}
```

No exemplo a seguir, o loop no `CreateComponent` método gera três componentes de detalhes de animal de estimação. Ao chamar `RenderTreeBuilder` métodos para criar os componentes (`OpenComponent` e `AddAttribute`), números de sequência são números de linha de código de origem. Os componentes de Razor depende de algoritmo de diferença nos números de sequência correspondente à linhas distintas do código, não são diferentes de chamar invocações. Durante a criação de um componente com `RenderTreeBuilder` métodos, codificar os argumentos para números de sequência. **Usar um cálculo ou um contador para gerar o número de sequência pode levar a um desempenho ruim.**

Incorporado componente (*BuiltContent.razor* nos componentes do Razor; *BuiltContent.cshtml* em Blazor):

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
