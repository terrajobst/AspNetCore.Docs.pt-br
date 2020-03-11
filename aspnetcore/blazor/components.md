---
title: Criar e usar ASP.NET Core componentes do Razor
author: guardrex
description: Saiba como criar e usar componentes do Razor, incluindo como associar dados, manipular eventos e gerenciar ciclos de vida do componente.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/25/2020
no-loc:
- Blazor
- SignalR
uid: blazor/components
ms.openlocfilehash: e444ebfef5143a6c33ed2d122933903ad3a4f4a7
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78660695"
---
# <a name="create-and-use-aspnet-core-razor-components"></a>Criar e usar ASP.NET Core componentes do Razor

De [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)

[Exibir ou baixar código de exemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([como baixar](xref:index#how-to-download-a-sample))

Blazor aplicativos são criados usando *componentes*. Um componente é uma parte independente da interface do usuário (IU), como uma página, uma caixa de diálogo ou um formulário. Um componente inclui marcação HTML e a lógica de processamento necessária para injetar dados ou responder a eventos da interface do usuário. Os componentes são flexíveis e leves. Eles podem ser aninhados, reutilizados e compartilhados entre projetos.

## <a name="component-classes"></a>Classes de componente

Os componentes são implementados em arquivos de componente do [Razor](xref:mvc/views/razor) ( *. Razor*) C# usando uma combinação de marcação HTML e. Um componente no Blazor é formalmente referido como um *componente Razor*.

O nome de um componente deve começar com um caractere maiúsculo. Por exemplo, *MyCoolComponent. Razor* é válido e *MyCoolComponent. Razor* é inválido.

A interface do usuário para um componente é definida usando HTML. A lógica de renderização dinâmica (por exemplo, loops, condicionais, expressões) é adicionada usando uma sintaxe de C# inserida chamada [Razor](xref:mvc/views/razor). Quando um aplicativo é compilado, a marcação HTML e C# a lógica de renderização são convertidas em uma classe de componente. O nome da classe gerada corresponde ao nome do arquivo.

Os membros da classe de componente são definidos em um bloco `@code`. No bloco de `@code`, estado do componente (Propriedades, campos) é especificado com métodos para manipulação de eventos ou para definir outra lógica de componente. Mais de um bloco de `@code` é permitido.

Os membros do componente podem ser usados como parte da lógica de renderização do C# componente usando expressões que começam com `@`. Por exemplo, um C# campo é renderizado pela prefixação `@` ao nome do campo. O exemplo a seguir avalia e renderiza:

* `_headingFontStyle` ao valor da propriedade CSS para `font-style`.
* `_headingText` o conteúdo do elemento `<h1>`.

```razor
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

Depois que o componente é processado inicialmente, o componente regenera sua árvore de renderização em resposta a eventos. em seguida, Blazor compara a nova árvore de renderização com a anterior e aplica quaisquer modificações à Modelo de Objeto do Documento do navegador (DOM).

Os componentes são C# classes comuns e podem ser colocados em qualquer lugar dentro de um projeto. Os componentes que produzem páginas da Web geralmente residem na pasta *páginas* . Os componentes que não são de página são frequentemente colocados na pasta *compartilhada* ou em uma pasta personalizada adicionada ao projeto.

Normalmente, o namespace de um componente é derivado do namespace raiz do aplicativo e do local do componente (pasta) no aplicativo. Se o namespace raiz do aplicativo for `BlazorApp` e o componente `Counter` residir na pasta *páginas* :

* O namespace do componente de `Counter` é `BlazorApp.Pages`.
* O nome do tipo totalmente qualificado do componente é `BlazorApp.Pages.Counter`.

Para obter mais informações, consulte a seção [importar componentes](#import-components) .

Para usar uma pasta personalizada, adicione o namespace da pasta personalizada ao componente pai ou ao arquivo *_Imports. Razor* do aplicativo. Por exemplo, o namespace a seguir torna os componentes em uma pasta *componentes* disponíveis quando o namespace raiz do aplicativo é `BlazorApp`:

```razor
@using BlazorApp.Components
```

## <a name="static-assets"></a>Ativos estáticos

Blazor segue a Convenção de ASP.NET Core aplicativos que estão colocando ativos estáticos na [pasta raiz da Web do projeto (wwwroot)](xref:fundamentals/index#web-root).

Use um caminho relativo de base (`/`) para se referir à raiz da Web para um ativo estático. No exemplo a seguir, *logo. png* está localizado fisicamente na pasta *{Project root}/wwwroot/images* :

```razor
<img alt="Company logo" src="/images/logo.png" />
```

Os componentes do Razor **não** dão suporte à notação de barra de til (`~/`).

Para obter informações sobre como definir o caminho base de um aplicativo, consulte <xref:host-and-deploy/blazor/index#app-base-path>.

## <a name="tag-helpers-arent-supported-in-components"></a>Não há suporte para auxiliares de marcas nos componentes

Os [auxiliares de marca](xref:mvc/views/tag-helpers/intro) não têm suporte em componentes do Razor (arquivos *. Razor* ). Para fornecer a funcionalidade do tipo auxiliar da marca no Blazor, crie um componente com a mesma funcionalidade que o auxiliar de marca e use o componente em vez disso.

## <a name="use-components"></a>Usar componentes

Os componentes podem incluir outros componentes, declarando-os usando a sintaxe do elemento HTML. A marcação para uso de um componente é semelhante a uma marca HTML, em que o nome da marca é o tipo de componente.

A associação de atributo diferencia maiúsculas de minúsculas. Por exemplo, `@bind` é válido e `@Bind` é inválido.

A marcação a seguir no *index. Razor* renderiza uma instância de `HeadingComponent`:

```razor
<HeadingComponent />
```

*Componentes/HeadingComponent. Razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

Se um componente contiver um elemento HTML com uma letra maiúscula ou minúscula que não corresponda a um nome de componente, um aviso será emitido indicando que o elemento tem um nome inesperado. Adicionar uma diretiva de `@using` para o namespace do componente disponibiliza o componente, o que resolve o aviso.

## <a name="routing"></a>Roteamento

O roteamento no Blazor é obtido fornecendo um modelo de rota para cada componente acessível no aplicativo.

Quando um arquivo Razor com uma diretiva `@page` é compilado, a classe gerada recebe um <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> especificando o modelo de rota. Em tempo de execução, o roteador procura classes de componentes com um `RouteAttribute` e renderiza qualquer componente que tenha um modelo de rota que corresponda à URL solicitada.

```razor
@page "/ParentComponent"

...
```

Para obter mais informações, consulte <xref:blazor/routing>.

## <a name="parameters"></a>Parâmetros

### <a name="route-parameters"></a>Parâmetros de rota

Os componentes podem receber parâmetros de rota do modelo de rota fornecido na diretiva `@page`. O roteador usa parâmetros de rota para preencher os parâmetros de componente correspondentes.

*Páginas/RouteParameter. Razor*:

[!code-razor[](components/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

Não há suporte para parâmetros opcionais, portanto, duas diretivas `@page` são aplicadas no exemplo anterior. O primeiro permite a navegação para o componente sem um parâmetro. A segunda diretiva `@page` recebe o parâmetro `{text}` Route e atribui o valor à propriedade `Text`.

A sintaxe de parâmetro *catch-all* (`*`/`**`), que captura o caminho entre vários limites de pasta, **não** tem suporte em componentes do Razor ( *. Razor*).

### <a name="component-parameters"></a>Parâmetros do componente

Os componentes podem ter *parâmetros de componente*, que são definidos usando propriedades públicas na classe de componente com o atributo `[Parameter]`. Use atributos para especificar argumentos para um componente na marcação.

*Componentes/ChildComponent. Razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

No exemplo a seguir do aplicativo de exemplo, o `ParentComponent` define o valor da propriedade `Title` do `ChildComponent`.

*Páginas/ParentComponent. Razor*:

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=5-6)]

## <a name="child-content"></a>Conteúdo filho

Os componentes podem definir o conteúdo de outro componente. O componente de atribuição fornece o conteúdo entre as marcas que especificam o componente de recebimento.

No exemplo a seguir, o `ChildComponent` tem uma propriedade `ChildContent` que representa um `RenderFragment`, que representa um segmento de interface do usuário a ser renderizado. O valor de `ChildContent` é posicionado na marcação do componente onde o conteúdo deve ser renderizado. O valor de `ChildContent` é recebido do componente pai e renderizado dentro do `panel-body`do painel de inicialização.

*Componentes/ChildComponent. Razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> A propriedade que recebe o conteúdo de `RenderFragment` deve ser nomeada `ChildContent` por convenção.

O `ParentComponent` no aplicativo de exemplo pode fornecer conteúdo para renderizar o `ChildComponent` colocando o conteúdo dentro das marcas de `<ChildComponent>`.

*Páginas/ParentComponent. Razor*:

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a>Atributo nivelamento e parâmetros arbitrários

Os componentes podem capturar e renderizar atributos adicionais além dos parâmetros declarados do componente. Atributos adicionais podem ser capturados em um dicionário e, em seguida, *splatted* em um elemento quando o componente é renderizado usando a diretiva [`@attributes`](xref:mvc/views/razor#attributes) Razor. Esse cenário é útil ao definir um componente que produz um elemento de marcação que dá suporte a uma variedade de personalizações. Por exemplo, pode ser entediante definir atributos separadamente para um `<input>` que ofereça suporte a muitos parâmetros.

No exemplo a seguir, o primeiro elemento `<input>` (`id="useIndividualParams"`) usa parâmetros de componente individuais, enquanto o segundo elemento `<input>` (`id="useAttributesDict"`) usa o atributo nivelamento:

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

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

A propriedade `CaptureUnmatchedValues` em `[Parameter]` permite que o parâmetro corresponda a todos os atributos que não correspondem a nenhum outro parâmetro. Um componente só pode definir um único parâmetro com `CaptureUnmatchedValues`. O tipo de propriedade usado com `CaptureUnmatchedValues` deve ser atribuível de `Dictionary<string, object>` com chaves de cadeia de caracteres. `IEnumerable<KeyValuePair<string, object>>` ou `IReadOnlyDictionary<string, object>` também são opções neste cenário.

A posição de `@attributes` em relação à posição dos atributos do elemento é importante. Quando `@attributes` são splatted no elemento, os atributos são processados da direita para a esquerda (último a primeiro). Considere o exemplo a seguir de um componente que consome um componente `Child`:

*ParentComponent. Razor*:

```razor
<ChildComponent extra="10" />
```

*ChildComponent. Razor*:

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

O atributo `extra` do componente de `Child` é definido à direita de `@attributes`. O `<div>` renderizado do componente `Parent` contém `extra="5"` quando passa pelo atributo adicional porque os atributos são processados da direita para a esquerda (último a primeiro):

```html
<div extra="5" />
```

No exemplo a seguir, a ordem de `extra` e `@attributes` é revertida na `<div>`do componente `Child`:

*ParentComponent. Razor*:

```razor
<ChildComponent extra="10" />
```

*ChildComponent. Razor*:

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

O `<div>` renderizado no componente `Parent` contém `extra="10"` quando passado pelo atributo adicional:

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a>Capturar referências a componentes

As referências de componente fornecem uma maneira de fazer referência a uma instância de componente para que você possa emitir comandos para essa instância, como `Show` ou `Reset`. Para capturar uma referência de componente:

* Adicione um atributo [`@ref`](xref:mvc/views/razor#ref) ao componente filho.
* Defina um campo com o mesmo tipo do componente filho.

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

Quando o componente é renderizado, o campo `_loginDialog` é populado com a instância de componente filho `MyLoginDialog`. Em seguida, você pode invocar os métodos .NET na instância do componente.

> [!IMPORTANT]
> A variável `_loginDialog` é populada apenas depois que o componente é renderizado e sua saída inclui o elemento `MyLoginDialog`. Até esse ponto, não há nada a fazer referência. Para manipular referências de componentes após a conclusão da renderização do componente, use os [métodos OnAfterRenderAsync ou OnAfterRender](xref:blazor/lifecycle#after-component-render).

Embora a captura de referências de componente use uma sintaxe semelhante à [captura de referências de elemento](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), ela não é um recurso de interoperabilidade do JavaScript. As referências de componente não são passadas para o código JavaScript&mdash;elas são usadas apenas no código .NET.

> [!NOTE]
> Não **use referências** de componente para converter o estado dos componentes filho. Em vez disso, use parâmetros declarativos normais para passar dados para componentes filho. O uso de parâmetros declarativos normais resulta em componentes filho que são reprocessados nos horários corretos automaticamente.

## <a name="invoke-component-methods-externally-to-update-state"></a>Invocar métodos de componente externamente para atualizar o estado

Blazor usa uma `SynchronizationContext` para impor um único thread lógico de execução. Os métodos de [ciclo de vida](xref:blazor/lifecycle) de um componente e quaisquer retornos de chamada de evento gerados por Blazor são executados nesse `SynchronizationContext`. No caso de um componente precisar ser atualizado com base em um evento externo, como um temporizador ou outras notificações, use o método `InvokeAsync`, que será expedido para o `SynchronizationContext`de Blazor.

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

Registre o `NotifierService` como um singletion:

* Em Blazor Webassembly, registre o serviço no `Program.Main`:

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* No Blazor Server, registre o serviço no `Startup.ConfigureServices`:

  ```csharp
  services.AddSingleton<NotifierService>();
  ```

Use o `NotifierService` para atualizar um componente:

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

No exemplo anterior, `NotifierService` invoca o método `OnNotify` do componente fora da `SynchronizationContext`do Blazor. `InvokeAsync` é usado para alternar para o contexto correto e enfileirar uma renderização.

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a>Use \@chave para controlar a preservação de elementos e componentes

Ao renderizar uma lista de elementos ou componentes e os elementos ou componentes subsequentemente mudam, o algoritmo de diferenciação do Blazordeve decidir qual dos elementos ou componentes anteriores podem ser mantidos e como os objetos de modelo devem ser mapeados para eles. Normalmente, esse processo é automático e pode ser ignorado, mas há casos em que você talvez queira controlar o processo.

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

O conteúdo da coleção de `People` pode ser alterado com entradas inseridas, excluídas ou reordenadas. Quando o componente é rerenderizado, o componente `<DetailsEditor>` pode ser alterado para receber diferentes valores de parâmetro `Details`. Isso pode causar um reprocessamento mais complexo do que o esperado. Em alguns casos, a rerenderização pode levar a diferenças de comportamento visíveis, como o foco de elemento perdido.

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

Quando a coleção de `People` é alterada, o algoritmo diff mantém a associação entre instâncias de `<DetailsEditor>` e `person` instâncias:

* Se uma `Person` for excluída da lista de `People`, somente a instância `<DetailsEditor>` correspondente será removida da interface do usuário. Outras instâncias permanecem inalteradas.
* Se uma `Person` for inserida em alguma posição na lista, uma nova instância de `<DetailsEditor>` será inserida na posição correspondente. Outras instâncias permanecem inalteradas.
* Se `Person` entradas forem reordenadas, as instâncias de `<DetailsEditor>` correspondentes serão preservadas e reordenadas na interface do usuário.

Em alguns cenários, o uso de `@key` minimiza a complexidade de rerenderização e evita possíveis problemas com partes com estado da alteração do DOM, como a posição do foco.

> [!IMPORTANT]
> As chaves são locais para cada elemento ou componente de contêiner. As chaves não são comparadas globalmente ao longo do documento.

### <a name="when-to-use-key"></a>Quando usar \@chave

Normalmente, faz sentido usar `@key` sempre que uma lista é renderizada (por exemplo, em um bloco de `@foreach`) e um valor adequado existe para definir o `@key`.

Você também pode usar `@key` para impedir Blazor de preservar uma subárvore de elemento ou componente quando um objeto for alterado:

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

Se `@currentPerson` for alterado, a diretiva de atributo `@key` forçará Blazor a descartar toda a `<div>` e seus descendentes e recriará a subárvore dentro da interface do usuário com novos elementos e componentes. Isso pode ser útil se você precisar garantir que nenhum estado da interface do usuário seja preservado quando `@currentPerson` for alterado.

### <a name="when-not-to-use-key"></a>Quando não usar \@chave

Há um custo de desempenho ao comparar com `@key`. O custo de desempenho não é grande, mas só especifique `@key` se o controle das regras de preservação de elemento ou componente beneficiar o aplicativo.

Mesmo que `@key` não seja usado, Blazor preserva as instâncias de elemento filho e de componente o máximo possível. A única vantagem de usar `@key` é o controle sobre *como* as instâncias de modelo são mapeadas para as instâncias de componente preservadas, em vez do algoritmo diff, selecionando o mapeamento.

### <a name="what-values-to-use-for-key"></a>Quais valores usar para \@chave

Geralmente, faz sentido fornecer um dos seguintes tipos de valor para `@key`:

* Instâncias de objeto de modelo (por exemplo, uma instância de `Person` como no exemplo anterior). Isso garante a preservação com base na igualdade de referência de objeto.
* Identificadores exclusivos (por exemplo, valores de chave primária do tipo `int`, `string`ou `Guid`).

Verifique se os valores usados para `@key` não estão em conflito. Se valores conflitantes forem detectados dentro do mesmo elemento pai, Blazor lançará uma exceção porque ele não pode mapear de forma determinística elementos ou componentes antigos para novos elementos ou componentes. Use apenas valores distintos, como instâncias de objeto ou valores de chave primária.

## <a name="partial-class-support"></a>Suporte de classe parcial

Os componentes do Razor são gerados como classes parciais. Os componentes do Razor são criados usando uma das seguintes abordagens:

* C#o código é definido em um bloco de [`@code`](xref:mvc/views/razor#code) com marcação HTML e código do Razor em um único arquivo. Blazor modelos definem seus componentes do Razor usando essa abordagem.
* C#o código é colocado em um arquivo code-behind definido como uma classe parcial.

O exemplo a seguir mostra o componente de `Counter` padrão com um bloco de `@code` em um aplicativo gerado por meio de um modelo de Blazor. Marcação HTML, código do Razor e C# código estão no mesmo arquivo:

*Counter. Razor*:

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

O componente `Counter` também pode ser criado usando um arquivo code-behind com uma classe parcial:

*Counter. Razor*:

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @_currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

*Counter.Razor.cs*:

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

Adicione quaisquer namespaces necessários ao arquivo de classe parcial, conforme necessário. Os namespaces típicos usados pelos componentes do Razor incluem:

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

## <a name="specify-a-base-class"></a>Especificar uma classe base

A diretiva [`@inherits`](xref:mvc/views/razor#inherits) pode ser usada para especificar uma classe base para um componente. O exemplo a seguir mostra como um componente pode herdar uma classe base, `BlazorRocksBase`, para fornecer as propriedades e os métodos do componente. A classe base deve derivar de `ComponentBase`.

*Páginas/BlazorRocks. Razor*:

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

*BlazorRocksBase.cs*:

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

## <a name="specify-an-attribute"></a>Especificar um atributo

Os atributos podem ser especificados em componentes do Razor com a diretiva [`@attribute`](xref:mvc/views/razor#attribute) . O exemplo a seguir aplica o atributo `[Authorize]` à classe de componente:

```razor
@page "/"
@attribute [Authorize]
```

## <a name="import-components"></a>Importar componentes

O namespace de um componente criado com o Razor baseia-se em (em ordem de prioridade):

* [`@namespace`](xref:mvc/views/razor#namespace) designação na marcação de arquivo Razor ( *. Razor*) (`@namespace BlazorSample.MyNamespace`).
* O `RootNamespace` do projeto no arquivo de projeto (`<RootNamespace>BlazorSample</RootNamespace>`).
* O nome do projeto, obtido do nome do arquivo do projeto ( *. csproj*) e o caminho da raiz do projeto para o componente. Por exemplo, a estrutura resolve *{raiz do projeto}/pages/index.Razor* (*BlazorSample. csproj*) para o namespace `BlazorSample.Pages`. Os componentes C# seguem regras de associação de nome. Para o componente `Index` neste exemplo, os componentes no escopo são todos os componentes:
  * Na mesma pasta, *páginas*.
  * Os componentes na raiz do projeto que não especificam explicitamente um namespace diferente.

Os componentes definidos em um namespace diferente são trazidos para o escopo usando a diretiva [`@using`](xref:mvc/views/razor#using) do Razor.

Se outro componente, `NavMenu.razor`, existir na pasta *BlazorSample/Shared/* , o componente poderá ser usado em `Index.razor` com a seguinte instrução `@using`:

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

Os componentes também podem ser referenciados usando seus nomes totalmente qualificados, o que não requer a diretiva [`@using`](xref:mvc/views/razor#using) :

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> Não há suporte para a qualificação de `global::`.
>
> Não há suporte para a importação de componentes com instruções de `using` com alias (por exemplo, `@using Foo = Bar`).
>
> Não há suporte para nomes parcialmente qualificados. Por exemplo, não há suporte para a adição de `@using BlazorSample` e referência `NavMenu.razor` com `<Shared.NavMenu></Shared.NavMenu>`.

## <a name="conditional-html-element-attributes"></a>Atributos de elemento HTML condicional

Os atributos de elemento HTML são processados condicionalmente com base no valor do .NET. Se o valor for `false` ou `null`, o atributo não será renderizado. Se o valor é `true`, o atributo é processado minimizado.

No exemplo a seguir, `IsCompleted` determina se `checked` é renderizado na marcação do elemento:

```razor
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
> Alguns atributos HTML, como [pressionados pelo Aria](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), não funcionam corretamente quando o tipo .net é um `bool`. Nesses casos, use um tipo de `string` em vez de um `bool`.

## <a name="raw-html"></a>HTML bruto

Normalmente, as cadeias de caracteres são renderizadas usando nós de texto DOM, o que significa que qualquer marcação que ela possa conter será ignorada e tratada como texto literal. Para renderizar HTML bruto, empacote o conteúdo HTML em um valor `MarkupString`. O valor é analisado como HTML ou SVG e inserido no DOM.

> [!WARNING]
> O processamento de HTML bruto construído a partir de qualquer fonte não confiável é um **risco à segurança** e deve ser evitado!

O exemplo a seguir mostra como usar o tipo de `MarkupString` para adicionar um bloco de conteúdo HTML estático à saída renderizada de um componente:

```html
@((MarkupString)_myMarkup)

@code {
    private string _myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="cascading-values-and-parameters"></a>Valores e parâmetros em cascata

Em alguns cenários, é inconveniente fluir dados de um componente ancestral para um componente descendente usando [parâmetros de componente](#component-parameters), especialmente quando há várias camadas de componente. Valores e parâmetros em cascata resolvem esse problema fornecendo uma maneira conveniente para um componente ancestral fornecer um valor para todos os seus componentes descendentes. Valores e parâmetros em cascata também fornecem uma abordagem para que os componentes sejam coordenados.

### <a name="theme-example"></a>Exemplo de tema

No exemplo a seguir do aplicativo de exemplo, a classe `ThemeInfo` especifica as informações do tema para fluir para baixo na hierarquia do componente para que todos os botões de uma determinada parte do aplicativo compartilhem o mesmo estilo.

*UIThemeClasses/ThemeInfo. cs*:

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

Um componente ancestral pode fornecer um valor em cascata usando o componente de valor em cascata. O componente `CascadingValue` encapsula uma subárvore da hierarquia de componentes e fornece um único valor para todos os componentes dentro dessa subárvore.

Por exemplo, o aplicativo de exemplo especifica informações de tema (`ThemeInfo`) em um dos layouts do aplicativo como um parâmetro em cascata para todos os componentes que compõem o corpo do layout da propriedade `@Body`. `ButtonClass` é atribuído um valor de `btn-success` no componente de layout. Qualquer componente descendente pode consumir essa propriedade por meio do `ThemeInfo` objeto em cascata.

`CascadingValuesParametersLayout` componente:

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

Para fazer uso de valores em cascata, os componentes declaram parâmetros em cascata usando o atributo `[CascadingParameter]`. Os valores em cascata são associados a parâmetros em cascata por tipo.

No aplicativo de exemplo, o componente `CascadingValuesParametersTheme` associa o valor de `ThemeInfo` em cascata a um parâmetro em cascata. O parâmetro é usado para definir a classe CSS para um dos botões exibidos pelo componente.

`CascadingValuesParametersTheme` componente:

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

Para propagar vários valores do mesmo tipo dentro da mesma subárvore, forneça uma cadeia de caracteres `Name` exclusiva para cada componente `CascadingValue` e sua `CascadingParameter`correspondente. No exemplo a seguir, dois componentes `CascadingValue` em cascata diferentes instâncias de `MyCascadingType` por nome:

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

Em um componente descendente, os parâmetros em cascata recebem seus valores dos valores em cascata correspondentes no componente ancestral por nome:

```razor
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

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

O componente `CascadingValuesParametersTabSet` usa o componente `TabSet`, que contém vários componentes `Tab`:

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

Os componentes de `Tab` filhos não são passados explicitamente como parâmetros para o `TabSet`. Em vez disso, os componentes de `Tab` filhos fazem parte do conteúdo filho do `TabSet`. No entanto, o `TabSet` ainda precisa saber sobre cada componente `Tab` para que ele possa renderizar os cabeçalhos e a guia ativa. Para habilitar essa coordenação sem a necessidade de código adicional, o componente `TabSet` *pode fornecer a si mesmo como um valor em cascata* que é então coletado pelos componentes do `Tab` descendente.

`TabSet` componente:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

Os componentes `Tab` descendentes capturam o `TabSet` recipiente como um parâmetro em cascata, de modo que os componentes `Tab` se adicionam à `TabSet` e coordenam em qual guia está ativa.

`Tab` componente:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a>Modelos do Razor

Os fragmentos de renderização podem ser definidos usando a sintaxe de modelo Razor. Os modelos Razor são uma maneira de definir um trecho de interface do usuário e assumir o seguinte formato:

```razor
@<{HTML tag}>...</{HTML tag}>
```

O exemplo a seguir ilustra como especificar `RenderFragment` e valores de `RenderFragment<T>` e renderizar modelos diretamente em um componente. Os fragmentos de renderização também podem ser passados como argumentos para [componentes de modelo](xref:blazor/templated-components).

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

Saída renderizada do código anterior:

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="scalable-vector-graphics-svg-images"></a>Imagens SVG (gráficos vetoriais escaláveis)

Como Blazor renderiza imagens HTML, com suporte para navegador, incluindo imagens SVG (gráficos de vetor escalonáveis) ( *. svg*), há suporte por meio da marca `<img>`:

```html
<img alt="Example image" src="some-image.svg" />
```

Da mesma forma, as imagens SVG têm suporte nas regras de CSS de um arquivo de folha de estilos ( *. css*):

```css
.my-element {
    background-image: url("some-image.svg");
}
```

No entanto, a marcação SVG embutida não tem suporte em todos os cenários. Se você posicionar uma marca de `<svg>` diretamente em um arquivo de componente ( *. Razor*), a renderização de imagem básica terá suporte, mas muitos cenários avançados ainda não têm suporte. Por exemplo, `<use>` marcas não são respeitadas atualmente e `@bind` não podem ser usadas com algumas marcas SVG. Esperamos abordar essas limitações em uma versão futura.

## <a name="additional-resources"></a>Recursos adicionais

* <xref:security/blazor/server> &ndash; inclui diretrizes sobre a criação de aplicativos do Blazor Server que devem combater o esgotamento de recursos.
