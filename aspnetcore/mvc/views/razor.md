---
title: Referência da sintaxe Razor para ASP.NET Core
author: rick-anderson
description: Saiba mais sobre a sintaxe de marcação Razor para inserir código baseado em servidor em páginas da Web.
ms.author: riande
ms.date: 10/26/2018
uid: mvc/views/razor
ms.openlocfilehash: 8e9ec3c5040e5a24cd5f773b1232897338741c0c
ms.sourcegitcommit: 184ba5b44d1c393076015510ac842b77bc9d4d93
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2019
ms.locfileid: "54396253"
---
# <a name="razor-syntax-reference-for-aspnet-core"></a><span data-ttu-id="eb08d-103">Referência da sintaxe Razor para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="eb08d-103">Razor syntax reference for ASP.NET Core</span></span>

<span data-ttu-id="eb08d-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Luke Latham](https://github.com/guardrex), [Taylor Mullen](https://twitter.com/ntaylormullen) e [Dan Vicarel](https://github.com/Rabadash8820)</span><span class="sxs-lookup"><span data-stu-id="eb08d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Luke Latham](https://github.com/guardrex), [Taylor Mullen](https://twitter.com/ntaylormullen), and [Dan Vicarel](https://github.com/Rabadash8820)</span></span>

<span data-ttu-id="eb08d-105">Razor é uma sintaxe de marcação para inserir código baseado em servidor em páginas da Web.</span><span class="sxs-lookup"><span data-stu-id="eb08d-105">Razor is a markup syntax for embedding server-based code into webpages.</span></span> <span data-ttu-id="eb08d-106">A sintaxe Razor é composta pela marcação Razor, por C# e por HTML.</span><span class="sxs-lookup"><span data-stu-id="eb08d-106">The Razor syntax consists of Razor markup, C#, and HTML.</span></span> <span data-ttu-id="eb08d-107">Arquivos que contêm Razor geralmente têm a extensão de arquivo *.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="eb08d-107">Files containing Razor generally have a *.cshtml* file extension.</span></span>

## <a name="rendering-html"></a><span data-ttu-id="eb08d-108">Renderizando HTML</span><span class="sxs-lookup"><span data-stu-id="eb08d-108">Rendering HTML</span></span>

<span data-ttu-id="eb08d-109">A linguagem padrão do Razor padrão é o HTML.</span><span class="sxs-lookup"><span data-stu-id="eb08d-109">The default Razor language is HTML.</span></span> <span data-ttu-id="eb08d-110">Renderizar HTML da marcação Razor não é diferente de renderizar HTML de um arquivo HTML.</span><span class="sxs-lookup"><span data-stu-id="eb08d-110">Rendering HTML from Razor markup is no different than rendering HTML from an HTML file.</span></span> <span data-ttu-id="eb08d-111">A marcação HTML em arquivos Razor *.cshtml* é renderizada pelo servidor inalterado.</span><span class="sxs-lookup"><span data-stu-id="eb08d-111">HTML markup in *.cshtml* Razor files is rendered by the server unchanged.</span></span>

## <a name="razor-syntax"></a><span data-ttu-id="eb08d-112">Sintaxe Razor</span><span class="sxs-lookup"><span data-stu-id="eb08d-112">Razor syntax</span></span>

<span data-ttu-id="eb08d-113">O Razor dá suporte a C# e usa o símbolo `@` para fazer a transição de HTML para C#.</span><span class="sxs-lookup"><span data-stu-id="eb08d-113">Razor supports C# and uses the `@` symbol to transition from HTML to C#.</span></span> <span data-ttu-id="eb08d-114">O Razor avalia expressões em C# e as renderiza na saída HTML.</span><span class="sxs-lookup"><span data-stu-id="eb08d-114">Razor evaluates C# expressions and renders them in the HTML output.</span></span>

<span data-ttu-id="eb08d-115">Quando um símbolo `@` é seguido por uma [palavra-chave reservada do Razor](#razor-reserved-keywords), ele faz a transição para marcação específica do Razor.</span><span class="sxs-lookup"><span data-stu-id="eb08d-115">When an `@` symbol is followed by a [Razor reserved keyword](#razor-reserved-keywords), it transitions into Razor-specific markup.</span></span> <span data-ttu-id="eb08d-116">Caso contrário, ele faz a transição para C# simples.</span><span class="sxs-lookup"><span data-stu-id="eb08d-116">Otherwise, it transitions into plain C#.</span></span>

<span data-ttu-id="eb08d-117">Para fazer o escape de um símbolo `@` na marcação Razor, use um segundo símbolo `@`:</span><span class="sxs-lookup"><span data-stu-id="eb08d-117">To escape an `@` symbol in Razor markup, use a second `@` symbol:</span></span>

```cshtml
<p>@@Username</p>
```

<span data-ttu-id="eb08d-118">O código é renderizado em HTML com um único símbolo `@`:</span><span class="sxs-lookup"><span data-stu-id="eb08d-118">The code is rendered in HTML with a single `@` symbol:</span></span>

```html
<p>@Username</p>
```

<span data-ttu-id="eb08d-119">Conteúdo e atributos HTML que contêm endereços de email não tratam o símbolo `@` como um caractere de transição.</span><span class="sxs-lookup"><span data-stu-id="eb08d-119">HTML attributes and content containing email addresses don't treat the `@` symbol as a transition character.</span></span> <span data-ttu-id="eb08d-120">Os endereços de email no exemplo a seguir não são alterados pela análise do Razor:</span><span class="sxs-lookup"><span data-stu-id="eb08d-120">The email addresses in the following example are untouched by Razor parsing:</span></span>

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-razor-expressions"></a><span data-ttu-id="eb08d-121">Expressões Razor implícitas</span><span class="sxs-lookup"><span data-stu-id="eb08d-121">Implicit Razor expressions</span></span>

<span data-ttu-id="eb08d-122">Expressões Razor implícitas são iniciadas por `@` seguido do código C#:</span><span class="sxs-lookup"><span data-stu-id="eb08d-122">Implicit Razor expressions start with `@` followed by C# code:</span></span>

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

<span data-ttu-id="eb08d-123">Com exceção da palavra-chave C# `await`, expressões implícitas não devem conter espaços.</span><span class="sxs-lookup"><span data-stu-id="eb08d-123">With the exception of the C# `await` keyword, implicit expressions must not contain spaces.</span></span> <span data-ttu-id="eb08d-124">Se a instrução C# tiver uma terminação clara, espaços podem ser misturados:</span><span class="sxs-lookup"><span data-stu-id="eb08d-124">If the C# statement has a clear ending, spaces can be intermingled:</span></span>

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

<span data-ttu-id="eb08d-125">Expressões implícitas **não podem** conter elementos genéricos de C#, pois caracteres dentro de colchetes (`<>`) são interpretados como uma marca HTML.</span><span class="sxs-lookup"><span data-stu-id="eb08d-125">Implicit expressions **cannot** contain C# generics, as the characters inside the brackets (`<>`) are interpreted as an HTML tag.</span></span> <span data-ttu-id="eb08d-126">O código a seguir é **inválido**:</span><span class="sxs-lookup"><span data-stu-id="eb08d-126">The following code is **not** valid:</span></span>

```cshtml
<p>@GenericMethod<int>()</p>
```

<span data-ttu-id="eb08d-127">O código anterior gera um erro de compilador semelhante a um dos seguintes:</span><span class="sxs-lookup"><span data-stu-id="eb08d-127">The preceding code generates a compiler error similar to one of the following:</span></span>

 * <span data-ttu-id="eb08d-128">O elemento "int" não foi fechado.</span><span class="sxs-lookup"><span data-stu-id="eb08d-128">The "int" element wasn't closed.</span></span> <span data-ttu-id="eb08d-129">Todos os elementos devem ter fechamento automático ou ter uma marca de fim correspondente.</span><span class="sxs-lookup"><span data-stu-id="eb08d-129">All elements must be either self-closing or have a matching end tag.</span></span>
 *  <span data-ttu-id="eb08d-130">Não é possível converter o grupo de métodos "GenericMethod" em um "object" de tipo não delegado.</span><span class="sxs-lookup"><span data-stu-id="eb08d-130">Cannot convert method group 'GenericMethod' to non-delegate type 'object'.</span></span> <span data-ttu-id="eb08d-131">Você pretendia invocar o método?</span><span class="sxs-lookup"><span data-stu-id="eb08d-131">Did you intend to invoke the method?\`</span></span> 
 
<span data-ttu-id="eb08d-132">Chamadas de método genérico devem ser encapsuladas em uma [expressão Razor explícita](#explicit-razor-expressions) ou em um [bloco de código Razor](#razor-code-blocks).</span><span class="sxs-lookup"><span data-stu-id="eb08d-132">Generic method calls must be wrapped in an [explicit Razor expression](#explicit-razor-expressions) or a [Razor code block](#razor-code-blocks).</span></span>

## <a name="explicit-razor-expressions"></a><span data-ttu-id="eb08d-133">Expressões Razor explícitas</span><span class="sxs-lookup"><span data-stu-id="eb08d-133">Explicit Razor expressions</span></span>

<span data-ttu-id="eb08d-134">Expressões Razor explícitas consistem de um símbolo `@` com parênteses equilibradas.</span><span class="sxs-lookup"><span data-stu-id="eb08d-134">Explicit Razor expressions consist of an `@` symbol with balanced parenthesis.</span></span> <span data-ttu-id="eb08d-135">Para renderizar a hora da última semana, a seguinte marcação Razor é usada:</span><span class="sxs-lookup"><span data-stu-id="eb08d-135">To render last week's time, the following Razor markup is used:</span></span>

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

<span data-ttu-id="eb08d-136">Qualquer conteúdo dentro dos parênteses `@()` é avaliado e renderizado para a saída.</span><span class="sxs-lookup"><span data-stu-id="eb08d-136">Any content within the `@()` parenthesis is evaluated and rendered to the output.</span></span>

<span data-ttu-id="eb08d-137">Expressões implícitas, descritas na seção anterior, geralmente não podem conter espaços.</span><span class="sxs-lookup"><span data-stu-id="eb08d-137">Implicit expressions, described in the previous section, generally can't contain spaces.</span></span> <span data-ttu-id="eb08d-138">No código a seguir, uma semana não é subtraída da hora atual:</span><span class="sxs-lookup"><span data-stu-id="eb08d-138">In the following code, one week isn't subtracted from the current time:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

<span data-ttu-id="eb08d-139">O código renderiza o HTML a seguir:</span><span class="sxs-lookup"><span data-stu-id="eb08d-139">The code renders the following HTML:</span></span>

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

<span data-ttu-id="eb08d-140">Expressões explícitas podem ser usadas para concatenar texto com um resultado de expressão:</span><span class="sxs-lookup"><span data-stu-id="eb08d-140">Explicit expressions can be used to concatenate text with an expression result:</span></span>

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

<span data-ttu-id="eb08d-141">Sem a expressão explícita, `<p>Age@joe.Age</p>` é tratado como um endereço de email e `<p>Age@joe.Age</p>` é renderizado.</span><span class="sxs-lookup"><span data-stu-id="eb08d-141">Without the explicit expression, `<p>Age@joe.Age</p>` is treated as an email address, and `<p>Age@joe.Age</p>` is rendered.</span></span> <span data-ttu-id="eb08d-142">Quando escrito como uma expressão explícita, `<p>Age33</p>` é renderizado.</span><span class="sxs-lookup"><span data-stu-id="eb08d-142">When written as an explicit expression, `<p>Age33</p>` is rendered.</span></span>

<span data-ttu-id="eb08d-143">Expressões explícitas podem ser usadas para renderizar a saída de métodos genéricos em arquivos *.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="eb08d-143">Explicit expressions can be used to render output from generic methods in *.cshtml* files.</span></span> <span data-ttu-id="eb08d-144">A marcação a seguir mostra como corrigir o erro mostrado anteriormente causado pelos colchetes de um C# genérico.</span><span class="sxs-lookup"><span data-stu-id="eb08d-144">The following markup shows how to correct the error shown earlier caused by the brackets of a C# generic.</span></span> <span data-ttu-id="eb08d-145">O código é escrito como uma expressão explícita:</span><span class="sxs-lookup"><span data-stu-id="eb08d-145">The code is written as an explicit expression:</span></span>

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a><span data-ttu-id="eb08d-146">Codificação de expressão</span><span class="sxs-lookup"><span data-stu-id="eb08d-146">Expression encoding</span></span>

<span data-ttu-id="eb08d-147">Expressões em C# que são avaliadas como uma cadeia de caracteres estão codificadas em HTML.</span><span class="sxs-lookup"><span data-stu-id="eb08d-147">C# expressions that evaluate to a string are HTML encoded.</span></span> <span data-ttu-id="eb08d-148">Expressões em C# que são avaliadas como `IHtmlContent` são renderizadas diretamente por meio `IHtmlContent.WriteTo`.</span><span class="sxs-lookup"><span data-stu-id="eb08d-148">C# expressions that evaluate to `IHtmlContent` are rendered directly through `IHtmlContent.WriteTo`.</span></span> <span data-ttu-id="eb08d-149">Expressões em C# que não são avaliadas como `IHtmlContent` são convertidas em uma cadeia de caracteres por `ToString` e codificadas antes que sejam renderizadas.</span><span class="sxs-lookup"><span data-stu-id="eb08d-149">C# expressions that don't evaluate to `IHtmlContent` are converted to a string by `ToString` and encoded before they're rendered.</span></span>

```cshtml
@("<span>Hello World</span>")
```

<span data-ttu-id="eb08d-150">O código renderiza o HTML a seguir:</span><span class="sxs-lookup"><span data-stu-id="eb08d-150">The code renders the following HTML:</span></span>

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

<span data-ttu-id="eb08d-151">O HTML é mostrado no navegador como:</span><span class="sxs-lookup"><span data-stu-id="eb08d-151">The HTML is shown in the browser as:</span></span>

```
<span>Hello World</span>
```

<span data-ttu-id="eb08d-152">A saída `HtmlHelper.Raw` não é codificada, mas renderizada como marcação HTML.</span><span class="sxs-lookup"><span data-stu-id="eb08d-152">`HtmlHelper.Raw` output isn't encoded but rendered as HTML markup.</span></span>

> [!WARNING]
> <span data-ttu-id="eb08d-153">Usar `HtmlHelper.Raw` em uma entrada do usuário que não está limpa é um risco de segurança.</span><span class="sxs-lookup"><span data-stu-id="eb08d-153">Using `HtmlHelper.Raw` on unsanitized user input is a security risk.</span></span> <span data-ttu-id="eb08d-154">A entrada do usuário pode conter JavaScript mal-intencionado ou outras formas de exploração.</span><span class="sxs-lookup"><span data-stu-id="eb08d-154">User input might contain malicious JavaScript or other exploits.</span></span> <span data-ttu-id="eb08d-155">Limpar a entrada do usuário é difícil.</span><span class="sxs-lookup"><span data-stu-id="eb08d-155">Sanitizing user input is difficult.</span></span> <span data-ttu-id="eb08d-156">Evite usar `HtmlHelper.Raw` com a entrada do usuário.</span><span class="sxs-lookup"><span data-stu-id="eb08d-156">Avoid using `HtmlHelper.Raw` with user input.</span></span>

```cshtml
@Html.Raw("<span>Hello World</span>")
```

<span data-ttu-id="eb08d-157">O código renderiza o HTML a seguir:</span><span class="sxs-lookup"><span data-stu-id="eb08d-157">The code renders the following HTML:</span></span>

```html
<span>Hello World</span>
```

## <a name="razor-code-blocks"></a><span data-ttu-id="eb08d-158">Blocos de código Razor</span><span class="sxs-lookup"><span data-stu-id="eb08d-158">Razor code blocks</span></span>

<span data-ttu-id="eb08d-159">Blocos de código Razor são iniciados por `@` e delimitados por `{}`.</span><span class="sxs-lookup"><span data-stu-id="eb08d-159">Razor code blocks start with `@` and are enclosed by `{}`.</span></span> <span data-ttu-id="eb08d-160">Diferente das expressões, o código C# dentro de blocos de código não é renderizado.</span><span class="sxs-lookup"><span data-stu-id="eb08d-160">Unlike expressions, C# code inside code blocks isn't rendered.</span></span> <span data-ttu-id="eb08d-161">Blocos de código e expressões em uma exibição compartilham o mesmo escopo e são definidos em ordem:</span><span class="sxs-lookup"><span data-stu-id="eb08d-161">Code blocks and expressions in a view share the same scope and are defined in order:</span></span>

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

<span data-ttu-id="eb08d-162">O código renderiza o HTML a seguir:</span><span class="sxs-lookup"><span data-stu-id="eb08d-162">The code renders the following HTML:</span></span>

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

### <a name="implicit-transitions"></a><span data-ttu-id="eb08d-163">Transições implícitas</span><span class="sxs-lookup"><span data-stu-id="eb08d-163">Implicit transitions</span></span>

<span data-ttu-id="eb08d-164">A linguagem padrão em um bloco de código é C#, mas a página Razor pode fazer a transição de volta para HTML:</span><span class="sxs-lookup"><span data-stu-id="eb08d-164">The default language in a code block is C#, but the Razor Page can transition back to HTML:</span></span>

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a><span data-ttu-id="eb08d-165">Transição delimitada explícita</span><span class="sxs-lookup"><span data-stu-id="eb08d-165">Explicit delimited transition</span></span>

<span data-ttu-id="eb08d-166">Para definir uma subseção de um bloco de código que deve renderizar HTML, circunde os caracteres para renderização com as marca Razor **\<text>**:</span><span class="sxs-lookup"><span data-stu-id="eb08d-166">To define a subsection of a code block that should render HTML, surround the characters for rendering with the Razor **\<text>** tag:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

<span data-ttu-id="eb08d-167">Use essa abordagem para renderizar HTML que não está circundado por uma marca HTML.</span><span class="sxs-lookup"><span data-stu-id="eb08d-167">Use this approach to render HTML that isn't surrounded by an HTML tag.</span></span> <span data-ttu-id="eb08d-168">Sem uma marca HTML ou Razor, ocorrerá um erro de tempo de execução do Razor.</span><span class="sxs-lookup"><span data-stu-id="eb08d-168">Without an HTML or Razor tag, a Razor runtime error occurs.</span></span>

<span data-ttu-id="eb08d-169">A marca **\<text>** é útil para controlar o espaço em branco ao renderizar conteúdo:</span><span class="sxs-lookup"><span data-stu-id="eb08d-169">The **\<text>** tag is useful to control whitespace when rendering content:</span></span>

* <span data-ttu-id="eb08d-170">Somente o conteúdo entre a marca **\<text>** é renderizado.</span><span class="sxs-lookup"><span data-stu-id="eb08d-170">Only the content between the **\<text>** tag is rendered.</span></span> 
* <span data-ttu-id="eb08d-171">Não aparece nenhum espaço em branco antes ou depois da marca **\<text>** na saída HTML.</span><span class="sxs-lookup"><span data-stu-id="eb08d-171">No whitespace before or after the **\<text>** tag appears in the HTML output.</span></span>

### <a name="explicit-line-transition-with-"></a><span data-ttu-id="eb08d-172">Transição de linha explícita com @:</span><span class="sxs-lookup"><span data-stu-id="eb08d-172">Explicit Line Transition with @:</span></span>

<span data-ttu-id="eb08d-173">Para renderizar o restante de uma linha inteira como HTML dentro de um bloco de código, use a sintaxe `@:`:</span><span class="sxs-lookup"><span data-stu-id="eb08d-173">To render the rest of an entire line as HTML inside a code block, use the `@:` syntax:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

<span data-ttu-id="eb08d-174">Sem o `@:` no código, será gerado um erro de tempo de execução do Razor.</span><span class="sxs-lookup"><span data-stu-id="eb08d-174">Without the `@:` in the code, a Razor runtime error is generated.</span></span>

<span data-ttu-id="eb08d-175">Aviso: Caracteres `@` extras em um arquivo Razor podem causar erros do compilador em instruções mais adiante no bloco.</span><span class="sxs-lookup"><span data-stu-id="eb08d-175">Warning: Extra `@` characters in a Razor file can cause compiler errors at statements later in the block.</span></span> <span data-ttu-id="eb08d-176">Esses erros do compilador podem ser difíceis de entender porque o erro real ocorre antes do erro relatado.</span><span class="sxs-lookup"><span data-stu-id="eb08d-176">These compiler errors can be difficult to understand because the actual error occurs before the reported error.</span></span> <span data-ttu-id="eb08d-177">Esse erro é comum após combinar várias expressões implícitas/explícitas em um bloco de código único.</span><span class="sxs-lookup"><span data-stu-id="eb08d-177">This error is common after combining multiple implicit/explicit expressions into a single code block.</span></span>

## <a name="control-structures"></a><span data-ttu-id="eb08d-178">Estruturas de controle</span><span class="sxs-lookup"><span data-stu-id="eb08d-178">Control structures</span></span>

<span data-ttu-id="eb08d-179">Estruturas de controle são uma extensão dos blocos de código.</span><span class="sxs-lookup"><span data-stu-id="eb08d-179">Control structures are an extension of code blocks.</span></span> <span data-ttu-id="eb08d-180">Todos os aspectos dos blocos de código (transição para marcação, C# embutido) também se aplicam às seguintes estruturas:</span><span class="sxs-lookup"><span data-stu-id="eb08d-180">All aspects of code blocks (transitioning to markup, inline C#) also apply to the following structures:</span></span>

### <a name="conditionals-if-else-if-else-and-switch"></a><span data-ttu-id="eb08d-181">Condicionais @if, else if, else e @switch</span><span class="sxs-lookup"><span data-stu-id="eb08d-181">Conditionals @if, else if, else, and @switch</span></span>

<span data-ttu-id="eb08d-182">`@if` controla quando o código é executado:</span><span class="sxs-lookup"><span data-stu-id="eb08d-182">`@if` controls when code runs:</span></span>

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

<span data-ttu-id="eb08d-183">`else` e `else if` não exigem o símbolo `@`:</span><span class="sxs-lookup"><span data-stu-id="eb08d-183">`else` and `else if` don't require the `@` symbol:</span></span>

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

<span data-ttu-id="eb08d-184">A marcação a seguir mostra como usar uma instrução switch:</span><span class="sxs-lookup"><span data-stu-id="eb08d-184">The following markup shows how to use a switch statement:</span></span>

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

### <a name="looping-for-foreach-while-and-do-while"></a><span data-ttu-id="eb08d-185">Loop de @for, @foreach, @while e @do while</span><span class="sxs-lookup"><span data-stu-id="eb08d-185">Looping @for, @foreach, @while, and @do while</span></span>

<span data-ttu-id="eb08d-186">O HTML no modelo pode ser renderizado com instruções de controle em loop.</span><span class="sxs-lookup"><span data-stu-id="eb08d-186">Templated HTML can be rendered with looping control statements.</span></span> <span data-ttu-id="eb08d-187">Para renderizar uma lista de pessoas:</span><span class="sxs-lookup"><span data-stu-id="eb08d-187">To render a list of people:</span></span>

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

<span data-ttu-id="eb08d-188">Há suporte para as seguintes instruções em loop:</span><span class="sxs-lookup"><span data-stu-id="eb08d-188">The following looping statements are supported:</span></span>

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

### <a name="compound-using"></a><span data-ttu-id="eb08d-189">@using composto</span><span class="sxs-lookup"><span data-stu-id="eb08d-189">Compound @using</span></span>

<span data-ttu-id="eb08d-190">Em C#, uma instrução `using` é usada para garantir que um objeto seja descartado.</span><span class="sxs-lookup"><span data-stu-id="eb08d-190">In C#, a `using` statement is used to ensure an object is disposed.</span></span> <span data-ttu-id="eb08d-191">No Razor, o mesmo mecanismo é usado para criar Auxiliares HTML que têm conteúdo adicional.</span><span class="sxs-lookup"><span data-stu-id="eb08d-191">In Razor, the same mechanism is used to create HTML Helpers that contain additional content.</span></span> <span data-ttu-id="eb08d-192">No código a seguir, os Auxiliares HTML renderizam uma marca de formulário com a instrução `@using`:</span><span class="sxs-lookup"><span data-stu-id="eb08d-192">In the following code, HTML Helpers render a form tag with the `@using` statement:</span></span>


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

<span data-ttu-id="eb08d-193">Ações no nível de escopo podem ser executadas com [Auxiliares de marca](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="eb08d-193">Scope-level actions can be performed with [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

### <a name="try-catch-finally"></a><span data-ttu-id="eb08d-194">@try, catch, finally</span><span class="sxs-lookup"><span data-stu-id="eb08d-194">@try, catch, finally</span></span>

<span data-ttu-id="eb08d-195">O tratamento de exceções é semelhante ao de C#:</span><span class="sxs-lookup"><span data-stu-id="eb08d-195">Exception handling is similar to C#:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### <a name="lock"></a>@lock

<span data-ttu-id="eb08d-196">O Razor tem a capacidade de proteger seções críticas com instruções de bloqueio:</span><span class="sxs-lookup"><span data-stu-id="eb08d-196">Razor has the capability to protect critical sections with lock statements:</span></span>

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a><span data-ttu-id="eb08d-197">Comentários</span><span class="sxs-lookup"><span data-stu-id="eb08d-197">Comments</span></span>

<span data-ttu-id="eb08d-198">O Razor dá suporte a comentários em C# e HTML:</span><span class="sxs-lookup"><span data-stu-id="eb08d-198">Razor supports C# and HTML comments:</span></span>

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

<span data-ttu-id="eb08d-199">O código renderiza o HTML a seguir:</span><span class="sxs-lookup"><span data-stu-id="eb08d-199">The code renders the following HTML:</span></span>

```html
<!-- HTML comment -->
```

<span data-ttu-id="eb08d-200">Comentários em Razor são removidos pelo servidor antes que a página da Web seja renderizada.</span><span class="sxs-lookup"><span data-stu-id="eb08d-200">Razor comments are removed by the server before the webpage is rendered.</span></span> <span data-ttu-id="eb08d-201">O Razor usa `@*  *@` para delimitar comentários.</span><span class="sxs-lookup"><span data-stu-id="eb08d-201">Razor uses `@*  *@` to delimit comments.</span></span> <span data-ttu-id="eb08d-202">O código a seguir é comentado, de modo que o servidor não renderiza nenhuma marcação:</span><span class="sxs-lookup"><span data-stu-id="eb08d-202">The following code is commented out, so the server doesn't render any markup:</span></span>

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a><span data-ttu-id="eb08d-203">Diretivas</span><span class="sxs-lookup"><span data-stu-id="eb08d-203">Directives</span></span>

<span data-ttu-id="eb08d-204">Diretivas de Razor são representadas por expressões implícitas com palavras-chave reservadas após o símbolo `@`.</span><span class="sxs-lookup"><span data-stu-id="eb08d-204">Razor directives are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="eb08d-205">Uma diretiva geralmente altera o modo como uma exibição é analisada ou habilita uma funcionalidade diferente.</span><span class="sxs-lookup"><span data-stu-id="eb08d-205">A directive typically changes the way a view is parsed or enables different functionality.</span></span>

<span data-ttu-id="eb08d-206">Compreender como o Razor gera código para uma exibição torna mais fácil entender como as diretivas funcionam.</span><span class="sxs-lookup"><span data-stu-id="eb08d-206">Understanding how Razor generates code for a view makes it easier to understand how directives work.</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact8.cshtml)]

<span data-ttu-id="eb08d-207">O código gera uma classe semelhante à seguinte:</span><span class="sxs-lookup"><span data-stu-id="eb08d-207">The code generates a class similar to the following:</span></span>

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

<span data-ttu-id="eb08d-208">Mais adiante neste artigo, a seção [Inspecionar a classe do Razor C# gerada para uma exibição](#inspect-the-razor-c-class-generated-for-a-view) explica como exibir essa classe gerada.</span><span class="sxs-lookup"><span data-stu-id="eb08d-208">Later in this article, the section [Inspect the Razor C# class generated for a view](#inspect-the-razor-c-class-generated-for-a-view) explains how to view this generated class.</span></span>

<a name="using"></a>
### <a name="using"></a>@using

<span data-ttu-id="eb08d-209">A diretiva `@using` adiciona a diretiva `using` de C# à exibição gerada:</span><span class="sxs-lookup"><span data-stu-id="eb08d-209">The `@using` directive adds the C# `using` directive to the generated view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

### <a name="model"></a>@model

<span data-ttu-id="eb08d-210">A diretiva `@model` especifica o tipo do modelo passado para uma exibição:</span><span class="sxs-lookup"><span data-stu-id="eb08d-210">The `@model` directive specifies the type of the model passed to a view:</span></span>

```cshtml
@model TypeNameOfModel
```

<span data-ttu-id="eb08d-211">Em um aplicativo do ASP.NET Core MCV criado com contas de usuário individuais, a exibição *Views/Account/Login.cshtml* contém a declaração de modelo a seguir:</span><span class="sxs-lookup"><span data-stu-id="eb08d-211">In an ASP.NET Core MVC app created with individual user accounts, the *Views/Account/Login.cshtml* view contains the following model declaration:</span></span>

```cshtml
@model LoginViewModel
```

<span data-ttu-id="eb08d-212">A classe gerada herda de `RazorPage<dynamic>`:</span><span class="sxs-lookup"><span data-stu-id="eb08d-212">The class generated inherits from `RazorPage<dynamic>`:</span></span>

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

<span data-ttu-id="eb08d-213">O Razor expõe uma propriedade `Model` para acessar o modelo passado para a exibição:</span><span class="sxs-lookup"><span data-stu-id="eb08d-213">Razor exposes a `Model` property for accessing the model passed to the view:</span></span>

```cshtml
<div>The Login Email: @Model.Email</div>
```

<span data-ttu-id="eb08d-214">A diretiva `@model` especifica o tipo dessa propriedade.</span><span class="sxs-lookup"><span data-stu-id="eb08d-214">The `@model` directive specifies the type of this property.</span></span> <span data-ttu-id="eb08d-215">A diretiva especifica o `T` em `RazorPage<T>` da classe gerada da qual a exibição deriva.</span><span class="sxs-lookup"><span data-stu-id="eb08d-215">The directive specifies the `T` in `RazorPage<T>` that the generated class that the view derives from.</span></span> <span data-ttu-id="eb08d-216">Se a diretiva `@model` não for especificada, a propriedade `Model` será do tipo `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="eb08d-216">If the `@model` directive isn't specified, the `Model` property is of type `dynamic`.</span></span> <span data-ttu-id="eb08d-217">O valor do modelo é passado do controlador para a exibição.</span><span class="sxs-lookup"><span data-stu-id="eb08d-217">The value of the model is passed from the controller to the view.</span></span> <span data-ttu-id="eb08d-218">Para obter mais informações, confira [Modelos fortemente tipados e a &commat;palavra-chave do modelo](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span><span class="sxs-lookup"><span data-stu-id="eb08d-218">For more information, see [Strongly typed models and the &commat;model keyword](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span></span>

### <a name="inherits"></a>@inherits

<span data-ttu-id="eb08d-219">A diretiva `@inherits` fornece controle total da classe que a exibição herda:</span><span class="sxs-lookup"><span data-stu-id="eb08d-219">The `@inherits` directive provides full control of the class the view inherits:</span></span>

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

<span data-ttu-id="eb08d-220">O código a seguir é um tipo de página Razor personalizado:</span><span class="sxs-lookup"><span data-stu-id="eb08d-220">The following code is a custom Razor page type:</span></span>

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

<span data-ttu-id="eb08d-221">O `CustomText` é exibido em uma exibição:</span><span class="sxs-lookup"><span data-stu-id="eb08d-221">The `CustomText` is displayed in a view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

<span data-ttu-id="eb08d-222">O código renderiza o HTML a seguir:</span><span class="sxs-lookup"><span data-stu-id="eb08d-222">The code renders the following HTML:</span></span>

```html
<div>Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping a slop bucket on the street below.</div>
```

 <span data-ttu-id="eb08d-223">`@model` e `@inherits` podem ser usados na mesma exibição.</span><span class="sxs-lookup"><span data-stu-id="eb08d-223">`@model` and `@inherits` can be used in the same view.</span></span> <span data-ttu-id="eb08d-224">`@inherits` pode estar em um arquivo *_ViewImports.cshtml* que a exibição importa:</span><span class="sxs-lookup"><span data-stu-id="eb08d-224">`@inherits` can be in a *_ViewImports.cshtml* file that the view imports:</span></span>

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

<span data-ttu-id="eb08d-225">O código a seguir é um exemplo de exibição fortemente tipada:</span><span class="sxs-lookup"><span data-stu-id="eb08d-225">The following code is an example of a strongly-typed view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

<span data-ttu-id="eb08d-226">Se "rick@contoso.com" for passado no modelo, a exibição gerará a seguinte marcação HTML:</span><span class="sxs-lookup"><span data-stu-id="eb08d-226">If "rick@contoso.com" is passed in the model, the view generates the following HTML markup:</span></span>

```html
<div>The Login Email: rick@contoso.com</div>
<div>Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping a slop bucket on the street below.</div>
```

### <a name="inject"></a>@inject

<span data-ttu-id="eb08d-227">A diretiva `@inject` permite que a página do Razor injete um serviço do [contêiner de serviço](xref:fundamentals/dependency-injection) em uma exibição.</span><span class="sxs-lookup"><span data-stu-id="eb08d-227">The `@inject` directive enables the Razor Page to inject a service from the [service container](xref:fundamentals/dependency-injection) into a view.</span></span> <span data-ttu-id="eb08d-228">Para obter mais informações, consulte [Injeção de dependência em exibições](xref:mvc/views/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="eb08d-228">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span>

### <a name="functions"></a>@functions

<span data-ttu-id="eb08d-229">A diretiva `@functions` permite que uma página Razor adicione um bloco de código C# a uma exibição:</span><span class="sxs-lookup"><span data-stu-id="eb08d-229">The `@functions` directive enables a Razor Page to add a C# code block to a view:</span></span>

```cshtml
@functions { // C# Code }
```

<span data-ttu-id="eb08d-230">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="eb08d-230">For example:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

<span data-ttu-id="eb08d-231">O código gera a seguinte marcação HTML:</span><span class="sxs-lookup"><span data-stu-id="eb08d-231">The code generates the following HTML markup:</span></span>

```html
<div>From method: Hello</div>
```

<span data-ttu-id="eb08d-232">O código a seguir é a classe C# do Razor gerada:</span><span class="sxs-lookup"><span data-stu-id="eb08d-232">The following code is the generated Razor C# class:</span></span>

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

### <a name="section"></a>@section

<span data-ttu-id="eb08d-233">A diretiva `@section` é usada em conjunto com o [layout](xref:mvc/views/layout) para permitir que as exibições renderizem conteúdo em partes diferentes da página HTML.</span><span class="sxs-lookup"><span data-stu-id="eb08d-233">The `@section` directive is used in conjunction with the [layout](xref:mvc/views/layout) to enable views to render content in different parts of the HTML page.</span></span> <span data-ttu-id="eb08d-234">Para obter mais informações, consulte [Seções](xref:mvc/views/layout#layout-sections-label).</span><span class="sxs-lookup"><span data-stu-id="eb08d-234">For more information, see [Sections](xref:mvc/views/layout#layout-sections-label).</span></span>

## <a name="templated-razor-delegates"></a><span data-ttu-id="eb08d-235">Representantes do Razor com modelo</span><span class="sxs-lookup"><span data-stu-id="eb08d-235">Templated Razor delegates</span></span>

<span data-ttu-id="eb08d-236">Os modelos Razor permitem que você defina um snippet da interface do usuário com o seguinte formato:</span><span class="sxs-lookup"><span data-stu-id="eb08d-236">Razor templates allow you to define a UI snippet with the following format:</span></span>

```cshtml
@<tag>...</tag>
```

<span data-ttu-id="eb08d-237">O exemplo a seguir ilustra como especificar um delegado do Razor com modelo como um <xref:System.Func`2>.</span><span class="sxs-lookup"><span data-stu-id="eb08d-237">The following example illustrates how to specify a templated Razor delegate as a <xref:System.Func`2>.</span></span> <span data-ttu-id="eb08d-238">O [tipo dinâmico](/dotnet/csharp/programming-guide/types/using-type-dynamic) é especificado para o parâmetro do método encapsulado pelo delegado.</span><span class="sxs-lookup"><span data-stu-id="eb08d-238">The [dynamic type](/dotnet/csharp/programming-guide/types/using-type-dynamic) is specified for the parameter of the method that the delegate encapsulates.</span></span> <span data-ttu-id="eb08d-239">Um [tipo de objeto](/dotnet/csharp/language-reference/keywords/object) é especificado como o valor retornado do delegado.</span><span class="sxs-lookup"><span data-stu-id="eb08d-239">An [object type](/dotnet/csharp/language-reference/keywords/object) is specified as the return value of the delegate.</span></span> <span data-ttu-id="eb08d-240">O modelo é usado com uma <xref:System.Collections.Generic.List`1> de `Pet` que tem uma propriedade `Name`.</span><span class="sxs-lookup"><span data-stu-id="eb08d-240">The template is used with a <xref:System.Collections.Generic.List`1> of `Pet` that has a `Name` property.</span></span>

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

<span data-ttu-id="eb08d-241">O modelo é renderizado com `pets` fornecido por uma instrução `foreach`:</span><span class="sxs-lookup"><span data-stu-id="eb08d-241">The template is rendered with `pets` supplied by a `foreach` statement:</span></span>

```cshtml
@foreach (var pet in pets)
{
    @petTemplate(pet)
}
```

<span data-ttu-id="eb08d-242">Saída renderizada:</span><span class="sxs-lookup"><span data-stu-id="eb08d-242">Rendered output:</span></span>

```html
<p>You have a pet named <strong>Rin Tin Tin</strong>.</p>
<p>You have a pet named <strong>Mr. Bigglesworth</strong>.</p>
<p>You have a pet named <strong>K-9</strong>.</p>
```

<span data-ttu-id="eb08d-243">Você também pode fornecer um modelo Razor embutido como um argumento para um método.</span><span class="sxs-lookup"><span data-stu-id="eb08d-243">You can also supply an inline Razor template as an argument to a method.</span></span> <span data-ttu-id="eb08d-244">No exemplo a seguir, o método `Repeat` recebe um modelo Razor.</span><span class="sxs-lookup"><span data-stu-id="eb08d-244">In the following example, the `Repeat` method receives a Razor template.</span></span> <span data-ttu-id="eb08d-245">O método usa o modelo para produzir o conteúdo HTML com repetições de itens fornecidos em uma lista:</span><span class="sxs-lookup"><span data-stu-id="eb08d-245">The method uses the template to produce HTML content with repeats of items supplied from a list:</span></span>

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

<span data-ttu-id="eb08d-246">Usando a lista de animais de estimação do exemplo anterior, o método `Repeat` é chamado com:</span><span class="sxs-lookup"><span data-stu-id="eb08d-246">Using the list of pets from the prior example, the `Repeat` method is called with:</span></span>

* <span data-ttu-id="eb08d-247"><xref:System.Collections.Generic.List`1> de `Pet`.</span><span class="sxs-lookup"><span data-stu-id="eb08d-247"><xref:System.Collections.Generic.List`1> of `Pet`.</span></span>
* <span data-ttu-id="eb08d-248">Número de vezes que deve ser repetido cada animal de estimação.</span><span class="sxs-lookup"><span data-stu-id="eb08d-248">Number of times to repeat each pet.</span></span>
* <span data-ttu-id="eb08d-249">Modelo embutido a ser usado para os itens da lista de uma lista não ordenada.</span><span class="sxs-lookup"><span data-stu-id="eb08d-249">Inline template to use for the list items of an unordered list.</span></span>

```cshtml
<ul>
    @Repeat(pets, 3, @<li>@item.Name</li>)
</ul>
```

<span data-ttu-id="eb08d-250">Saída renderizada:</span><span class="sxs-lookup"><span data-stu-id="eb08d-250">Rendered output:</span></span>

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

## <a name="tag-helpers"></a><span data-ttu-id="eb08d-251">Auxiliares de Marca</span><span class="sxs-lookup"><span data-stu-id="eb08d-251">Tag Helpers</span></span>

<span data-ttu-id="eb08d-252">Há três diretivas que relacionadas aos [Auxiliares de marca](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="eb08d-252">There are three directives that pertain to [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

| <span data-ttu-id="eb08d-253">Diretiva</span><span class="sxs-lookup"><span data-stu-id="eb08d-253">Directive</span></span> | <span data-ttu-id="eb08d-254">Função</span><span class="sxs-lookup"><span data-stu-id="eb08d-254">Function</span></span> |
| --------- | -------- |
| [<span data-ttu-id="eb08d-255">&commat;addTagHelper</span><span class="sxs-lookup"><span data-stu-id="eb08d-255">&commat;addTagHelper</span></span>](xref:mvc/views/tag-helpers/intro#add-helper-label) | <span data-ttu-id="eb08d-256">Disponibiliza os Auxiliares de marca para uma exibição.</span><span class="sxs-lookup"><span data-stu-id="eb08d-256">Makes Tag Helpers available to a view.</span></span> |
| [<span data-ttu-id="eb08d-257">&commat;removeTagHelper</span><span class="sxs-lookup"><span data-stu-id="eb08d-257">&commat;removeTagHelper</span></span>](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | <span data-ttu-id="eb08d-258">Remove os Auxiliares de marca adicionados anteriormente de uma exibição.</span><span class="sxs-lookup"><span data-stu-id="eb08d-258">Removes Tag Helpers previously added from a view.</span></span> |
| [<span data-ttu-id="eb08d-259">&commat;tagHelperPrefix</span><span class="sxs-lookup"><span data-stu-id="eb08d-259">&commat;tagHelperPrefix</span></span>](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | <span data-ttu-id="eb08d-260">Especifica um prefixo de marca para habilitar o suporte do Auxiliar de marca e tornar explícito o uso do Auxiliar de marca.</span><span class="sxs-lookup"><span data-stu-id="eb08d-260">Specifies a tag prefix to enable Tag Helper support and to make Tag Helper usage explicit.</span></span> |

## <a name="razor-reserved-keywords"></a><span data-ttu-id="eb08d-261">Palavras-chave reservadas ao Razor</span><span class="sxs-lookup"><span data-stu-id="eb08d-261">Razor reserved keywords</span></span>

### <a name="razor-keywords"></a><span data-ttu-id="eb08d-262">Palavras-chave do Razor</span><span class="sxs-lookup"><span data-stu-id="eb08d-262">Razor keywords</span></span>

* <span data-ttu-id="eb08d-263">page (requer o ASP.NET Core 2.0 e posteriores)</span><span class="sxs-lookup"><span data-stu-id="eb08d-263">page (Requires ASP.NET Core 2.0 and later)</span></span>
* <span data-ttu-id="eb08d-264">namespace</span><span class="sxs-lookup"><span data-stu-id="eb08d-264">namespace</span></span>
* <span data-ttu-id="eb08d-265">funções</span><span class="sxs-lookup"><span data-stu-id="eb08d-265">functions</span></span>
* <span data-ttu-id="eb08d-266">herda</span><span class="sxs-lookup"><span data-stu-id="eb08d-266">inherits</span></span>
* <span data-ttu-id="eb08d-267">modelo</span><span class="sxs-lookup"><span data-stu-id="eb08d-267">model</span></span>
* <span data-ttu-id="eb08d-268">section</span><span class="sxs-lookup"><span data-stu-id="eb08d-268">section</span></span>
* <span data-ttu-id="eb08d-269">helper (atualmente sem suporte do ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="eb08d-269">helper (Not currently supported by ASP.NET Core)</span></span>

<span data-ttu-id="eb08d-270">Palavras-chave do Razor têm o escape feito com `@(Razor Keyword)` (por exemplo, `@(functions)`).</span><span class="sxs-lookup"><span data-stu-id="eb08d-270">Razor keywords are escaped with `@(Razor Keyword)` (for example, `@(functions)`).</span></span>

### <a name="c-razor-keywords"></a><span data-ttu-id="eb08d-271">Palavras-chave do Razor em C#</span><span class="sxs-lookup"><span data-stu-id="eb08d-271">C# Razor keywords</span></span>

* <span data-ttu-id="eb08d-272">case</span><span class="sxs-lookup"><span data-stu-id="eb08d-272">case</span></span>
* <span data-ttu-id="eb08d-273">do</span><span class="sxs-lookup"><span data-stu-id="eb08d-273">do</span></span>
* <span data-ttu-id="eb08d-274">default</span><span class="sxs-lookup"><span data-stu-id="eb08d-274">default</span></span>
* <span data-ttu-id="eb08d-275">for</span><span class="sxs-lookup"><span data-stu-id="eb08d-275">for</span></span>
* <span data-ttu-id="eb08d-276">foreach</span><span class="sxs-lookup"><span data-stu-id="eb08d-276">foreach</span></span>
* <span data-ttu-id="eb08d-277">if</span><span class="sxs-lookup"><span data-stu-id="eb08d-277">if</span></span>
* <span data-ttu-id="eb08d-278">else</span><span class="sxs-lookup"><span data-stu-id="eb08d-278">else</span></span>
* <span data-ttu-id="eb08d-279">bloqueio</span><span class="sxs-lookup"><span data-stu-id="eb08d-279">lock</span></span>
* <span data-ttu-id="eb08d-280">switch</span><span class="sxs-lookup"><span data-stu-id="eb08d-280">switch</span></span>
* <span data-ttu-id="eb08d-281">try</span><span class="sxs-lookup"><span data-stu-id="eb08d-281">try</span></span>
* <span data-ttu-id="eb08d-282">catch</span><span class="sxs-lookup"><span data-stu-id="eb08d-282">catch</span></span>
* <span data-ttu-id="eb08d-283">finally</span><span class="sxs-lookup"><span data-stu-id="eb08d-283">finally</span></span>
* <span data-ttu-id="eb08d-284">using</span><span class="sxs-lookup"><span data-stu-id="eb08d-284">using</span></span>
* <span data-ttu-id="eb08d-285">while</span><span class="sxs-lookup"><span data-stu-id="eb08d-285">while</span></span>

<span data-ttu-id="eb08d-286">Palavras-chave do Razor em C# precisam ter o escape duplo com `@(@C# Razor Keyword)` (por exemplo, `@(@case)`).</span><span class="sxs-lookup"><span data-stu-id="eb08d-286">C# Razor keywords must be double-escaped with `@(@C# Razor Keyword)` (for example, `@(@case)`).</span></span> <span data-ttu-id="eb08d-287">O primeiro `@` faz o escape do analisador Razor.</span><span class="sxs-lookup"><span data-stu-id="eb08d-287">The first `@` escapes the Razor parser.</span></span> <span data-ttu-id="eb08d-288">O segundo `@` faz o escape do analisador C#.</span><span class="sxs-lookup"><span data-stu-id="eb08d-288">The second `@` escapes the C# parser.</span></span>

### <a name="reserved-keywords-not-used-by-razor"></a><span data-ttu-id="eb08d-289">Palavras-chave reservadas não usadas pelo Razor</span><span class="sxs-lookup"><span data-stu-id="eb08d-289">Reserved keywords not used by Razor</span></span>

* <span data-ttu-id="eb08d-290">classe</span><span class="sxs-lookup"><span data-stu-id="eb08d-290">class</span></span>

## <a name="inspect-the-razor-c-class-generated-for-a-view"></a><span data-ttu-id="eb08d-291">Inspecionar a classe do Razor C# gerada para uma exibição</span><span class="sxs-lookup"><span data-stu-id="eb08d-291">Inspect the Razor C# class generated for a view</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="eb08d-292">Com o SDK do .NET Core 2.1 ou posterior, o [SDK do Razor](xref:razor-pages/sdk) lida com a compilação de arquivos do Razor.</span><span class="sxs-lookup"><span data-stu-id="eb08d-292">With .NET Core SDK 2.1 or later, the [Razor SDK](xref:razor-pages/sdk) handles compilation of Razor files.</span></span> <span data-ttu-id="eb08d-293">Ao compilar um projeto, o SDK do Razor gera um diretório *obj/<configuração_de_build>/<moniker_da_estrutura_de_destino>/Razor* na raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="eb08d-293">When building a project, the Razor SDK generates an *obj/<build_configuration>/<target_framework_moniker>/Razor* directory in the project root.</span></span> <span data-ttu-id="eb08d-294">A estrutura de diretórios dentro do diretório do *Razor* espelha a estrutura de diretórios do projeto.</span><span class="sxs-lookup"><span data-stu-id="eb08d-294">The directory structure within the *Razor* directory mirrors the project's directory structure.</span></span>

<span data-ttu-id="eb08d-295">Considere a seguinte estrutura de diretórios em um projeto do Razor Pages ASP.NET Core 2.1 direcionado ao .NET Core 2.1:</span><span class="sxs-lookup"><span data-stu-id="eb08d-295">Consider the following directory structure in an ASP.NET Core 2.1 Razor Pages project targeting .NET Core 2.1:</span></span>

* <span data-ttu-id="eb08d-296">**Areas/**</span><span class="sxs-lookup"><span data-stu-id="eb08d-296">**Areas/**</span></span>
  * <span data-ttu-id="eb08d-297">**Admin/**</span><span class="sxs-lookup"><span data-stu-id="eb08d-297">**Admin/**</span></span>
    * <span data-ttu-id="eb08d-298">**Pages/**</span><span class="sxs-lookup"><span data-stu-id="eb08d-298">**Pages/**</span></span>
      * <span data-ttu-id="eb08d-299">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="eb08d-299">*Index.cshtml*</span></span>
      * <span data-ttu-id="eb08d-300">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="eb08d-300">*Index.cshtml.cs*</span></span>
* <span data-ttu-id="eb08d-301">**Pages/**</span><span class="sxs-lookup"><span data-stu-id="eb08d-301">**Pages/**</span></span>
  * <span data-ttu-id="eb08d-302">**Shared/**</span><span class="sxs-lookup"><span data-stu-id="eb08d-302">**Shared/**</span></span>
    * <span data-ttu-id="eb08d-303">*_Layout.cshtml*</span><span class="sxs-lookup"><span data-stu-id="eb08d-303">*_Layout.cshtml*</span></span>
  * <span data-ttu-id="eb08d-304">*_ViewImports.cshtml*</span><span class="sxs-lookup"><span data-stu-id="eb08d-304">*_ViewImports.cshtml*</span></span>
  * <span data-ttu-id="eb08d-305">*_ViewStart.cshtml*</span><span class="sxs-lookup"><span data-stu-id="eb08d-305">*_ViewStart.cshtml*</span></span>
  * <span data-ttu-id="eb08d-306">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="eb08d-306">*Index.cshtml*</span></span>
  * <span data-ttu-id="eb08d-307">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="eb08d-307">*Index.cshtml.cs*</span></span>

<span data-ttu-id="eb08d-308">A criação do projeto na configuração de *Depuração* produz o seguinte diretório *obj*:</span><span class="sxs-lookup"><span data-stu-id="eb08d-308">Building the project in *Debug* configuration yields the following *obj* directory:</span></span>

* <span data-ttu-id="eb08d-309">**obj/**</span><span class="sxs-lookup"><span data-stu-id="eb08d-309">**obj/**</span></span>
  * <span data-ttu-id="eb08d-310">**Debug/**</span><span class="sxs-lookup"><span data-stu-id="eb08d-310">**Debug/**</span></span>
    * <span data-ttu-id="eb08d-311">**netcoreapp2.1/**</span><span class="sxs-lookup"><span data-stu-id="eb08d-311">**netcoreapp2.1/**</span></span>
      * <span data-ttu-id="eb08d-312">**Razor/**</span><span class="sxs-lookup"><span data-stu-id="eb08d-312">**Razor/**</span></span>
        * <span data-ttu-id="eb08d-313">**Areas/**</span><span class="sxs-lookup"><span data-stu-id="eb08d-313">**Areas/**</span></span>
          * <span data-ttu-id="eb08d-314">**Admin/**</span><span class="sxs-lookup"><span data-stu-id="eb08d-314">**Admin/**</span></span>
            * <span data-ttu-id="eb08d-315">**Pages/**</span><span class="sxs-lookup"><span data-stu-id="eb08d-315">**Pages/**</span></span>
              * <span data-ttu-id="eb08d-316">*Index.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="eb08d-316">*Index.g.cshtml.cs*</span></span>
        * <span data-ttu-id="eb08d-317">**Pages/**</span><span class="sxs-lookup"><span data-stu-id="eb08d-317">**Pages/**</span></span>
          * <span data-ttu-id="eb08d-318">**Shared/**</span><span class="sxs-lookup"><span data-stu-id="eb08d-318">**Shared/**</span></span>
            * <span data-ttu-id="eb08d-319">*_Layout.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="eb08d-319">*_Layout.g.cshtml.cs*</span></span>
          * <span data-ttu-id="eb08d-320">*_ViewImports.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="eb08d-320">*_ViewImports.g.cshtml.cs*</span></span>
          * <span data-ttu-id="eb08d-321">*_ViewStart.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="eb08d-321">*_ViewStart.g.cshtml.cs*</span></span>
          * <span data-ttu-id="eb08d-322">*Index.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="eb08d-322">*Index.g.cshtml.cs*</span></span>

<span data-ttu-id="eb08d-323">Para exibir a classe gerada para *Pages/Index.cshtml*, abra *obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="eb08d-323">To view the generated class for *Pages/Index.cshtml*, open *obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs*.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="eb08d-324">Adicione a seguinte classe ao projeto do ASP.NET Core MVC:</span><span class="sxs-lookup"><span data-stu-id="eb08d-324">Add the following class to the ASP.NET Core MVC project:</span></span>

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

<span data-ttu-id="eb08d-325">Em `Startup.ConfigureServices`, substitua o `RazorTemplateEngine` adicionado pelo MVC pela classe `CustomTemplateEngine`:</span><span class="sxs-lookup"><span data-stu-id="eb08d-325">In `Startup.ConfigureServices`, override the `RazorTemplateEngine` added by MVC with the `CustomTemplateEngine` class:</span></span>

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

<span data-ttu-id="eb08d-326">Defina o ponto de interrupção `CustomTemplateEngine` na instrução `return csharpDocument;`.</span><span class="sxs-lookup"><span data-stu-id="eb08d-326">Set a breakpoint on the `return csharpDocument;` statement of `CustomTemplateEngine`.</span></span> <span data-ttu-id="eb08d-327">Quando a execução do programa for interrompida no ponto de interrupção, veja o valor de `generatedCode`.</span><span class="sxs-lookup"><span data-stu-id="eb08d-327">When program execution stops at the breakpoint, view the value of `generatedCode`.</span></span>

![Exibição do Visualizador de Texto de generatedCode](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a><span data-ttu-id="eb08d-329">Pesquisas de exibição e diferenciação de maiúsculas e minúsculas</span><span class="sxs-lookup"><span data-stu-id="eb08d-329">View lookups and case sensitivity</span></span>

<span data-ttu-id="eb08d-330">O mecanismo de exibição do Razor executa pesquisas que diferenciam maiúsculas de minúsculas para as exibições.</span><span class="sxs-lookup"><span data-stu-id="eb08d-330">The Razor view engine performs case-sensitive lookups for views.</span></span> <span data-ttu-id="eb08d-331">No entanto, a pesquisa real é determinada pelo sistema de arquivos subjacente:</span><span class="sxs-lookup"><span data-stu-id="eb08d-331">However, the actual lookup is determined by the underlying file system:</span></span>

* <span data-ttu-id="eb08d-332">Origem baseada em arquivo:</span><span class="sxs-lookup"><span data-stu-id="eb08d-332">File based source:</span></span>
  * <span data-ttu-id="eb08d-333">Em sistemas operacionais com sistemas de arquivos que não diferenciam maiúsculas e minúsculas (por exemplo, Windows), pesquisas no provedor de arquivos físico não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="eb08d-333">On operating systems with case insensitive file systems (for example, Windows), physical file provider lookups are case insensitive.</span></span> <span data-ttu-id="eb08d-334">Por exemplo, `return View("Test")` resulta em correspondências para */Views/Home/Test.cshtml*, */Views/home/test.cshtml* e qualquer outra variação de maiúsculas e minúsculas.</span><span class="sxs-lookup"><span data-stu-id="eb08d-334">For example, `return View("Test")` results in matches for */Views/Home/Test.cshtml*, */Views/home/test.cshtml*, and any other casing variant.</span></span>
  * <span data-ttu-id="eb08d-335">Em sistemas de arquivos que diferenciam maiúsculas de minúsculas (por exemplo, Linux, OSX e com `EmbeddedFileProvider`), as pesquisas diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="eb08d-335">On case-sensitive file systems (for example, Linux, OSX, and with `EmbeddedFileProvider`), lookups are case-sensitive.</span></span> <span data-ttu-id="eb08d-336">Por exemplo, `return View("Test")` corresponde especificamente a */Views/Home/Test.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="eb08d-336">For example, `return View("Test")` specifically matches */Views/Home/Test.cshtml*.</span></span>
* <span data-ttu-id="eb08d-337">Exibições pré-compiladas: Com o ASP.NET Core 2.0 e posteriores, pesquisar em exibições pré-compiladas não diferencia maiúsculas de minúsculas em nenhum sistema operacional.</span><span class="sxs-lookup"><span data-stu-id="eb08d-337">Precompiled views: With ASP.NET Core 2.0 and later, looking up precompiled views is case insensitive on all operating systems.</span></span> <span data-ttu-id="eb08d-338">O comportamento é idêntico ao comportamento do provedor de arquivos físico no Windows.</span><span class="sxs-lookup"><span data-stu-id="eb08d-338">The behavior is identical to physical file provider's behavior on Windows.</span></span> <span data-ttu-id="eb08d-339">Se duas exibições pré-compiladas diferirem apenas quanto ao padrão de maiúsculas e minúsculas, o resultado da pesquisa não será determinístico.</span><span class="sxs-lookup"><span data-stu-id="eb08d-339">If two precompiled views differ only in case, the result of lookup is non-deterministic.</span></span>

<span data-ttu-id="eb08d-340">Os desenvolvedores são incentivados a fazer a correspondência entre as maiúsculas e minúsculas dos nomes dos arquivos e de diretórios com o uso de maiúsculas e minúsculas em:</span><span class="sxs-lookup"><span data-stu-id="eb08d-340">Developers are encouraged to match the casing of file and directory names to the casing of:</span></span>

* <span data-ttu-id="eb08d-341">Nomes de área, controlador e ação.</span><span class="sxs-lookup"><span data-stu-id="eb08d-341">Area, controller, and action names.</span></span>
* <span data-ttu-id="eb08d-342">Páginas do Razor.</span><span class="sxs-lookup"><span data-stu-id="eb08d-342">Razor Pages.</span></span>

<span data-ttu-id="eb08d-343">Fazer essa correspondência garante que as implantações encontrem suas exibições, independentemente do sistema de arquivos subjacente.</span><span class="sxs-lookup"><span data-stu-id="eb08d-343">Matching case ensures the deployments find their views regardless of the underlying file system.</span></span>
