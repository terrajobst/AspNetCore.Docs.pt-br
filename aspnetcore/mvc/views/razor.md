---
title: Referência da sintaxe Razor para ASP.NET Core
author: rick-anderson
description: Saiba mais sobre a sintaxe de marcação Razor para inserir código baseado em servidor em páginas da Web.
ms.author: riande
ms.date: 12/05/2019
uid: mvc/views/razor
ms.openlocfilehash: baac0ac38a0781cb9c16689cf3e29526b602d8da
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74944246"
---
# <a name="razor-syntax-reference-for-aspnet-core"></a><span data-ttu-id="aa01c-103">Referência da sintaxe Razor para ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="aa01c-103">Razor syntax reference for ASP.NET Core</span></span>

<span data-ttu-id="aa01c-104">Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Luke Latham](https://github.com/guardrex), [Taylor Mullen](https://twitter.com/ntaylormullen) e [Dan Vicarel](https://github.com/Rabadash8820)</span><span class="sxs-lookup"><span data-stu-id="aa01c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Luke Latham](https://github.com/guardrex), [Taylor Mullen](https://twitter.com/ntaylormullen), and [Dan Vicarel](https://github.com/Rabadash8820)</span></span>

<span data-ttu-id="aa01c-105">Razor é uma sintaxe de marcação para inserir código baseado em servidor em páginas da Web.</span><span class="sxs-lookup"><span data-stu-id="aa01c-105">Razor is a markup syntax for embedding server-based code into webpages.</span></span> <span data-ttu-id="aa01c-106">A sintaxe Razor é composta pela marcação Razor, por C# e por HTML.</span><span class="sxs-lookup"><span data-stu-id="aa01c-106">The Razor syntax consists of Razor markup, C#, and HTML.</span></span> <span data-ttu-id="aa01c-107">Arquivos que contêm Razor geralmente têm a extensão de arquivo *.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="aa01c-107">Files containing Razor generally have a *.cshtml* file extension.</span></span> <span data-ttu-id="aa01c-108">O Razor também é encontrado em arquivos de [Componentes do Razor](xref:blazor/components) ( *.razor*).</span><span class="sxs-lookup"><span data-stu-id="aa01c-108">Razor is also found in [Razor components](xref:blazor/components) files (*.razor*).</span></span>

## <a name="rendering-html"></a><span data-ttu-id="aa01c-109">Renderizando HTML</span><span class="sxs-lookup"><span data-stu-id="aa01c-109">Rendering HTML</span></span>

<span data-ttu-id="aa01c-110">A linguagem padrão do Razor padrão é o HTML.</span><span class="sxs-lookup"><span data-stu-id="aa01c-110">The default Razor language is HTML.</span></span> <span data-ttu-id="aa01c-111">Renderizar HTML da marcação Razor não é diferente de renderizar HTML de um arquivo HTML.</span><span class="sxs-lookup"><span data-stu-id="aa01c-111">Rendering HTML from Razor markup is no different than rendering HTML from an HTML file.</span></span> <span data-ttu-id="aa01c-112">A marcação HTML em arquivos Razor *.cshtml* é renderizada pelo servidor inalterado.</span><span class="sxs-lookup"><span data-stu-id="aa01c-112">HTML markup in *.cshtml* Razor files is rendered by the server unchanged.</span></span>

## <a name="razor-syntax"></a><span data-ttu-id="aa01c-113">Sintaxe Razor</span><span class="sxs-lookup"><span data-stu-id="aa01c-113">Razor syntax</span></span>

<span data-ttu-id="aa01c-114">O Razor dá suporte a C# e usa o símbolo `@` para fazer a transição de HTML para C#.</span><span class="sxs-lookup"><span data-stu-id="aa01c-114">Razor supports C# and uses the `@` symbol to transition from HTML to C#.</span></span> <span data-ttu-id="aa01c-115">O Razor avalia expressões em C# e as renderiza na saída HTML.</span><span class="sxs-lookup"><span data-stu-id="aa01c-115">Razor evaluates C# expressions and renders them in the HTML output.</span></span>

<span data-ttu-id="aa01c-116">Quando um símbolo `@` é seguido por uma [palavra-chave reservada do Razor](#razor-reserved-keywords), ele faz a transição para marcação específica do Razor.</span><span class="sxs-lookup"><span data-stu-id="aa01c-116">When an `@` symbol is followed by a [Razor reserved keyword](#razor-reserved-keywords), it transitions into Razor-specific markup.</span></span> <span data-ttu-id="aa01c-117">Caso contrário, ele faz a transição para C# simples.</span><span class="sxs-lookup"><span data-stu-id="aa01c-117">Otherwise, it transitions into plain C#.</span></span>

<span data-ttu-id="aa01c-118">Para fazer o escape de um símbolo `@` na marcação Razor, use um segundo símbolo `@`:</span><span class="sxs-lookup"><span data-stu-id="aa01c-118">To escape an `@` symbol in Razor markup, use a second `@` symbol:</span></span>

```cshtml
<p>@@Username</p>
```

<span data-ttu-id="aa01c-119">O código é renderizado em HTML com um único símbolo `@`:</span><span class="sxs-lookup"><span data-stu-id="aa01c-119">The code is rendered in HTML with a single `@` symbol:</span></span>

```html
<p>@Username</p>
```

<span data-ttu-id="aa01c-120">Conteúdo e atributos HTML que contêm endereços de email não tratam o símbolo `@` como um caractere de transição.</span><span class="sxs-lookup"><span data-stu-id="aa01c-120">HTML attributes and content containing email addresses don't treat the `@` symbol as a transition character.</span></span> <span data-ttu-id="aa01c-121">Os endereços de email no exemplo a seguir não são alterados pela análise do Razor:</span><span class="sxs-lookup"><span data-stu-id="aa01c-121">The email addresses in the following example are untouched by Razor parsing:</span></span>

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-razor-expressions"></a><span data-ttu-id="aa01c-122">Expressões Razor implícitas</span><span class="sxs-lookup"><span data-stu-id="aa01c-122">Implicit Razor expressions</span></span>

<span data-ttu-id="aa01c-123">Expressões Razor implícitas são iniciadas por `@` seguido do código C#:</span><span class="sxs-lookup"><span data-stu-id="aa01c-123">Implicit Razor expressions start with `@` followed by C# code:</span></span>

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

<span data-ttu-id="aa01c-124">Com exceção da palavra-chave C# `await`, expressões implícitas não devem conter espaços.</span><span class="sxs-lookup"><span data-stu-id="aa01c-124">With the exception of the C# `await` keyword, implicit expressions must not contain spaces.</span></span> <span data-ttu-id="aa01c-125">Se a instrução C# tiver uma terminação clara, espaços podem ser misturados:</span><span class="sxs-lookup"><span data-stu-id="aa01c-125">If the C# statement has a clear ending, spaces can be intermingled:</span></span>

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

<span data-ttu-id="aa01c-126">Expressões implícitas **não podem** conter elementos genéricos de C#, pois caracteres dentro de colchetes (`<>`) são interpretados como uma marca HTML.</span><span class="sxs-lookup"><span data-stu-id="aa01c-126">Implicit expressions **cannot** contain C# generics, as the characters inside the brackets (`<>`) are interpreted as an HTML tag.</span></span> <span data-ttu-id="aa01c-127">O código a seguir é **inválido**:</span><span class="sxs-lookup"><span data-stu-id="aa01c-127">The following code is **not** valid:</span></span>

```cshtml
<p>@GenericMethod<int>()</p>
```

<span data-ttu-id="aa01c-128">O código anterior gera um erro de compilador semelhante a um dos seguintes:</span><span class="sxs-lookup"><span data-stu-id="aa01c-128">The preceding code generates a compiler error similar to one of the following:</span></span>

* <span data-ttu-id="aa01c-129">O elemento "int" não foi fechado.</span><span class="sxs-lookup"><span data-stu-id="aa01c-129">The "int" element wasn't closed.</span></span> <span data-ttu-id="aa01c-130">Todos os elementos devem ter fechamento automático ou ter uma marca de fim correspondente.</span><span class="sxs-lookup"><span data-stu-id="aa01c-130">All elements must be either self-closing or have a matching end tag.</span></span>
* <span data-ttu-id="aa01c-131">Não é possível converter o grupo de métodos "GenericMethod" em um "object" de tipo não delegado.</span><span class="sxs-lookup"><span data-stu-id="aa01c-131">Cannot convert method group 'GenericMethod' to non-delegate type 'object'.</span></span> <span data-ttu-id="aa01c-132">Você pretendia invocar o método?</span><span class="sxs-lookup"><span data-stu-id="aa01c-132">Did you intend to invoke the method?\`</span></span>

<span data-ttu-id="aa01c-133">Chamadas de método genérico devem ser encapsuladas em uma [expressão Razor explícita](#explicit-razor-expressions) ou em um [bloco de código Razor](#razor-code-blocks).</span><span class="sxs-lookup"><span data-stu-id="aa01c-133">Generic method calls must be wrapped in an [explicit Razor expression](#explicit-razor-expressions) or a [Razor code block](#razor-code-blocks).</span></span>

## <a name="explicit-razor-expressions"></a><span data-ttu-id="aa01c-134">Expressões Razor explícitas</span><span class="sxs-lookup"><span data-stu-id="aa01c-134">Explicit Razor expressions</span></span>

<span data-ttu-id="aa01c-135">Expressões Razor explícitas consistem de um símbolo `@` com parênteses equilibradas.</span><span class="sxs-lookup"><span data-stu-id="aa01c-135">Explicit Razor expressions consist of an `@` symbol with balanced parenthesis.</span></span> <span data-ttu-id="aa01c-136">Para renderizar a hora da última semana, a seguinte marcação Razor é usada:</span><span class="sxs-lookup"><span data-stu-id="aa01c-136">To render last week's time, the following Razor markup is used:</span></span>

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

<span data-ttu-id="aa01c-137">Qualquer conteúdo dentro dos parênteses `@()` é avaliado e renderizado para a saída.</span><span class="sxs-lookup"><span data-stu-id="aa01c-137">Any content within the `@()` parenthesis is evaluated and rendered to the output.</span></span>

<span data-ttu-id="aa01c-138">Expressões implícitas, descritas na seção anterior, geralmente não podem conter espaços.</span><span class="sxs-lookup"><span data-stu-id="aa01c-138">Implicit expressions, described in the previous section, generally can't contain spaces.</span></span> <span data-ttu-id="aa01c-139">No código a seguir, uma semana não é subtraída da hora atual:</span><span class="sxs-lookup"><span data-stu-id="aa01c-139">In the following code, one week isn't subtracted from the current time:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

<span data-ttu-id="aa01c-140">O código renderiza o HTML a seguir:</span><span class="sxs-lookup"><span data-stu-id="aa01c-140">The code renders the following HTML:</span></span>

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

<span data-ttu-id="aa01c-141">Expressões explícitas podem ser usadas para concatenar texto com um resultado de expressão:</span><span class="sxs-lookup"><span data-stu-id="aa01c-141">Explicit expressions can be used to concatenate text with an expression result:</span></span>

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

<span data-ttu-id="aa01c-142">Sem a expressão explícita, `<p>Age@joe.Age</p>` é tratado como um endereço de email e `<p>Age@joe.Age</p>` é renderizado.</span><span class="sxs-lookup"><span data-stu-id="aa01c-142">Without the explicit expression, `<p>Age@joe.Age</p>` is treated as an email address, and `<p>Age@joe.Age</p>` is rendered.</span></span> <span data-ttu-id="aa01c-143">Quando escrito como uma expressão explícita, `<p>Age33</p>` é renderizado.</span><span class="sxs-lookup"><span data-stu-id="aa01c-143">When written as an explicit expression, `<p>Age33</p>` is rendered.</span></span>

<span data-ttu-id="aa01c-144">Expressões explícitas podem ser usadas para renderizar a saída de métodos genéricos em arquivos *.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="aa01c-144">Explicit expressions can be used to render output from generic methods in *.cshtml* files.</span></span> <span data-ttu-id="aa01c-145">A marcação a seguir mostra como corrigir o erro mostrado anteriormente causado pelos colchetes de um C# genérico.</span><span class="sxs-lookup"><span data-stu-id="aa01c-145">The following markup shows how to correct the error shown earlier caused by the brackets of a C# generic.</span></span> <span data-ttu-id="aa01c-146">O código é escrito como uma expressão explícita:</span><span class="sxs-lookup"><span data-stu-id="aa01c-146">The code is written as an explicit expression:</span></span>

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a><span data-ttu-id="aa01c-147">Codificação de expressão</span><span class="sxs-lookup"><span data-stu-id="aa01c-147">Expression encoding</span></span>

<span data-ttu-id="aa01c-148">Expressões em C# que são avaliadas como uma cadeia de caracteres estão codificadas em HTML.</span><span class="sxs-lookup"><span data-stu-id="aa01c-148">C# expressions that evaluate to a string are HTML encoded.</span></span> <span data-ttu-id="aa01c-149">Expressões em C# que são avaliadas como `IHtmlContent` são renderizadas diretamente por meio `IHtmlContent.WriteTo`.</span><span class="sxs-lookup"><span data-stu-id="aa01c-149">C# expressions that evaluate to `IHtmlContent` are rendered directly through `IHtmlContent.WriteTo`.</span></span> <span data-ttu-id="aa01c-150">Expressões em C# que não são avaliadas como `IHtmlContent` são convertidas em uma cadeia de caracteres por `ToString` e codificadas antes que sejam renderizadas.</span><span class="sxs-lookup"><span data-stu-id="aa01c-150">C# expressions that don't evaluate to `IHtmlContent` are converted to a string by `ToString` and encoded before they're rendered.</span></span>

```cshtml
@("<span>Hello World</span>")
```

<span data-ttu-id="aa01c-151">O código renderiza o HTML a seguir:</span><span class="sxs-lookup"><span data-stu-id="aa01c-151">The code renders the following HTML:</span></span>

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

<span data-ttu-id="aa01c-152">O HTML é mostrado no navegador como:</span><span class="sxs-lookup"><span data-stu-id="aa01c-152">The HTML is shown in the browser as:</span></span>

```
<span>Hello World</span>
```

<span data-ttu-id="aa01c-153">A saída `HtmlHelper.Raw` não é codificada, mas renderizada como marcação HTML.</span><span class="sxs-lookup"><span data-stu-id="aa01c-153">`HtmlHelper.Raw` output isn't encoded but rendered as HTML markup.</span></span>

> [!WARNING]
> <span data-ttu-id="aa01c-154">Usar `HtmlHelper.Raw` em uma entrada do usuário que não está limpa é um risco de segurança.</span><span class="sxs-lookup"><span data-stu-id="aa01c-154">Using `HtmlHelper.Raw` on unsanitized user input is a security risk.</span></span> <span data-ttu-id="aa01c-155">A entrada do usuário pode conter JavaScript mal-intencionado ou outras formas de exploração.</span><span class="sxs-lookup"><span data-stu-id="aa01c-155">User input might contain malicious JavaScript or other exploits.</span></span> <span data-ttu-id="aa01c-156">Limpar a entrada do usuário é difícil.</span><span class="sxs-lookup"><span data-stu-id="aa01c-156">Sanitizing user input is difficult.</span></span> <span data-ttu-id="aa01c-157">Evite usar `HtmlHelper.Raw` com a entrada do usuário.</span><span class="sxs-lookup"><span data-stu-id="aa01c-157">Avoid using `HtmlHelper.Raw` with user input.</span></span>

```cshtml
@Html.Raw("<span>Hello World</span>")
```

<span data-ttu-id="aa01c-158">O código renderiza o HTML a seguir:</span><span class="sxs-lookup"><span data-stu-id="aa01c-158">The code renders the following HTML:</span></span>

```html
<span>Hello World</span>
```

## <a name="razor-code-blocks"></a><span data-ttu-id="aa01c-159">Blocos de código Razor</span><span class="sxs-lookup"><span data-stu-id="aa01c-159">Razor code blocks</span></span>

<span data-ttu-id="aa01c-160">Blocos de código Razor são iniciados por `@` e delimitados por `{}`.</span><span class="sxs-lookup"><span data-stu-id="aa01c-160">Razor code blocks start with `@` and are enclosed by `{}`.</span></span> <span data-ttu-id="aa01c-161">Diferente das expressões, o código C# dentro de blocos de código não é renderizado.</span><span class="sxs-lookup"><span data-stu-id="aa01c-161">Unlike expressions, C# code inside code blocks isn't rendered.</span></span> <span data-ttu-id="aa01c-162">Blocos de código e expressões em uma exibição compartilham o mesmo escopo e são definidos em ordem:</span><span class="sxs-lookup"><span data-stu-id="aa01c-162">Code blocks and expressions in a view share the same scope and are defined in order:</span></span>

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

<span data-ttu-id="aa01c-163">O código renderiza o HTML a seguir:</span><span class="sxs-lookup"><span data-stu-id="aa01c-163">The code renders the following HTML:</span></span>

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="aa01c-164">Em blocos de código, declare [funções locais](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) com uma marcação para servir como métodos de modelagem:</span><span class="sxs-lookup"><span data-stu-id="aa01c-164">In code blocks, declare [local functions](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) with markup to serve as templating methods:</span></span>

```cshtml
@{
    void RenderName(string name)
    {
        <p>Name: <strong>@name</strong></p>
    }

    RenderName("Mahatma Gandhi");
    RenderName("Martin Luther King, Jr.");
}
```

<span data-ttu-id="aa01c-165">O código renderiza o HTML a seguir:</span><span class="sxs-lookup"><span data-stu-id="aa01c-165">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

::: moniker-end

### <a name="implicit-transitions"></a><span data-ttu-id="aa01c-166">Transições implícitas</span><span class="sxs-lookup"><span data-stu-id="aa01c-166">Implicit transitions</span></span>

<span data-ttu-id="aa01c-167">A linguagem padrão em um bloco de código é C#, mas a página Razor pode fazer a transição de volta para HTML:</span><span class="sxs-lookup"><span data-stu-id="aa01c-167">The default language in a code block is C#, but the Razor Page can transition back to HTML:</span></span>

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a><span data-ttu-id="aa01c-168">Transição delimitada explícita</span><span class="sxs-lookup"><span data-stu-id="aa01c-168">Explicit delimited transition</span></span>

<span data-ttu-id="aa01c-169">Para definir uma subseção de um bloco de código que deve renderizar HTML, circunde os caracteres para renderização com a marca de Razor `<text>`:</span><span class="sxs-lookup"><span data-stu-id="aa01c-169">To define a subsection of a code block that should render HTML, surround the characters for rendering with the Razor `<text>` tag:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

<span data-ttu-id="aa01c-170">Use essa abordagem para renderizar HTML que não está circundado por uma marca HTML.</span><span class="sxs-lookup"><span data-stu-id="aa01c-170">Use this approach to render HTML that isn't surrounded by an HTML tag.</span></span> <span data-ttu-id="aa01c-171">Sem uma marca HTML ou Razor, ocorrerá um erro de runtime do Razor.</span><span class="sxs-lookup"><span data-stu-id="aa01c-171">Without an HTML or Razor tag, a Razor runtime error occurs.</span></span>

<span data-ttu-id="aa01c-172">A marca `<text>` é útil para controlar o espaço em branco ao renderizar conteúdo:</span><span class="sxs-lookup"><span data-stu-id="aa01c-172">The `<text>` tag is useful to control whitespace when rendering content:</span></span>

* <span data-ttu-id="aa01c-173">Somente o conteúdo entre a marca `<text>` é renderizado.</span><span class="sxs-lookup"><span data-stu-id="aa01c-173">Only the content between the `<text>` tag is rendered.</span></span>
* <span data-ttu-id="aa01c-174">Não aparece nenhum espaço em branco antes ou depois da marca `<text>` na saída HTML.</span><span class="sxs-lookup"><span data-stu-id="aa01c-174">No whitespace before or after the `<text>` tag appears in the HTML output.</span></span>

### <a name="explicit-line-transition"></a><span data-ttu-id="aa01c-175">Transição de linha explícita</span><span class="sxs-lookup"><span data-stu-id="aa01c-175">Explicit line transition</span></span>

<span data-ttu-id="aa01c-176">Para renderizar o restante de uma linha inteira como HTML dentro de um bloco de código, use `@:` sintaxe:</span><span class="sxs-lookup"><span data-stu-id="aa01c-176">To render the rest of an entire line as HTML inside a code block, use `@:` syntax:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

<span data-ttu-id="aa01c-177">Sem o `@:` no código, será gerado um erro de runtime do Razor.</span><span class="sxs-lookup"><span data-stu-id="aa01c-177">Without the `@:` in the code, a Razor runtime error is generated.</span></span>

<span data-ttu-id="aa01c-178">Caracteres `@` extras em um arquivo Razor podem causar erros do compilador em instruções mais adiante no bloco.</span><span class="sxs-lookup"><span data-stu-id="aa01c-178">Extra `@` characters in a Razor file can cause compiler errors at statements later in the block.</span></span> <span data-ttu-id="aa01c-179">Esses erros do compilador podem ser difíceis de entender porque o erro real ocorre antes do erro relatado.</span><span class="sxs-lookup"><span data-stu-id="aa01c-179">These compiler errors can be difficult to understand because the actual error occurs before the reported error.</span></span> <span data-ttu-id="aa01c-180">Esse erro é comum após combinar várias expressões implícitas/explícitas em um bloco de código único.</span><span class="sxs-lookup"><span data-stu-id="aa01c-180">This error is common after combining multiple implicit/explicit expressions into a single code block.</span></span>

## <a name="control-structures"></a><span data-ttu-id="aa01c-181">Estruturas de controle</span><span class="sxs-lookup"><span data-stu-id="aa01c-181">Control structures</span></span>

<span data-ttu-id="aa01c-182">Estruturas de controle são uma extensão dos blocos de código.</span><span class="sxs-lookup"><span data-stu-id="aa01c-182">Control structures are an extension of code blocks.</span></span> <span data-ttu-id="aa01c-183">Todos os aspectos dos blocos de código (transição para marcação, C# embutido) também se aplicam às seguintes estruturas:</span><span class="sxs-lookup"><span data-stu-id="aa01c-183">All aspects of code blocks (transitioning to markup, inline C#) also apply to the following structures:</span></span>

### <a name="conditionals-if-else-if-else-and-switch"></a><span data-ttu-id="aa01c-184">Condicionais \@if, else if, else e \@switch</span><span class="sxs-lookup"><span data-stu-id="aa01c-184">Conditionals \@if, else if, else, and \@switch</span></span>

<span data-ttu-id="aa01c-185">`@if` controla quando o código é executado:</span><span class="sxs-lookup"><span data-stu-id="aa01c-185">`@if` controls when code runs:</span></span>

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

<span data-ttu-id="aa01c-186">`else` e `else if` não exigem o símbolo `@`:</span><span class="sxs-lookup"><span data-stu-id="aa01c-186">`else` and `else if` don't require the `@` symbol:</span></span>

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

<span data-ttu-id="aa01c-187">A marcação a seguir mostra como usar uma instrução switch:</span><span class="sxs-lookup"><span data-stu-id="aa01c-187">The following markup shows how to use a switch statement:</span></span>

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

### <a name="looping-for-foreach-while-and-do-while"></a><span data-ttu-id="aa01c-188">Looping \@for, \@foreach, \@while e \@do while</span><span class="sxs-lookup"><span data-stu-id="aa01c-188">Looping \@for, \@foreach, \@while, and \@do while</span></span>

<span data-ttu-id="aa01c-189">O HTML no modelo pode ser renderizado com instruções de controle em loop.</span><span class="sxs-lookup"><span data-stu-id="aa01c-189">Templated HTML can be rendered with looping control statements.</span></span> <span data-ttu-id="aa01c-190">Para renderizar uma lista de pessoas:</span><span class="sxs-lookup"><span data-stu-id="aa01c-190">To render a list of people:</span></span>

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

<span data-ttu-id="aa01c-191">Há suporte para as seguintes instruções em loop:</span><span class="sxs-lookup"><span data-stu-id="aa01c-191">The following looping statements are supported:</span></span>

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

### <a name="compound-using"></a><span data-ttu-id="aa01c-192">Composto \@using</span><span class="sxs-lookup"><span data-stu-id="aa01c-192">Compound \@using</span></span>

<span data-ttu-id="aa01c-193">Em C#, uma instrução `using` é usada para garantir que um objeto seja descartado.</span><span class="sxs-lookup"><span data-stu-id="aa01c-193">In C#, a `using` statement is used to ensure an object is disposed.</span></span> <span data-ttu-id="aa01c-194">No Razor, o mesmo mecanismo é usado para criar Auxiliares HTML que têm conteúdo adicional.</span><span class="sxs-lookup"><span data-stu-id="aa01c-194">In Razor, the same mechanism is used to create HTML Helpers that contain additional content.</span></span> <span data-ttu-id="aa01c-195">No código a seguir, os Auxiliares HTML renderizam uma marca `<form>` com a instrução `@using`:</span><span class="sxs-lookup"><span data-stu-id="aa01c-195">In the following code, HTML Helpers render a `<form>` tag with the `@using` statement:</span></span>

```cshtml
@using (Html.BeginForm())
{
    <div>
        Email: <input type="email" id="Email" value="">
        <button>Register</button>
    </div>
}
```

### <a name="try-catch-finally"></a><span data-ttu-id="aa01c-196">\@try, catch, finally</span><span class="sxs-lookup"><span data-stu-id="aa01c-196">\@try, catch, finally</span></span>

<span data-ttu-id="aa01c-197">O tratamento de exceções é semelhante ao de C#:</span><span class="sxs-lookup"><span data-stu-id="aa01c-197">Exception handling is similar to C#:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### <a name="lock"></a><span data-ttu-id="aa01c-198">\@lock</span><span class="sxs-lookup"><span data-stu-id="aa01c-198">\@lock</span></span>

<span data-ttu-id="aa01c-199">O Razor tem a capacidade de proteger seções críticas com instruções de bloqueio:</span><span class="sxs-lookup"><span data-stu-id="aa01c-199">Razor has the capability to protect critical sections with lock statements:</span></span>

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a><span data-ttu-id="aa01c-200">Comments</span><span class="sxs-lookup"><span data-stu-id="aa01c-200">Comments</span></span>

<span data-ttu-id="aa01c-201">O Razor dá suporte a comentários em C# e HTML:</span><span class="sxs-lookup"><span data-stu-id="aa01c-201">Razor supports C# and HTML comments:</span></span>

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

<span data-ttu-id="aa01c-202">O código renderiza o HTML a seguir:</span><span class="sxs-lookup"><span data-stu-id="aa01c-202">The code renders the following HTML:</span></span>

```html
<!-- HTML comment -->
```

<span data-ttu-id="aa01c-203">Comentários em Razor são removidos pelo servidor antes que a página da Web seja renderizada.</span><span class="sxs-lookup"><span data-stu-id="aa01c-203">Razor comments are removed by the server before the webpage is rendered.</span></span> <span data-ttu-id="aa01c-204">O Razor usa `@*  *@` para delimitar comentários.</span><span class="sxs-lookup"><span data-stu-id="aa01c-204">Razor uses `@*  *@` to delimit comments.</span></span> <span data-ttu-id="aa01c-205">O código a seguir é comentado, de modo que o servidor não renderiza nenhuma marcação:</span><span class="sxs-lookup"><span data-stu-id="aa01c-205">The following code is commented out, so the server doesn't render any markup:</span></span>

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a><span data-ttu-id="aa01c-206">Diretivas</span><span class="sxs-lookup"><span data-stu-id="aa01c-206">Directives</span></span>

<span data-ttu-id="aa01c-207">Diretivas de Razor são representadas por expressões implícitas com palavras-chave reservadas após o símbolo `@`.</span><span class="sxs-lookup"><span data-stu-id="aa01c-207">Razor directives are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="aa01c-208">Uma diretiva geralmente altera o modo como uma exibição é analisada ou habilita uma funcionalidade diferente.</span><span class="sxs-lookup"><span data-stu-id="aa01c-208">A directive typically changes the way a view is parsed or enables different functionality.</span></span>

<span data-ttu-id="aa01c-209">Compreender como o Razor gera código para uma exibição torna mais fácil entender como as diretivas funcionam.</span><span class="sxs-lookup"><span data-stu-id="aa01c-209">Understanding how Razor generates code for a view makes it easier to understand how directives work.</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact8.cshtml)]

<span data-ttu-id="aa01c-210">O código gera uma classe semelhante à seguinte:</span><span class="sxs-lookup"><span data-stu-id="aa01c-210">The code generates a class similar to the following:</span></span>

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

<span data-ttu-id="aa01c-211">Mais adiante neste artigo, a seção [Inspecionar a classe do Razor C# gerada para uma exibição](#inspect-the-razor-c-class-generated-for-a-view) explica como exibir essa classe gerada.</span><span class="sxs-lookup"><span data-stu-id="aa01c-211">Later in this article, the section [Inspect the Razor C# class generated for a view](#inspect-the-razor-c-class-generated-for-a-view) explains how to view this generated class.</span></span>

### <a name="attribute"></a><span data-ttu-id="aa01c-212">\@attribute</span><span class="sxs-lookup"><span data-stu-id="aa01c-212">\@attribute</span></span>

<span data-ttu-id="aa01c-213">A diretiva `@attribute` adiciona o atributo fornecido à classe da página ou exibição gerada.</span><span class="sxs-lookup"><span data-stu-id="aa01c-213">The `@attribute` directive adds the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="aa01c-214">O exemplo a seguir adiciona o atributo `[Authorize]`:</span><span class="sxs-lookup"><span data-stu-id="aa01c-214">The following example adds the `[Authorize]` attribute:</span></span>

```cshtml
@attribute [Authorize]
```

::: moniker range=">= aspnetcore-3.0"

### <a name="code"></a><span data-ttu-id="aa01c-215">\@code</span><span class="sxs-lookup"><span data-stu-id="aa01c-215">\@code</span></span>

<span data-ttu-id="aa01c-216">*Este cenário aplica-se somente a componentes do Razor (.razor).*</span><span class="sxs-lookup"><span data-stu-id="aa01c-216">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="aa01c-217">O bloco `@code` permite que um [componente de Razor](xref:blazor/components) adicione membros (campos, propriedades e métodos) de C# a um componente:</span><span class="sxs-lookup"><span data-stu-id="aa01c-217">The `@code` block enables a [Razor component](xref:blazor/components) to add C# members (fields, properties, and methods) to a component:</span></span>

```razor
@code {
    // C# members (fields, properties, and methods)
}
```

<span data-ttu-id="aa01c-218">Para componentes de Razor, `@code` é um alias de [`@functions`](#functions) e é recomendado seu uso com `@functions`.</span><span class="sxs-lookup"><span data-stu-id="aa01c-218">For Razor components, `@code` is an alias of [`@functions`](#functions) and recommended over `@functions`.</span></span> <span data-ttu-id="aa01c-219">Mais de um bloco de `@code` é permitido.</span><span class="sxs-lookup"><span data-stu-id="aa01c-219">More than one `@code` block is permissible.</span></span>

::: moniker-end

### <a name="functions"></a><span data-ttu-id="aa01c-220">\@functions</span><span class="sxs-lookup"><span data-stu-id="aa01c-220">\@functions</span></span>

<span data-ttu-id="aa01c-221">A diretiva `@functions` permite adicionar membros (campos, propriedades e métodos) de C# à classe gerada:</span><span class="sxs-lookup"><span data-stu-id="aa01c-221">The `@functions` directive enables adding C# members (fields, properties, and methods) to the generated class:</span></span>

```cshtml
@functions {
    // C# members (fields, properties, and methods)
}
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="aa01c-222">Em [Componentes de Razor](xref:blazor/components), use `@code` com `@functions` para adicionar membros de C#.</span><span class="sxs-lookup"><span data-stu-id="aa01c-222">In [Razor components](xref:blazor/components), use `@code` over `@functions` to add C# members.</span></span>

::: moniker-end

<span data-ttu-id="aa01c-223">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="aa01c-223">For example:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

<span data-ttu-id="aa01c-224">O código gera a seguinte marcação HTML:</span><span class="sxs-lookup"><span data-stu-id="aa01c-224">The code generates the following HTML markup:</span></span>

```html
<div>From method: Hello</div>
```

<span data-ttu-id="aa01c-225">O código a seguir é a classe C# do Razor gerada:</span><span class="sxs-lookup"><span data-stu-id="aa01c-225">The following code is the generated Razor C# class:</span></span>

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="aa01c-226">Os métodos `@functions` servem como métodos de modelagem quando têm uma marcação:</span><span class="sxs-lookup"><span data-stu-id="aa01c-226">`@functions` methods serve as templating methods when they have markup:</span></span>

```cshtml
@{
    RenderName("Mahatma Gandhi");
    RenderName("Martin Luther King, Jr.");
}

@functions {
    private void RenderName(string name)
    {
        <p>Name: <strong>@name</strong></p>
    }
}
```

<span data-ttu-id="aa01c-227">O código renderiza o HTML a seguir:</span><span class="sxs-lookup"><span data-stu-id="aa01c-227">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

### <a name="implements"></a><span data-ttu-id="aa01c-228">\@implements</span><span class="sxs-lookup"><span data-stu-id="aa01c-228">\@implements</span></span>

<span data-ttu-id="aa01c-229">A diretiva `@implements` implementa uma interface para a classe gerada.</span><span class="sxs-lookup"><span data-stu-id="aa01c-229">The `@implements` directive implements an interface for the generated class.</span></span>

<span data-ttu-id="aa01c-230">O exemplo a seguir implementa <xref:System.IDisposable?displayProperty=fullName> para que o método <xref:System.IDisposable.Dispose*> possa ser chamado:</span><span class="sxs-lookup"><span data-stu-id="aa01c-230">The following example implements <xref:System.IDisposable?displayProperty=fullName> so that the <xref:System.IDisposable.Dispose*> method can be called:</span></span>

```cshtml
@implements IDisposable

<h1>Example</h1>

@functions {
    private bool _isDisposed;

    ...

    public void Dispose() => _isDisposed = true;
}
```

::: moniker-end

### <a name="inherits"></a><span data-ttu-id="aa01c-231">\@inherits</span><span class="sxs-lookup"><span data-stu-id="aa01c-231">\@inherits</span></span>

<span data-ttu-id="aa01c-232">A diretiva `@inherits` fornece controle total da classe que a exibição herda:</span><span class="sxs-lookup"><span data-stu-id="aa01c-232">The `@inherits` directive provides full control of the class the view inherits:</span></span>

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

<span data-ttu-id="aa01c-233">O código a seguir é um tipo de página Razor personalizado:</span><span class="sxs-lookup"><span data-stu-id="aa01c-233">The following code is a custom Razor page type:</span></span>

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

<span data-ttu-id="aa01c-234">O `CustomText` é exibido em uma exibição:</span><span class="sxs-lookup"><span data-stu-id="aa01c-234">The `CustomText` is displayed in a view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

<span data-ttu-id="aa01c-235">O código renderiza o HTML a seguir:</span><span class="sxs-lookup"><span data-stu-id="aa01c-235">The code renders the following HTML:</span></span>

```html
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

 <span data-ttu-id="aa01c-236">`@model` e `@inherits` podem ser usados na mesma exibição.</span><span class="sxs-lookup"><span data-stu-id="aa01c-236">`@model` and `@inherits` can be used in the same view.</span></span> <span data-ttu-id="aa01c-237">`@inherits` pode estar em um arquivo *_ViewImports.cshtml* que a exibição importa:</span><span class="sxs-lookup"><span data-stu-id="aa01c-237">`@inherits` can be in a *_ViewImports.cshtml* file that the view imports:</span></span>

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

<span data-ttu-id="aa01c-238">O código a seguir é um exemplo de exibição fortemente tipada:</span><span class="sxs-lookup"><span data-stu-id="aa01c-238">The following code is an example of a strongly-typed view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

<span data-ttu-id="aa01c-239">Se "rick@contoso.com" for passado no modelo, a exibição gerará a seguinte marcação HTML:</span><span class="sxs-lookup"><span data-stu-id="aa01c-239">If "rick@contoso.com" is passed in the model, the view generates the following HTML markup:</span></span>

```html
<div>The Login Email: rick@contoso.com</div>
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

### <a name="inject"></a><span data-ttu-id="aa01c-240">\@inject</span><span class="sxs-lookup"><span data-stu-id="aa01c-240">\@inject</span></span>

<span data-ttu-id="aa01c-241">A diretiva `@inject` permite que a página do Razor injete um serviço do [contêiner de serviço](xref:fundamentals/dependency-injection) em uma exibição.</span><span class="sxs-lookup"><span data-stu-id="aa01c-241">The `@inject` directive enables the Razor Page to inject a service from the [service container](xref:fundamentals/dependency-injection) into a view.</span></span> <span data-ttu-id="aa01c-242">Para obter mais informações, consulte [Injeção de dependência em exibições](xref:mvc/views/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="aa01c-242">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="layout"></a><span data-ttu-id="aa01c-243">\@layout</span><span class="sxs-lookup"><span data-stu-id="aa01c-243">\@layout</span></span>

<span data-ttu-id="aa01c-244">*Este cenário aplica-se somente a componentes do Razor (.razor).*</span><span class="sxs-lookup"><span data-stu-id="aa01c-244">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="aa01c-245">A diretiva `@layout` especifica um layout para um componente de Razor.</span><span class="sxs-lookup"><span data-stu-id="aa01c-245">The `@layout` directive specifies a layout for a Razor component.</span></span> <span data-ttu-id="aa01c-246">Os componentes de layout são usados para evitar casos de duplicação e inconsistência no código.</span><span class="sxs-lookup"><span data-stu-id="aa01c-246">Layout components are used to avoid code duplication and inconsistency.</span></span> <span data-ttu-id="aa01c-247">Para obter mais informações, consulte <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="aa01c-247">For more information, see <xref:blazor/layouts>.</span></span>

::: moniker-end

### <a name="model"></a><span data-ttu-id="aa01c-248">\@model</span><span class="sxs-lookup"><span data-stu-id="aa01c-248">\@model</span></span>

<span data-ttu-id="aa01c-249">*Este cenário aplica-se somente a exibições do MVC e do Razor Pages (.cshtml).*</span><span class="sxs-lookup"><span data-stu-id="aa01c-249">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="aa01c-250">A diretiva `@model` especifica o tipo do modelo passado para uma exibição ou página:</span><span class="sxs-lookup"><span data-stu-id="aa01c-250">The `@model` directive specifies the type of the model passed to a view or page:</span></span>

```cshtml
@model TypeNameOfModel
```

<span data-ttu-id="aa01c-251">Em um aplicativo de MVC ou Razor Pages do ASP.NET Core criado com contas de usuário individuais, *Views/Account/Login.cshtml* contém a declaração de modelo a seguir:</span><span class="sxs-lookup"><span data-stu-id="aa01c-251">In an ASP.NET Core MVC or Razor Pages app created with individual user accounts, *Views/Account/Login.cshtml* contains the following model declaration:</span></span>

```cshtml
@model LoginViewModel
```

<span data-ttu-id="aa01c-252">A classe gerada herda de `RazorPage<dynamic>`:</span><span class="sxs-lookup"><span data-stu-id="aa01c-252">The class generated inherits from `RazorPage<dynamic>`:</span></span>

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

<span data-ttu-id="aa01c-253">O Razor expõe uma propriedade `Model` para acessar o modelo passado para a exibição:</span><span class="sxs-lookup"><span data-stu-id="aa01c-253">Razor exposes a `Model` property for accessing the model passed to the view:</span></span>

```cshtml
<div>The Login Email: @Model.Email</div>
```

<span data-ttu-id="aa01c-254">A diretiva `@model` especifica o tipo da propriedade `Model`.</span><span class="sxs-lookup"><span data-stu-id="aa01c-254">The `@model` directive specifies the type of the `Model` property.</span></span> <span data-ttu-id="aa01c-255">A diretiva especifica o `T` em `RazorPage<T>` da classe gerada da qual a exibição deriva.</span><span class="sxs-lookup"><span data-stu-id="aa01c-255">The directive specifies the `T` in `RazorPage<T>` that the generated class that the view derives from.</span></span> <span data-ttu-id="aa01c-256">Se a diretiva `@model` não for especificada, a propriedade `Model` será do tipo `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="aa01c-256">If the `@model` directive isn't specified, the `Model` property is of type `dynamic`.</span></span> <span data-ttu-id="aa01c-257">Para obter mais informações, consulte [Modelos fortemente tipados e a palavra-chave @model](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span><span class="sxs-lookup"><span data-stu-id="aa01c-257">For more information, see [Strongly typed models and the @model keyword](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span></span>

### <a name="namespace"></a><span data-ttu-id="aa01c-258">\@namespace</span><span class="sxs-lookup"><span data-stu-id="aa01c-258">\@namespace</span></span>

<span data-ttu-id="aa01c-259">A diretiva `@namespace`:</span><span class="sxs-lookup"><span data-stu-id="aa01c-259">The `@namespace` directive:</span></span>

* <span data-ttu-id="aa01c-260">Define o namespace da classe da página do Razor gerada, da exibição de MVC ou do componente de Razor.</span><span class="sxs-lookup"><span data-stu-id="aa01c-260">Sets the namespace of the class of the generated Razor page, MVC view, or Razor component.</span></span>
* <span data-ttu-id="aa01c-261">Define os namespaces raiz derivados das classes de páginas, exibições ou componentes do arquivo de importações mais próximo na árvore de diretórios, *_ViewImports. cshtml* (exibições ou páginas) ou *_Imports.razor* (componentes de Razor).</span><span class="sxs-lookup"><span data-stu-id="aa01c-261">Sets the root derived namespaces of a pages, views, or components classes from the closest imports file in the directory tree, *_ViewImports.cshtml* (views or pages) or *_Imports.razor* (Razor components).</span></span>

```cshtml
@namespace Your.Namespace.Here
```

<span data-ttu-id="aa01c-262">Para o exemplo de Razor Pages mostrado na tabela a seguir:</span><span class="sxs-lookup"><span data-stu-id="aa01c-262">For the Razor Pages example shown in the following table:</span></span>

* <span data-ttu-id="aa01c-263">Cada página importa *Pages/_ViewImports.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="aa01c-263">Each page imports *Pages/_ViewImports.cshtml*.</span></span>
* <span data-ttu-id="aa01c-264">*Pages/_ViewImports.cshtml* contém `@namespace Hello.World`.</span><span class="sxs-lookup"><span data-stu-id="aa01c-264">*Pages/_ViewImports.cshtml* contains `@namespace Hello.World`.</span></span>
* <span data-ttu-id="aa01c-265">Cada página tem `Hello.World` como a raiz do namespace.</span><span class="sxs-lookup"><span data-stu-id="aa01c-265">Each page has `Hello.World` as the root of it's namespace.</span></span>

| <span data-ttu-id="aa01c-266">Página</span><span class="sxs-lookup"><span data-stu-id="aa01c-266">Page</span></span>                                        | <span data-ttu-id="aa01c-267">Namespace</span><span class="sxs-lookup"><span data-stu-id="aa01c-267">Namespace</span></span>                             |
| ------------------------------------------- | ------------------------------------- |
| <span data-ttu-id="aa01c-268">*Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="aa01c-268">*Pages/Index.cshtml*</span></span>                        | `Hello.World`                         |
| <span data-ttu-id="aa01c-269">*Pages/MorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="aa01c-269">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages`               |
| <span data-ttu-id="aa01c-270">*Pages/MorePages/EvenMorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="aa01c-270">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Hello.World.MorePages.EvenMorePages` |

<span data-ttu-id="aa01c-271">As relações anteriores se aplicam a arquivos de importação usados com exibições de MVC e componentes de Razor.</span><span class="sxs-lookup"><span data-stu-id="aa01c-271">The preceding relationships apply to import files used with MVC views and Razor components.</span></span>

<span data-ttu-id="aa01c-272">Quando vários arquivos de importação têm uma diretiva `@namespace`, o arquivo mais próximo da página, exibição ou componente na árvore de diretórios é usado para definir o namespace raiz.</span><span class="sxs-lookup"><span data-stu-id="aa01c-272">When multiple import files have a `@namespace` directive, the file closest to the page, view, or component in the directory tree is used to set the root namespace.</span></span>

<span data-ttu-id="aa01c-273">Se a pasta *EvenMorePages* no exemplo anterior tiver um arquivo de importações com `@namespace Another.Planet` (ou se o arquivo *Pages/MorePages/EvenMorePages/Page.cshtml* contiver `@namespace Another.Planet`), o resultado será mostrado na tabela a seguir.</span><span class="sxs-lookup"><span data-stu-id="aa01c-273">If the *EvenMorePages* folder in the preceding example has an imports file with `@namespace Another.Planet` (or the *Pages/MorePages/EvenMorePages/Page.cshtml* file contains `@namespace Another.Planet`), the result is shown in the following table.</span></span>

| <span data-ttu-id="aa01c-274">Página</span><span class="sxs-lookup"><span data-stu-id="aa01c-274">Page</span></span>                                        | <span data-ttu-id="aa01c-275">Namespace</span><span class="sxs-lookup"><span data-stu-id="aa01c-275">Namespace</span></span>               |
| ------------------------------------------- | ----------------------- |
| <span data-ttu-id="aa01c-276">*Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="aa01c-276">*Pages/Index.cshtml*</span></span>                        | `Hello.World`           |
| <span data-ttu-id="aa01c-277">*Pages/MorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="aa01c-277">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages` |
| <span data-ttu-id="aa01c-278">*Pages/MorePages/EvenMorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="aa01c-278">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Another.Planet`        |

### <a name="page"></a><span data-ttu-id="aa01c-279">\@page</span><span class="sxs-lookup"><span data-stu-id="aa01c-279">\@page</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="aa01c-280">A diretiva `@page` tem efeitos diferentes dependendo do tipo do arquivo em que aparece.</span><span class="sxs-lookup"><span data-stu-id="aa01c-280">The `@page` directive has different effects depending on the type of the file where it appears.</span></span> <span data-ttu-id="aa01c-281">A diretiva:</span><span class="sxs-lookup"><span data-stu-id="aa01c-281">The directive:</span></span>

* <span data-ttu-id="aa01c-282">Em um arquivo *.cshtml*, indica que o arquivo é uma página do Razor.</span><span class="sxs-lookup"><span data-stu-id="aa01c-282">In in a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="aa01c-283">Para obter mais informações, consulte [rotas personalizadas](xref:razor-pages/index#custom-routes) e <xref:razor-pages/index>.</span><span class="sxs-lookup"><span data-stu-id="aa01c-283">For more information, see [Custom routes](xref:razor-pages/index#custom-routes) and <xref:razor-pages/index>.</span></span>
* <span data-ttu-id="aa01c-284">Especifica que um componente de Razor deve tratar as solicitações diretamente.</span><span class="sxs-lookup"><span data-stu-id="aa01c-284">Specifies that a Razor component should handle requests directly.</span></span> <span data-ttu-id="aa01c-285">Para obter mais informações, consulte <xref:blazor/routing>.</span><span class="sxs-lookup"><span data-stu-id="aa01c-285">For more information, see <xref:blazor/routing>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="aa01c-286">A diretiva `@page` na primeira linha de um arquivo *.cshtml* indica que o arquivo é uma página do Razor.</span><span class="sxs-lookup"><span data-stu-id="aa01c-286">The `@page` directive on the first line of a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="aa01c-287">Para obter mais informações, consulte <xref:razor-pages/index>.</span><span class="sxs-lookup"><span data-stu-id="aa01c-287">For more information, see <xref:razor-pages/index>.</span></span>

::: moniker-end

### <a name="section"></a><span data-ttu-id="aa01c-288">\@section</span><span class="sxs-lookup"><span data-stu-id="aa01c-288">\@section</span></span>

<span data-ttu-id="aa01c-289">*Este cenário aplica-se somente a exibições do MVC e do Razor Pages (.cshtml).*</span><span class="sxs-lookup"><span data-stu-id="aa01c-289">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="aa01c-290">A diretiva `@section` é usada em conjunto com [layouts de MVC e Razor Pages](xref:mvc/views/layout) para permitir que as exibições ou páginas renderizem conteúdo em partes diferentes da página HTML.</span><span class="sxs-lookup"><span data-stu-id="aa01c-290">The `@section` directive is used in conjunction with [MVC and Razor Pages layouts](xref:mvc/views/layout) to enable views or pages to render content in different parts of the HTML page.</span></span> <span data-ttu-id="aa01c-291">Para obter mais informações, consulte <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="aa01c-291">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="using"></a><span data-ttu-id="aa01c-292">\@using</span><span class="sxs-lookup"><span data-stu-id="aa01c-292">\@using</span></span>

<span data-ttu-id="aa01c-293">A diretiva `@using` adiciona a diretiva `using` de C# à exibição gerada:</span><span class="sxs-lookup"><span data-stu-id="aa01c-293">The `@using` directive adds the C# `using` directive to the generated view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="aa01c-294">Nos [componentes de Razor](xref:blazor/components), `@using` também controla quais componentes estão em um escopo.</span><span class="sxs-lookup"><span data-stu-id="aa01c-294">In [Razor components](xref:blazor/components), `@using` also controls which components are in scope.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="directive-attributes"></a><span data-ttu-id="aa01c-295">Atributos de diretiva</span><span class="sxs-lookup"><span data-stu-id="aa01c-295">Directive attributes</span></span>

### <a name="attributes"></a><span data-ttu-id="aa01c-296">\@attributes</span><span class="sxs-lookup"><span data-stu-id="aa01c-296">\@attributes</span></span>

<span data-ttu-id="aa01c-297">*Este cenário aplica-se somente a componentes do Razor (.razor).*</span><span class="sxs-lookup"><span data-stu-id="aa01c-297">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="aa01c-298">`@attributes` permite que um componente renderize atributos não declarados.</span><span class="sxs-lookup"><span data-stu-id="aa01c-298">`@attributes` allows a component to render non-declared attributes.</span></span> <span data-ttu-id="aa01c-299">Para obter mais informações, consulte <xref:blazor/components#attribute-splatting-and-arbitrary-parameters>.</span><span class="sxs-lookup"><span data-stu-id="aa01c-299">For more information, see <xref:blazor/components#attribute-splatting-and-arbitrary-parameters>.</span></span>

### <a name="bind"></a><span data-ttu-id="aa01c-300">\@bind</span><span class="sxs-lookup"><span data-stu-id="aa01c-300">\@bind</span></span>

<span data-ttu-id="aa01c-301">*Este cenário aplica-se somente a componentes do Razor (.razor).*</span><span class="sxs-lookup"><span data-stu-id="aa01c-301">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="aa01c-302">A vinculação de dados nos componentes é realizada com o atributo `@bind`.</span><span class="sxs-lookup"><span data-stu-id="aa01c-302">Data binding in components is accomplished with the `@bind` attribute.</span></span> <span data-ttu-id="aa01c-303">Para obter mais informações, consulte <xref:blazor/components#data-binding>.</span><span class="sxs-lookup"><span data-stu-id="aa01c-303">For more information, see <xref:blazor/components#data-binding>.</span></span>

### <a name="onevent"></a><span data-ttu-id="aa01c-304">\@no {EVENT}</span><span class="sxs-lookup"><span data-stu-id="aa01c-304">\@on{EVENT}</span></span>

<span data-ttu-id="aa01c-305">*Este cenário aplica-se somente a componentes do Razor (.razor).*</span><span class="sxs-lookup"><span data-stu-id="aa01c-305">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="aa01c-306">O Razor fornece recursos de manipulação de eventos para os componentes.</span><span class="sxs-lookup"><span data-stu-id="aa01c-306">Razor provides event handling features for components.</span></span> <span data-ttu-id="aa01c-307">Para obter mais informações, consulte <xref:blazor/components#event-handling>.</span><span class="sxs-lookup"><span data-stu-id="aa01c-307">For more information, see <xref:blazor/components#event-handling>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.1"

### <a name="oneventpreventdefault"></a><span data-ttu-id="aa01c-308">\@em {EVENT}:p reventDefault</span><span class="sxs-lookup"><span data-stu-id="aa01c-308">\@on{EVENT}:preventDefault</span></span>

<span data-ttu-id="aa01c-309">*Este cenário aplica-se somente a componentes do Razor (.razor).*</span><span class="sxs-lookup"><span data-stu-id="aa01c-309">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="aa01c-310">Impede a ação padrão para o evento.</span><span class="sxs-lookup"><span data-stu-id="aa01c-310">Prevents the default action for the event.</span></span>

### <a name="oneventstoppropagation"></a><span data-ttu-id="aa01c-311">\@em {EVENT}: stopPropagation</span><span class="sxs-lookup"><span data-stu-id="aa01c-311">\@on{EVENT}:stopPropagation</span></span>

<span data-ttu-id="aa01c-312">*Este cenário aplica-se somente a componentes do Razor (.razor).*</span><span class="sxs-lookup"><span data-stu-id="aa01c-312">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="aa01c-313">Interrompe a propagação do evento.</span><span class="sxs-lookup"><span data-stu-id="aa01c-313">Stops event propagation for the event.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="key"></a><span data-ttu-id="aa01c-314">\@key</span><span class="sxs-lookup"><span data-stu-id="aa01c-314">\@key</span></span>

<span data-ttu-id="aa01c-315">*Este cenário aplica-se somente a componentes do Razor (.razor).*</span><span class="sxs-lookup"><span data-stu-id="aa01c-315">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="aa01c-316">O atributo da diretiva `@key` faz com que os componentes comparem o algoritmo para garantir a preservação de elementos ou componentes com base no valor da chave.</span><span class="sxs-lookup"><span data-stu-id="aa01c-316">The `@key` directive attribute causes the components diffing algorithm to guarantee preservation of elements or components based on the key's value.</span></span> <span data-ttu-id="aa01c-317">Para obter mais informações, consulte <xref:blazor/components#use-key-to-control-the-preservation-of-elements-and-components>.</span><span class="sxs-lookup"><span data-stu-id="aa01c-317">For more information, see <xref:blazor/components#use-key-to-control-the-preservation-of-elements-and-components>.</span></span>

### <a name="ref"></a><span data-ttu-id="aa01c-318">\@ref</span><span class="sxs-lookup"><span data-stu-id="aa01c-318">\@ref</span></span>

<span data-ttu-id="aa01c-319">*Este cenário aplica-se somente a componentes do Razor (.razor).*</span><span class="sxs-lookup"><span data-stu-id="aa01c-319">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="aa01c-320">Referências de componente (`@ref`) proporcionam uma maneira de fazer referência a uma instância de componente para que você possa emitir comandos para essa instância.</span><span class="sxs-lookup"><span data-stu-id="aa01c-320">Component references (`@ref`) provide a way to reference a component instance so that you can issue commands to that instance.</span></span> <span data-ttu-id="aa01c-321">Para obter mais informações, consulte <xref:blazor/components#capture-references-to-components>.</span><span class="sxs-lookup"><span data-stu-id="aa01c-321">For more information, see <xref:blazor/components#capture-references-to-components>.</span></span>

### <a name="typeparam"></a><span data-ttu-id="aa01c-322">\@typeparam</span><span class="sxs-lookup"><span data-stu-id="aa01c-322">\@typeparam</span></span>

<span data-ttu-id="aa01c-323">*Este cenário aplica-se somente a componentes do Razor (.razor).*</span><span class="sxs-lookup"><span data-stu-id="aa01c-323">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="aa01c-324">A diretiva `@typeparam` declara um parâmetro de tipo genérico para a classe de componente gerada.</span><span class="sxs-lookup"><span data-stu-id="aa01c-324">The `@typeparam` directive declares a generic type parameter for the generated component class.</span></span> <span data-ttu-id="aa01c-325">Para obter mais informações, consulte <xref:blazor/components#generic-typed-components>.</span><span class="sxs-lookup"><span data-stu-id="aa01c-325">For more information, see <xref:blazor/components#generic-typed-components>.</span></span>

::: moniker-end

## <a name="templated-razor-delegates"></a><span data-ttu-id="aa01c-326">Representantes do Razor com modelo</span><span class="sxs-lookup"><span data-stu-id="aa01c-326">Templated Razor delegates</span></span>

<span data-ttu-id="aa01c-327">Os modelos Razor permitem que você defina um snippet da interface do usuário com o seguinte formato:</span><span class="sxs-lookup"><span data-stu-id="aa01c-327">Razor templates allow you to define a UI snippet with the following format:</span></span>

```cshtml
@<tag>...</tag>
```

<span data-ttu-id="aa01c-328">O exemplo a seguir ilustra como especificar um delegado do Razor com modelo como um <xref:System.Func%602>.</span><span class="sxs-lookup"><span data-stu-id="aa01c-328">The following example illustrates how to specify a templated Razor delegate as a <xref:System.Func%602>.</span></span> <span data-ttu-id="aa01c-329">O [tipo dinâmico](/dotnet/csharp/programming-guide/types/using-type-dynamic) é especificado para o parâmetro do método encapsulado pelo delegado.</span><span class="sxs-lookup"><span data-stu-id="aa01c-329">The [dynamic type](/dotnet/csharp/programming-guide/types/using-type-dynamic) is specified for the parameter of the method that the delegate encapsulates.</span></span> <span data-ttu-id="aa01c-330">Um [tipo de objeto](/dotnet/csharp/language-reference/keywords/object) é especificado como o valor retornado do delegado.</span><span class="sxs-lookup"><span data-stu-id="aa01c-330">An [object type](/dotnet/csharp/language-reference/keywords/object) is specified as the return value of the delegate.</span></span> <span data-ttu-id="aa01c-331">O modelo é usado com uma <xref:System.Collections.Generic.List%601> de `Pet` que tem uma propriedade `Name`.</span><span class="sxs-lookup"><span data-stu-id="aa01c-331">The template is used with a <xref:System.Collections.Generic.List%601> of `Pet` that has a `Name` property.</span></span>

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

<span data-ttu-id="aa01c-332">O modelo é renderizado com `pets` fornecido por uma instrução `foreach`:</span><span class="sxs-lookup"><span data-stu-id="aa01c-332">The template is rendered with `pets` supplied by a `foreach` statement:</span></span>

```cshtml
@foreach (var pet in pets)
{
    @petTemplate(pet)
}
```

<span data-ttu-id="aa01c-333">Saída renderizada:</span><span class="sxs-lookup"><span data-stu-id="aa01c-333">Rendered output:</span></span>

```html
<p>You have a pet named <strong>Rin Tin Tin</strong>.</p>
<p>You have a pet named <strong>Mr. Bigglesworth</strong>.</p>
<p>You have a pet named <strong>K-9</strong>.</p>
```

<span data-ttu-id="aa01c-334">Você também pode fornecer um modelo Razor embutido como um argumento para um método.</span><span class="sxs-lookup"><span data-stu-id="aa01c-334">You can also supply an inline Razor template as an argument to a method.</span></span> <span data-ttu-id="aa01c-335">No exemplo a seguir, o método `Repeat` recebe um modelo Razor.</span><span class="sxs-lookup"><span data-stu-id="aa01c-335">In the following example, the `Repeat` method receives a Razor template.</span></span> <span data-ttu-id="aa01c-336">O método usa o modelo para produzir o conteúdo HTML com repetições de itens fornecidos em uma lista:</span><span class="sxs-lookup"><span data-stu-id="aa01c-336">The method uses the template to produce HTML content with repeats of items supplied from a list:</span></span>

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

<span data-ttu-id="aa01c-337">Usando a lista de animais de estimação do exemplo anterior, o método `Repeat` é chamado com:</span><span class="sxs-lookup"><span data-stu-id="aa01c-337">Using the list of pets from the prior example, the `Repeat` method is called with:</span></span>

* <span data-ttu-id="aa01c-338"><xref:System.Collections.Generic.List%601> de `Pet`.</span><span class="sxs-lookup"><span data-stu-id="aa01c-338"><xref:System.Collections.Generic.List%601> of `Pet`.</span></span>
* <span data-ttu-id="aa01c-339">Número de vezes que deve ser repetido cada animal de estimação.</span><span class="sxs-lookup"><span data-stu-id="aa01c-339">Number of times to repeat each pet.</span></span>
* <span data-ttu-id="aa01c-340">Modelo embutido a ser usado para os itens da lista de uma lista não ordenada.</span><span class="sxs-lookup"><span data-stu-id="aa01c-340">Inline template to use for the list items of an unordered list.</span></span>

```cshtml
<ul>
    @Repeat(pets, 3, @<li>@item.Name</li>)
</ul>
```

<span data-ttu-id="aa01c-341">Saída renderizada:</span><span class="sxs-lookup"><span data-stu-id="aa01c-341">Rendered output:</span></span>

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

## <a name="tag-helpers"></a><span data-ttu-id="aa01c-342">Auxiliares de Marca</span><span class="sxs-lookup"><span data-stu-id="aa01c-342">Tag Helpers</span></span>

<span data-ttu-id="aa01c-343">*Este cenário aplica-se somente a exibições do MVC e do Razor Pages (.cshtml).*</span><span class="sxs-lookup"><span data-stu-id="aa01c-343">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="aa01c-344">Há três diretivas que relacionadas aos [Auxiliares de marca](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="aa01c-344">There are three directives that pertain to [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

| <span data-ttu-id="aa01c-345">Directive</span><span class="sxs-lookup"><span data-stu-id="aa01c-345">Directive</span></span> | <span data-ttu-id="aa01c-346">Função</span><span class="sxs-lookup"><span data-stu-id="aa01c-346">Function</span></span> |
| --------- | -------- |
| [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#add-helper-label) | <span data-ttu-id="aa01c-347">Disponibiliza os Auxiliares de marca para uma exibição.</span><span class="sxs-lookup"><span data-stu-id="aa01c-347">Makes Tag Helpers available to a view.</span></span> |
| [`@removeTagHelper`](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | <span data-ttu-id="aa01c-348">Remove os Auxiliares de marca adicionados anteriormente de uma exibição.</span><span class="sxs-lookup"><span data-stu-id="aa01c-348">Removes Tag Helpers previously added from a view.</span></span> |
| [`@tagHelperPrefix`](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | <span data-ttu-id="aa01c-349">Especifica um prefixo de marca para habilitar o suporte do Auxiliar de marca e tornar explícito o uso do Auxiliar de marca.</span><span class="sxs-lookup"><span data-stu-id="aa01c-349">Specifies a tag prefix to enable Tag Helper support and to make Tag Helper usage explicit.</span></span> |

## <a name="razor-reserved-keywords"></a><span data-ttu-id="aa01c-350">Palavras-chave reservadas ao Razor</span><span class="sxs-lookup"><span data-stu-id="aa01c-350">Razor reserved keywords</span></span>

### <a name="razor-keywords"></a><span data-ttu-id="aa01c-351">Palavras-chave do Razor</span><span class="sxs-lookup"><span data-stu-id="aa01c-351">Razor keywords</span></span>

* <span data-ttu-id="aa01c-352">page (exige o ASP.NET Core 2.1 ou posterior)</span><span class="sxs-lookup"><span data-stu-id="aa01c-352">page (Requires ASP.NET Core 2.1 or later)</span></span>
* <span data-ttu-id="aa01c-353">Namespace</span><span class="sxs-lookup"><span data-stu-id="aa01c-353">namespace</span></span>
* <span data-ttu-id="aa01c-354">funções</span><span class="sxs-lookup"><span data-stu-id="aa01c-354">functions</span></span>
* <span data-ttu-id="aa01c-355">herda</span><span class="sxs-lookup"><span data-stu-id="aa01c-355">inherits</span></span>
* <span data-ttu-id="aa01c-356">{1&gt;modelo&lt;1}</span><span class="sxs-lookup"><span data-stu-id="aa01c-356">model</span></span>
* <span data-ttu-id="aa01c-357">section</span><span class="sxs-lookup"><span data-stu-id="aa01c-357">section</span></span>
* <span data-ttu-id="aa01c-358">helper (atualmente sem suporte do ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="aa01c-358">helper (Not currently supported by ASP.NET Core)</span></span>

<span data-ttu-id="aa01c-359">Palavras-chave do Razor têm o escape feito com `@(Razor Keyword)` (por exemplo, `@(functions)`).</span><span class="sxs-lookup"><span data-stu-id="aa01c-359">Razor keywords are escaped with `@(Razor Keyword)` (for example, `@(functions)`).</span></span>

### <a name="c-razor-keywords"></a><span data-ttu-id="aa01c-360">Palavras-chave do Razor em C#</span><span class="sxs-lookup"><span data-stu-id="aa01c-360">C# Razor keywords</span></span>

* <span data-ttu-id="aa01c-361">case</span><span class="sxs-lookup"><span data-stu-id="aa01c-361">case</span></span>
* <span data-ttu-id="aa01c-362">do</span><span class="sxs-lookup"><span data-stu-id="aa01c-362">do</span></span>
* <span data-ttu-id="aa01c-363">{1&gt;default&lt;1}</span><span class="sxs-lookup"><span data-stu-id="aa01c-363">default</span></span>
* <span data-ttu-id="aa01c-364">para</span><span class="sxs-lookup"><span data-stu-id="aa01c-364">for</span></span>
* <span data-ttu-id="aa01c-365">foreach</span><span class="sxs-lookup"><span data-stu-id="aa01c-365">foreach</span></span>
* <span data-ttu-id="aa01c-366">if</span><span class="sxs-lookup"><span data-stu-id="aa01c-366">if</span></span>
* <span data-ttu-id="aa01c-367">else</span><span class="sxs-lookup"><span data-stu-id="aa01c-367">else</span></span>
* <span data-ttu-id="aa01c-368">bloqueio</span><span class="sxs-lookup"><span data-stu-id="aa01c-368">lock</span></span>
* <span data-ttu-id="aa01c-369">switch</span><span class="sxs-lookup"><span data-stu-id="aa01c-369">switch</span></span>
* <span data-ttu-id="aa01c-370">try</span><span class="sxs-lookup"><span data-stu-id="aa01c-370">try</span></span>
* <span data-ttu-id="aa01c-371">catch</span><span class="sxs-lookup"><span data-stu-id="aa01c-371">catch</span></span>
* <span data-ttu-id="aa01c-372">finally</span><span class="sxs-lookup"><span data-stu-id="aa01c-372">finally</span></span>
* <span data-ttu-id="aa01c-373">usando</span><span class="sxs-lookup"><span data-stu-id="aa01c-373">using</span></span>
* <span data-ttu-id="aa01c-374">while</span><span class="sxs-lookup"><span data-stu-id="aa01c-374">while</span></span>

<span data-ttu-id="aa01c-375">Palavras-chave do Razor em C# precisam ter o escape duplo com `@(@C# Razor Keyword)` (por exemplo, `@(@case)`).</span><span class="sxs-lookup"><span data-stu-id="aa01c-375">C# Razor keywords must be double-escaped with `@(@C# Razor Keyword)` (for example, `@(@case)`).</span></span> <span data-ttu-id="aa01c-376">O primeiro `@` faz o escape do analisador Razor.</span><span class="sxs-lookup"><span data-stu-id="aa01c-376">The first `@` escapes the Razor parser.</span></span> <span data-ttu-id="aa01c-377">O segundo `@` faz o escape do analisador C#.</span><span class="sxs-lookup"><span data-stu-id="aa01c-377">The second `@` escapes the C# parser.</span></span>

### <a name="reserved-keywords-not-used-by-razor"></a><span data-ttu-id="aa01c-378">Palavras-chave reservadas não usadas pelo Razor</span><span class="sxs-lookup"><span data-stu-id="aa01c-378">Reserved keywords not used by Razor</span></span>

* <span data-ttu-id="aa01c-379">{1&gt;classe&lt;1}</span><span class="sxs-lookup"><span data-stu-id="aa01c-379">class</span></span>

## <a name="inspect-the-razor-c-class-generated-for-a-view"></a><span data-ttu-id="aa01c-380">Inspecionar a classe do Razor C# gerada para uma exibição</span><span class="sxs-lookup"><span data-stu-id="aa01c-380">Inspect the Razor C# class generated for a view</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="aa01c-381">Com o SDK do .NET Core 2.1 ou posterior, o [SDK do Razor](xref:razor-pages/sdk) lida com a compilação de arquivos do Razor.</span><span class="sxs-lookup"><span data-stu-id="aa01c-381">With .NET Core SDK 2.1 or later, the [Razor SDK](xref:razor-pages/sdk) handles compilation of Razor files.</span></span> <span data-ttu-id="aa01c-382">Ao compilar um projeto, o SDK do Razor gera um diretório *obj/<configuração_de_build>/<moniker_da_estrutura_de_destino>/Razor* na raiz do projeto.</span><span class="sxs-lookup"><span data-stu-id="aa01c-382">When building a project, the Razor SDK generates an *obj/<build_configuration>/<target_framework_moniker>/Razor* directory in the project root.</span></span> <span data-ttu-id="aa01c-383">A estrutura de diretórios dentro do diretório do *Razor* espelha a estrutura de diretórios do projeto.</span><span class="sxs-lookup"><span data-stu-id="aa01c-383">The directory structure within the *Razor* directory mirrors the project's directory structure.</span></span>

<span data-ttu-id="aa01c-384">Considere a seguinte estrutura de diretórios em um projeto do Razor Pages ASP.NET Core 2.1 direcionado ao .NET Core 2.1:</span><span class="sxs-lookup"><span data-stu-id="aa01c-384">Consider the following directory structure in an ASP.NET Core 2.1 Razor Pages project targeting .NET Core 2.1:</span></span>

* <span data-ttu-id="aa01c-385">**Areas/**</span><span class="sxs-lookup"><span data-stu-id="aa01c-385">**Areas/**</span></span>
  * <span data-ttu-id="aa01c-386">**Admin/**</span><span class="sxs-lookup"><span data-stu-id="aa01c-386">**Admin/**</span></span>
    * <span data-ttu-id="aa01c-387">**Pages/**</span><span class="sxs-lookup"><span data-stu-id="aa01c-387">**Pages/**</span></span>
      * <span data-ttu-id="aa01c-388">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="aa01c-388">*Index.cshtml*</span></span>
      * <span data-ttu-id="aa01c-389">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="aa01c-389">*Index.cshtml.cs*</span></span>
* <span data-ttu-id="aa01c-390">**Pages/**</span><span class="sxs-lookup"><span data-stu-id="aa01c-390">**Pages/**</span></span>
  * <span data-ttu-id="aa01c-391">**Shared/**</span><span class="sxs-lookup"><span data-stu-id="aa01c-391">**Shared/**</span></span>
    * <span data-ttu-id="aa01c-392">*_Layout.cshtml*</span><span class="sxs-lookup"><span data-stu-id="aa01c-392">*_Layout.cshtml*</span></span>
  * <span data-ttu-id="aa01c-393">*_ViewImports.cshtml*</span><span class="sxs-lookup"><span data-stu-id="aa01c-393">*_ViewImports.cshtml*</span></span>
  * <span data-ttu-id="aa01c-394">*_ViewStart.cshtml*</span><span class="sxs-lookup"><span data-stu-id="aa01c-394">*_ViewStart.cshtml*</span></span>
  * <span data-ttu-id="aa01c-395">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="aa01c-395">*Index.cshtml*</span></span>
  * <span data-ttu-id="aa01c-396">*Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="aa01c-396">*Index.cshtml.cs*</span></span>

<span data-ttu-id="aa01c-397">A criação do projeto na configuração de *Depuração* produz o seguinte diretório *obj*:</span><span class="sxs-lookup"><span data-stu-id="aa01c-397">Building the project in *Debug* configuration yields the following *obj* directory:</span></span>

* <span data-ttu-id="aa01c-398">**obj/**</span><span class="sxs-lookup"><span data-stu-id="aa01c-398">**obj/**</span></span>
  * <span data-ttu-id="aa01c-399">**Debug/**</span><span class="sxs-lookup"><span data-stu-id="aa01c-399">**Debug/**</span></span>
    * <span data-ttu-id="aa01c-400">**netcoreapp2.1/**</span><span class="sxs-lookup"><span data-stu-id="aa01c-400">**netcoreapp2.1/**</span></span>
      * <span data-ttu-id="aa01c-401">**Razor/**</span><span class="sxs-lookup"><span data-stu-id="aa01c-401">**Razor/**</span></span>
        * <span data-ttu-id="aa01c-402">**Areas/**</span><span class="sxs-lookup"><span data-stu-id="aa01c-402">**Areas/**</span></span>
          * <span data-ttu-id="aa01c-403">**Admin/**</span><span class="sxs-lookup"><span data-stu-id="aa01c-403">**Admin/**</span></span>
            * <span data-ttu-id="aa01c-404">**Pages/**</span><span class="sxs-lookup"><span data-stu-id="aa01c-404">**Pages/**</span></span>
              * <span data-ttu-id="aa01c-405">*Index.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="aa01c-405">*Index.g.cshtml.cs*</span></span>
        * <span data-ttu-id="aa01c-406">**Pages/**</span><span class="sxs-lookup"><span data-stu-id="aa01c-406">**Pages/**</span></span>
          * <span data-ttu-id="aa01c-407">**Shared/**</span><span class="sxs-lookup"><span data-stu-id="aa01c-407">**Shared/**</span></span>
            * <span data-ttu-id="aa01c-408">*_Layout.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="aa01c-408">*_Layout.g.cshtml.cs*</span></span>
          * <span data-ttu-id="aa01c-409">*_ViewImports.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="aa01c-409">*_ViewImports.g.cshtml.cs*</span></span>
          * <span data-ttu-id="aa01c-410">*_ViewStart.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="aa01c-410">*_ViewStart.g.cshtml.cs*</span></span>
          * <span data-ttu-id="aa01c-411">*Index.g.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="aa01c-411">*Index.g.cshtml.cs*</span></span>

<span data-ttu-id="aa01c-412">Para exibir a classe gerada para *Pages/Index.cshtml*, abra *obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="aa01c-412">To view the generated class for *Pages/Index.cshtml*, open *obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs*.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="aa01c-413">Adicione a seguinte classe ao projeto do ASP.NET Core MVC:</span><span class="sxs-lookup"><span data-stu-id="aa01c-413">Add the following class to the ASP.NET Core MVC project:</span></span>

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

<span data-ttu-id="aa01c-414">Em `Startup.ConfigureServices`, substitua o `RazorTemplateEngine` adicionado pelo MVC pela classe `CustomTemplateEngine`:</span><span class="sxs-lookup"><span data-stu-id="aa01c-414">In `Startup.ConfigureServices`, override the `RazorTemplateEngine` added by MVC with the `CustomTemplateEngine` class:</span></span>

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

<span data-ttu-id="aa01c-415">Defina o ponto de interrupção `CustomTemplateEngine` na instrução `return csharpDocument;`.</span><span class="sxs-lookup"><span data-stu-id="aa01c-415">Set a breakpoint on the `return csharpDocument;` statement of `CustomTemplateEngine`.</span></span> <span data-ttu-id="aa01c-416">Quando a execução do programa for interrompida no ponto de interrupção, veja o valor de `generatedCode`.</span><span class="sxs-lookup"><span data-stu-id="aa01c-416">When program execution stops at the breakpoint, view the value of `generatedCode`.</span></span>

![Exibição do Visualizador de Texto de generatedCode](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a><span data-ttu-id="aa01c-418">Pesquisas de exibição e diferenciação de maiúsculas e minúsculas</span><span class="sxs-lookup"><span data-stu-id="aa01c-418">View lookups and case sensitivity</span></span>

<span data-ttu-id="aa01c-419">O mecanismo de exibição do Razor executa pesquisas que diferenciam maiúsculas de minúsculas para as exibições.</span><span class="sxs-lookup"><span data-stu-id="aa01c-419">The Razor view engine performs case-sensitive lookups for views.</span></span> <span data-ttu-id="aa01c-420">No entanto, a pesquisa real é determinada pelo sistema de arquivos subjacente:</span><span class="sxs-lookup"><span data-stu-id="aa01c-420">However, the actual lookup is determined by the underlying file system:</span></span>

* <span data-ttu-id="aa01c-421">Origem baseada em arquivo:</span><span class="sxs-lookup"><span data-stu-id="aa01c-421">File based source:</span></span>
  * <span data-ttu-id="aa01c-422">Em sistemas operacionais com sistemas de arquivos que não diferenciam maiúsculas e minúsculas (por exemplo, Windows), pesquisas no provedor de arquivos físico não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="aa01c-422">On operating systems with case insensitive file systems (for example, Windows), physical file provider lookups are case insensitive.</span></span> <span data-ttu-id="aa01c-423">Por exemplo, `return View("Test")` resulta em correspondências para */Views/Home/Test.cshtml*, */Views/home/test.cshtml* e qualquer outra variação de maiúsculas e minúsculas.</span><span class="sxs-lookup"><span data-stu-id="aa01c-423">For example, `return View("Test")` results in matches for */Views/Home/Test.cshtml*, */Views/home/test.cshtml*, and any other casing variant.</span></span>
  * <span data-ttu-id="aa01c-424">Em sistemas de arquivos que diferenciam maiúsculas de minúsculas (por exemplo, Linux, OSX e com `EmbeddedFileProvider`), as pesquisas diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="aa01c-424">On case-sensitive file systems (for example, Linux, OSX, and with `EmbeddedFileProvider`), lookups are case-sensitive.</span></span> <span data-ttu-id="aa01c-425">Por exemplo, `return View("Test")` corresponde especificamente a */Views/Home/Test.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="aa01c-425">For example, `return View("Test")` specifically matches */Views/Home/Test.cshtml*.</span></span>
* <span data-ttu-id="aa01c-426">Exibições pré-compiladas: com o ASP.NET Core 2.0 e posteriores, pesquisar em exibições pré-compiladas não diferencia maiúsculas de minúsculas em nenhum sistema operacional.</span><span class="sxs-lookup"><span data-stu-id="aa01c-426">Precompiled views: With ASP.NET Core 2.0 and later, looking up precompiled views is case insensitive on all operating systems.</span></span> <span data-ttu-id="aa01c-427">O comportamento é idêntico ao comportamento do provedor de arquivos físico no Windows.</span><span class="sxs-lookup"><span data-stu-id="aa01c-427">The behavior is identical to physical file provider's behavior on Windows.</span></span> <span data-ttu-id="aa01c-428">Se duas exibições pré-compiladas diferirem apenas quanto ao padrão de maiúsculas e minúsculas, o resultado da pesquisa não será determinístico.</span><span class="sxs-lookup"><span data-stu-id="aa01c-428">If two precompiled views differ only in case, the result of lookup is non-deterministic.</span></span>

<span data-ttu-id="aa01c-429">Os desenvolvedores são incentivados a fazer a correspondência entre as maiúsculas e minúsculas dos nomes dos arquivos e de diretórios com o uso de maiúsculas e minúsculas em:</span><span class="sxs-lookup"><span data-stu-id="aa01c-429">Developers are encouraged to match the casing of file and directory names to the casing of:</span></span>

* <span data-ttu-id="aa01c-430">Nomes de área, controlador e ação.</span><span class="sxs-lookup"><span data-stu-id="aa01c-430">Area, controller, and action names.</span></span>
* <span data-ttu-id="aa01c-431">Páginas do Razor.</span><span class="sxs-lookup"><span data-stu-id="aa01c-431">Razor Pages.</span></span>

<span data-ttu-id="aa01c-432">Fazer essa correspondência garante que as implantações encontrem suas exibições, independentemente do sistema de arquivos subjacente.</span><span class="sxs-lookup"><span data-stu-id="aa01c-432">Matching case ensures the deployments find their views regardless of the underlying file system.</span></span>
