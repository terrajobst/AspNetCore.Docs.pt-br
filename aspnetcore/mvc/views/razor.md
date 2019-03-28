---
title: Referência da sintaxe Razor para ASP.NET Core
author: rick-anderson
description: Saiba mais sobre a sintaxe de marcação Razor para inserir código baseado em servidor em páginas da Web.
ms.author: riande
ms.date: 10/26/2018
uid: mvc/views/razor
ms.openlocfilehash: 53d4dc608fbfd45bcc015a3af83f5d87f86c7f15
ms.sourcegitcommit: a1c43150ed46aa01572399e8aede50d4668745ca
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58327359"
---
# <a name="razor-syntax-reference-for-aspnet-core"></a>Referência da sintaxe Razor para ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Luke Latham](https://github.com/guardrex), [Taylor Mullen](https://twitter.com/ntaylormullen) e [Dan Vicarel](https://github.com/Rabadash8820)

Razor é uma sintaxe de marcação para inserir código baseado em servidor em páginas da Web. A sintaxe Razor é composta pela marcação Razor, por C# e por HTML. Arquivos que contêm Razor geralmente têm a extensão de arquivo *.cshtml*.

## <a name="rendering-html"></a>Renderizando HTML

A linguagem padrão do Razor padrão é o HTML. Renderizar HTML da marcação Razor não é diferente de renderizar HTML de um arquivo HTML. A marcação HTML em arquivos Razor *.cshtml* é renderizada pelo servidor inalterado.

## <a name="razor-syntax"></a>Sintaxe Razor

O Razor dá suporte a C# e usa o símbolo `@` para fazer a transição de HTML para C#. O Razor avalia expressões em C# e as renderiza na saída HTML.

Quando um símbolo `@` é seguido por uma [palavra-chave reservada do Razor](#razor-reserved-keywords), ele faz a transição para marcação específica do Razor. Caso contrário, ele faz a transição para C# simples.

Para fazer o escape de um símbolo `@` na marcação Razor, use um segundo símbolo `@`:

```cshtml
<p>@@Username</p>
```

O código é renderizado em HTML com um único símbolo `@`:

```html
<p>@Username</p>
```

Conteúdo e atributos HTML que contêm endereços de email não tratam o símbolo `@` como um caractere de transição. Os endereços de email no exemplo a seguir não são alterados pela análise do Razor:

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-razor-expressions"></a>Expressões Razor implícitas

Expressões Razor implícitas são iniciadas por `@` seguido do código C#:

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

Com exceção da palavra-chave C# `await`, expressões implícitas não devem conter espaços. Se a instrução C# tiver uma terminação clara, espaços podem ser misturados:

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

Expressões implícitas **não podem** conter elementos genéricos de C#, pois caracteres dentro de colchetes (`<>`) são interpretados como uma marca HTML. O código a seguir é **inválido**:

```cshtml
<p>@GenericMethod<int>()</p>
```

O código anterior gera um erro de compilador semelhante a um dos seguintes:

* O elemento "int" não foi fechado. Todos os elementos devem ter fechamento automático ou ter uma marca de fim correspondente.
* Não é possível converter o grupo de métodos "GenericMethod" em um "object" de tipo não delegado. Você pretendia invocar o método?

Chamadas de método genérico devem ser encapsuladas em uma [expressão Razor explícita](#explicit-razor-expressions) ou em um [bloco de código Razor](#razor-code-blocks).

## <a name="explicit-razor-expressions"></a>Expressões Razor explícitas

Expressões Razor explícitas consistem de um símbolo `@` com parênteses equilibradas. Para renderizar a hora da última semana, a seguinte marcação Razor é usada:

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

Qualquer conteúdo dentro dos parênteses `@()` é avaliado e renderizado para a saída.

Expressões implícitas, descritas na seção anterior, geralmente não podem conter espaços. No código a seguir, uma semana não é subtraída da hora atual:

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

O código renderiza o HTML a seguir:

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

Expressões explícitas podem ser usadas para concatenar texto com um resultado de expressão:

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

Sem a expressão explícita, `<p>Age@joe.Age</p>` é tratado como um endereço de email e `<p>Age@joe.Age</p>` é renderizado. Quando escrito como uma expressão explícita, `<p>Age33</p>` é renderizado.

Expressões explícitas podem ser usadas para renderizar a saída de métodos genéricos em arquivos *.cshtml*. A marcação a seguir mostra como corrigir o erro mostrado anteriormente causado pelos colchetes de um C# genérico. O código é escrito como uma expressão explícita:

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a>Codificação de expressão

Expressões em C# que são avaliadas como uma cadeia de caracteres estão codificadas em HTML. Expressões em C# que são avaliadas como `IHtmlContent` são renderizadas diretamente por meio `IHtmlContent.WriteTo`. Expressões em C# que não são avaliadas como `IHtmlContent` são convertidas em uma cadeia de caracteres por `ToString` e codificadas antes que sejam renderizadas.

```cshtml
@("<span>Hello World</span>")
```

O código renderiza o HTML a seguir:

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

O HTML é mostrado no navegador como:

```
<span>Hello World</span>
```

A saída `HtmlHelper.Raw` não é codificada, mas renderizada como marcação HTML.

> [!WARNING]
> Usar `HtmlHelper.Raw` em uma entrada do usuário que não está limpa é um risco de segurança. A entrada do usuário pode conter JavaScript mal-intencionado ou outras formas de exploração. Limpar a entrada do usuário é difícil. Evite usar `HtmlHelper.Raw` com a entrada do usuário.

```cshtml
@Html.Raw("<span>Hello World</span>")
```

O código renderiza o HTML a seguir:

```html
<span>Hello World</span>
```

## <a name="razor-code-blocks"></a>Blocos de código Razor

Blocos de código Razor são iniciados por `@` e delimitados por `{}`. Diferente das expressões, o código C# dentro de blocos de código não é renderizado. Blocos de código e expressões em uma exibição compartilham o mesmo escopo e são definidos em ordem:

```cshtml
@{
    var quote = "The future depends on what you do today. - Mahatma Gandhi";
}

<p>@quote</p>

@{
    quote = "Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.";
}

<p>@quote</p>
```

O código renderiza o HTML a seguir:

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

### <a name="implicit-transitions"></a>Transições implícitas

A linguagem padrão em um bloco de código é C#, mas a página Razor pode fazer a transição de volta para HTML:

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a>Transição delimitada explícita

Para definir uma subseção de um bloco de código que deve renderizar HTML, circunde os caracteres para renderização com as marca Razor **\<text>**:

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

Use essa abordagem para renderizar HTML que não está circundado por uma marca HTML. Sem uma marca HTML ou Razor, ocorrerá um erro de tempo de execução do Razor.

A marca **\<text>** é útil para controlar o espaço em branco ao renderizar conteúdo:

* Somente o conteúdo entre a marca **\<text>** é renderizado.
* Não aparece nenhum espaço em branco antes ou depois da marca **\<text>** na saída HTML.

### <a name="explicit-line-transition-with-"></a>Transição de linha explícita com @:

Para renderizar o restante de uma linha inteira como HTML dentro de um bloco de código, use a sintaxe `@:`:

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

Sem o `@:` no código, será gerado um erro de tempo de execução do Razor.

Aviso: Caracteres `@` extras em um arquivo Razor podem causar erros do compilador em instruções mais adiante no bloco. Esses erros do compilador podem ser difíceis de entender porque o erro real ocorre antes do erro relatado. Esse erro é comum após combinar várias expressões implícitas/explícitas em um bloco de código único.

## <a name="control-structures"></a>Estruturas de controle

Estruturas de controle são uma extensão dos blocos de código. Todos os aspectos dos blocos de código (transição para marcação, C# embutido) também se aplicam às seguintes estruturas:

### <a name="conditionals-if-else-if-else-and-switch"></a>Condicionais @if, else if, else e @switch

`@if` controla quando o código é executado:

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

`else` e `else if` não exigem o símbolo `@`:

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
else if (value >= 1337)
{
    <p>The value is large.</p>
}
else
{
    <p>The value is odd and small.</p>
}
```

A marcação a seguir mostra como usar uma instrução switch:

```cshtml
@switch (value)
{
    case 1:
        <p>The value is 1!</p>
        break;
    case 1337:
        <p>Your number is 1337!</p>
        break;
    default:
        <p>Your number wasn't 1 or 1337.</p>
        break;
}
```

### <a name="looping-for-foreach-while-and-do-while"></a>Loop de @for, @foreach, @while e @do while

O HTML no modelo pode ser renderizado com instruções de controle em loop. Para renderizar uma lista de pessoas:

```cshtml
@{
    var people = new Person[]
    {
          new Person("Weston", 33),
          new Person("Johnathon", 41),
          ...
    };
}
```

Há suporte para as seguintes instruções em loop:

`@for`

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@foreach`

```cshtml
@foreach (var person in people)
{
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@while`

```cshtml
@{ var i = 0; }
@while (i < people.Length)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
}
```

`@do while`

```cshtml
@{ var i = 0; }
@do
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
} while (i < people.Length);
```

### <a name="compound-using"></a>@using composto

Em C#, uma instrução `using` é usada para garantir que um objeto seja descartado. No Razor, o mesmo mecanismo é usado para criar Auxiliares HTML que têm conteúdo adicional. No código a seguir, os Auxiliares HTML renderizam uma marca de formulário com a instrução `@using`:

```cshtml
@using (Html.BeginForm())
{
    <div>
        email:
        <input type="email" id="Email" value="">
        <button>Register</button>
    </div>
}
```

Ações no nível de escopo podem ser executadas com [Auxiliares de marca](xref:mvc/views/tag-helpers/intro).

### <a name="try-catch-finally"></a>@try, catch, finally

O tratamento de exceções é semelhante ao de C#:

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### <a name="lock"></a>@lock

O Razor tem a capacidade de proteger seções críticas com instruções de bloqueio:

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a>Comentários

O Razor dá suporte a comentários em C# e HTML:

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

O código renderiza o HTML a seguir:

```html
<!-- HTML comment -->
```

Comentários em Razor são removidos pelo servidor antes que a página da Web seja renderizada. O Razor usa `@*  *@` para delimitar comentários. O código a seguir é comentado, de modo que o servidor não renderiza nenhuma marcação:

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a>Diretivas

Diretivas de Razor são representadas por expressões implícitas com palavras-chave reservadas após o símbolo `@`. Uma diretiva geralmente altera o modo como uma exibição é analisada ou habilita uma funcionalidade diferente.

Compreender como o Razor gera código para uma exibição torna mais fácil entender como as diretivas funcionam.

[!code-cshtml[](razor/sample/Views/Home/Contact8.cshtml)]

O código gera uma classe semelhante à seguinte:

```csharp
public class _Views_Something_cshtml : RazorPage<dynamic>
{
    public override async Task ExecuteAsync()
    {
        var output = "Getting old ain't for wimps! - Anonymous";

        WriteLiteral("/r/n<div>Quote of the Day: ");
        Write(output);
        WriteLiteral("</div>");
    }
}
```

Mais adiante neste artigo, a seção [Inspecionar a classe do Razor C# gerada para uma exibição](#inspect-the-razor-c-class-generated-for-a-view) explica como exibir essa classe gerada.

<a name="using"></a>

### <a name="using"></a>@using

A diretiva `@using` adiciona a diretiva `using` de C# à exibição gerada:

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

### <a name="model"></a>@model

A diretiva `@model` especifica o tipo do modelo passado para uma exibição:

```cshtml
@model TypeNameOfModel
```

Em um aplicativo do ASP.NET Core MCV criado com contas de usuário individuais, a exibição *Views/Account/Login.cshtml* contém a declaração de modelo a seguir:

```cshtml
@model LoginViewModel
```

A classe gerada herda de `RazorPage<dynamic>`:

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

O Razor expõe uma propriedade `Model` para acessar o modelo passado para a exibição:

```cshtml
<div>The Login Email: @Model.Email</div>
```

A diretiva `@model` especifica o tipo dessa propriedade. A diretiva especifica o `T` em `RazorPage<T>` da classe gerada da qual a exibição deriva. Se a diretiva `@model` não for especificada, a propriedade `Model` será do tipo `dynamic`. O valor do modelo é passado do controlador para a exibição. Para obter mais informações, confira [Modelos fortemente tipados e a &commat;palavra-chave do modelo](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).

### <a name="inherits"></a>@inherits

A diretiva `@inherits` fornece controle total da classe que a exibição herda:

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

O código a seguir é um tipo de página Razor personalizado:

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

O `CustomText` é exibido em uma exibição:

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

O código renderiza o HTML a seguir:

```html
<div>Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping a slop bucket on the street below.</div>
```

 `@model` e `@inherits` podem ser usados na mesma exibição. `@inherits` pode estar em um arquivo *_ViewImports.cshtml* que a exibição importa:

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

O código a seguir é um exemplo de exibição fortemente tipada:

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

Se "rick@contoso.com" for passado no modelo, a exibição gerará a seguinte marcação HTML:

```html
<div>The Login Email: rick@contoso.com</div>
<div>Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping a slop bucket on the street below.</div>
```

### <a name="inject"></a>@inject

A diretiva `@inject` permite que a página do Razor injete um serviço do [contêiner de serviço](xref:fundamentals/dependency-injection) em uma exibição. Para obter mais informações, consulte [Injeção de dependência em exibições](xref:mvc/views/dependency-injection).

### <a name="functions"></a>@functions

A diretiva `@functions` permite que uma página Razor adicione um bloco de código C# a uma exibição:

```cshtml
@functions { // C# Code }
```

Por exemplo:

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

O código gera a seguinte marcação HTML:

```html
<div>From method: Hello</div>
```

O código a seguir é a classe C# do Razor gerada:

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

### <a name="section"></a>@section

A diretiva `@section` é usada em conjunto com o [layout](xref:mvc/views/layout) para permitir que as exibições renderizem conteúdo em partes diferentes da página HTML. Para obter mais informações, consulte [Seções](xref:mvc/views/layout#layout-sections-label).

## <a name="templated-razor-delegates"></a>Representantes do Razor com modelo

Os modelos Razor permitem que você defina um snippet da interface do usuário com o seguinte formato:

```cshtml
@<tag>...</tag>
```

O exemplo a seguir ilustra como especificar um delegado do Razor com modelo como um <xref:System.Func%602>. O [tipo dinâmico](/dotnet/csharp/programming-guide/types/using-type-dynamic) é especificado para o parâmetro do método encapsulado pelo delegado. Um [tipo de objeto](/dotnet/csharp/language-reference/keywords/object) é especificado como o valor retornado do delegado. O modelo é usado com uma <xref:System.Collections.Generic.List%601> de `Pet` que tem uma propriedade `Name`.

```csharp
public class Pet
{
    public string Name { get; set; }
}
```

```cshtml
@{
    Func<dynamic, object> petTemplate = @<p>You have a pet named <strong>@item.Name</strong>.</p>;

    var pets = new List<Pet>
    {
        new Pet { Name = "Rin Tin Tin" },
        new Pet { Name = "Mr. Bigglesworth" },
        new Pet { Name = "K-9" }
    };
}
```

O modelo é renderizado com `pets` fornecido por uma instrução `foreach`:

```cshtml
@foreach (var pet in pets)
{
    @petTemplate(pet)
}
```

Saída renderizada:

```html
<p>You have a pet named <strong>Rin Tin Tin</strong>.</p>
<p>You have a pet named <strong>Mr. Bigglesworth</strong>.</p>
<p>You have a pet named <strong>K-9</strong>.</p>
```

Você também pode fornecer um modelo Razor embutido como um argumento para um método. No exemplo a seguir, o método `Repeat` recebe um modelo Razor. O método usa o modelo para produzir o conteúdo HTML com repetições de itens fornecidos em uma lista:

```cshtml
@using Microsoft.AspNetCore.Html

@functions {
    public static IHtmlContent Repeat(IEnumerable<dynamic> items, int times,
        Func<dynamic, IHtmlContent> template)
    {
        var html = new HtmlContentBuilder();

        foreach (var item in items)
        {
            for (var i = 0; i < times; i++)
            {
                html.AppendHtml(template(item));
            }
        }

        return html;
    }
}
```

Usando a lista de animais de estimação do exemplo anterior, o método `Repeat` é chamado com:

* <xref:System.Collections.Generic.List%601> de `Pet`.
* Número de vezes que deve ser repetido cada animal de estimação.
* Modelo embutido a ser usado para os itens da lista de uma lista não ordenada.

```cshtml
<ul>
    @Repeat(pets, 3, @<li>@item.Name</li>)
</ul>
```

Saída renderizada:

```html
<ul>
    <li>Rin Tin Tin</li>
    <li>Rin Tin Tin</li>
    <li>Rin Tin Tin</li>
    <li>Mr. Bigglesworth</li>
    <li>Mr. Bigglesworth</li>
    <li>Mr. Bigglesworth</li>
    <li>K-9</li>
    <li>K-9</li>
    <li>K-9</li>
</ul>
```

## <a name="tag-helpers"></a>Auxiliares de Marca

Há três diretivas que relacionadas aos [Auxiliares de marca](xref:mvc/views/tag-helpers/intro).

| Diretiva | Função |
| --------- | -------- |
| [&commat;addTagHelper](xref:mvc/views/tag-helpers/intro#add-helper-label) | Disponibiliza os Auxiliares de marca para uma exibição. |
| [&commat;removeTagHelper](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | Remove os Auxiliares de marca adicionados anteriormente de uma exibição. |
| [&commat;tagHelperPrefix](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | Especifica um prefixo de marca para habilitar o suporte do Auxiliar de marca e tornar explícito o uso do Auxiliar de marca. |

## <a name="razor-reserved-keywords"></a>Palavras-chave reservadas ao Razor

### <a name="razor-keywords"></a>Palavras-chave do Razor

* page (requer o ASP.NET Core 2.0 e posteriores)
* namespace
* funções
* herda
* modelo
* section
* helper (atualmente sem suporte do ASP.NET Core)

Palavras-chave do Razor têm o escape feito com `@(Razor Keyword)` (por exemplo, `@(functions)`).

### <a name="c-razor-keywords"></a>Palavras-chave do Razor em C#

* case
* do
* default
* for
* foreach
* if
* else
* bloqueio
* switch
* try
* catch
* finally
* using
* while

Palavras-chave do Razor em C# precisam ter o escape duplo com `@(@C# Razor Keyword)` (por exemplo, `@(@case)`). O primeiro `@` faz o escape do analisador Razor. O segundo `@` faz o escape do analisador C#.

### <a name="reserved-keywords-not-used-by-razor"></a>Palavras-chave reservadas não usadas pelo Razor

* classe

## <a name="inspect-the-razor-c-class-generated-for-a-view"></a>Inspecionar a classe do Razor C# gerada para uma exibição

::: moniker range=">= aspnetcore-2.1"

Com o SDK do .NET Core 2.1 ou posterior, o [SDK do Razor](xref:razor-pages/sdk) lida com a compilação de arquivos do Razor. Ao compilar um projeto, o SDK do Razor gera um diretório *obj/<configuração_de_build>/<moniker_da_estrutura_de_destino>/Razor* na raiz do projeto. A estrutura de diretórios dentro do diretório do *Razor* espelha a estrutura de diretórios do projeto.

Considere a seguinte estrutura de diretórios em um projeto do Razor Pages ASP.NET Core 2.1 direcionado ao .NET Core 2.1:

* **Areas/**
  * **Admin/**
    * **Pages/**
      * *Index.cshtml*
      * *Index.cshtml.cs*
* **Pages/**
  * **Shared/**
    * *_Layout.cshtml*
  * *_ViewImports.cshtml*
  * *_ViewStart.cshtml*
  * *Index.cshtml*
  * *Index.cshtml.cs*

A criação do projeto na configuração de *Depuração* produz o seguinte diretório *obj*:

* **obj/**
  * **Debug/**
    * **netcoreapp2.1/**
      * **Razor/**
        * **Areas/**
          * **Admin/**
            * **Pages/**
              * *Index.g.cshtml.cs*
        * **Pages/**
          * **Shared/**
            * *_Layout.g.cshtml.cs*
          * *_ViewImports.g.cshtml.cs*
          * *_ViewStart.g.cshtml.cs*
          * *Index.g.cshtml.cs*

Para exibir a classe gerada para *Pages/Index.cshtml*, abra *obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs*.

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

Adicione a seguinte classe ao projeto do ASP.NET Core MVC:

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

Em `Startup.ConfigureServices`, substitua o `RazorTemplateEngine` adicionado pelo MVC pela classe `CustomTemplateEngine`:

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

Defina o ponto de interrupção `CustomTemplateEngine` na instrução `return csharpDocument;`. Quando a execução do programa for interrompida no ponto de interrupção, veja o valor de `generatedCode`.

![Exibição do Visualizador de Texto de generatedCode](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a>Pesquisas de exibição e diferenciação de maiúsculas e minúsculas

O mecanismo de exibição do Razor executa pesquisas que diferenciam maiúsculas de minúsculas para as exibições. No entanto, a pesquisa real é determinada pelo sistema de arquivos subjacente:

* Origem baseada em arquivo:
  * Em sistemas operacionais com sistemas de arquivos que não diferenciam maiúsculas e minúsculas (por exemplo, Windows), pesquisas no provedor de arquivos físico não diferenciam maiúsculas de minúsculas. Por exemplo, `return View("Test")` resulta em correspondências para */Views/Home/Test.cshtml*, */Views/home/test.cshtml* e qualquer outra variação de maiúsculas e minúsculas.
  * Em sistemas de arquivos que diferenciam maiúsculas de minúsculas (por exemplo, Linux, OSX e com `EmbeddedFileProvider`), as pesquisas diferenciam maiúsculas de minúsculas. Por exemplo, `return View("Test")` corresponde especificamente a */Views/Home/Test.cshtml*.
* Exibições pré-compiladas: Com o ASP.NET Core 2.0 e posteriores, pesquisar em exibições pré-compiladas não diferencia maiúsculas de minúsculas em nenhum sistema operacional. O comportamento é idêntico ao comportamento do provedor de arquivos físico no Windows. Se duas exibições pré-compiladas diferirem apenas quanto ao padrão de maiúsculas e minúsculas, o resultado da pesquisa não será determinístico.

Os desenvolvedores são incentivados a fazer a correspondência entre as maiúsculas e minúsculas dos nomes dos arquivos e de diretórios com o uso de maiúsculas e minúsculas em:

* Nomes de área, controlador e ação.
* Páginas do Razor.

Fazer essa correspondência garante que as implantações encontrem suas exibições, independentemente do sistema de arquivos subjacente.
