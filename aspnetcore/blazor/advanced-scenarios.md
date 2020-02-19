---
title: ASP.NET Core Blazor cenários avançados
author: guardrex
description: Saiba mais sobre cenários avançados no Blazor, incluindo como incorporar a lógica de RenderTreeBuilder manual em um aplicativo.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/advanced-scenarios
ms.openlocfilehash: 5e0618faa7b1b5e4cc15e30d9c16afaf7ccabaf0
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77453257"
---
# <a name="aspnet-core-blazor-advanced-scenarios"></a><span data-ttu-id="b9079-103">Cenários avançados de ASP.NET Core mais experientes</span><span class="sxs-lookup"><span data-stu-id="b9079-103">ASP.NET Core Blazor advanced scenarios</span></span>

<span data-ttu-id="b9079-104">De [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="b9079-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="manual-rendertreebuilder-logic"></a><span data-ttu-id="b9079-105">Lógica RenderTreeBuilder manual</span><span class="sxs-lookup"><span data-stu-id="b9079-105">Manual RenderTreeBuilder logic</span></span>

<span data-ttu-id="b9079-106">`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` fornece métodos para manipular componentes e elementos, incluindo a criação manual de componentes C# no código.</span><span class="sxs-lookup"><span data-stu-id="b9079-106">`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` provides methods for manipulating components and elements, including building components manually in C# code.</span></span>

> [!NOTE]
> <span data-ttu-id="b9079-107">O uso de `RenderTreeBuilder` para criar componentes é um cenário avançado.</span><span class="sxs-lookup"><span data-stu-id="b9079-107">Use of `RenderTreeBuilder` to create components is an advanced scenario.</span></span> <span data-ttu-id="b9079-108">Um componente malformado (por exemplo, uma marca de marcação não fechada) pode resultar em um comportamento indefinido.</span><span class="sxs-lookup"><span data-stu-id="b9079-108">A malformed component (for example, an unclosed markup tag) can result in undefined behavior.</span></span>

<span data-ttu-id="b9079-109">Considere o seguinte componente de `PetDetails`, que pode ser compilado manualmente em outro componente:</span><span class="sxs-lookup"><span data-stu-id="b9079-109">Consider the following `PetDetails` component, which can be manually built into another component:</span></span>

```razor
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

<span data-ttu-id="b9079-110">No exemplo a seguir, o loop no método `CreateComponent` gera três componentes `PetDetails`.</span><span class="sxs-lookup"><span data-stu-id="b9079-110">In the following example, the loop in the `CreateComponent` method generates three `PetDetails` components.</span></span> <span data-ttu-id="b9079-111">Ao chamar `RenderTreeBuilder` métodos para criar os componentes (`OpenComponent` e `AddAttribute`), os números de sequência são números de linha de código-fonte.</span><span class="sxs-lookup"><span data-stu-id="b9079-111">When calling `RenderTreeBuilder` methods to create the components (`OpenComponent` and `AddAttribute`), sequence numbers are source code line numbers.</span></span> <span data-ttu-id="b9079-112">O algoritmo de diferença mais grande do que se baseia nos números de sequência correspondentes a linhas distintas de código, não a invocações de chamada distintas.</span><span class="sxs-lookup"><span data-stu-id="b9079-112">The Blazor difference algorithm relies on the sequence numbers corresponding to distinct lines of code, not distinct call invocations.</span></span> <span data-ttu-id="b9079-113">Ao criar um componente com métodos `RenderTreeBuilder`, codifique os argumentos para números de sequência.</span><span class="sxs-lookup"><span data-stu-id="b9079-113">When creating a component with `RenderTreeBuilder` methods, hardcode the arguments for sequence numbers.</span></span> <span data-ttu-id="b9079-114">**O uso de um cálculo ou contador para gerar o número de sequência pode levar a um desempenho insatisfatório.**</span><span class="sxs-lookup"><span data-stu-id="b9079-114">**Using a calculation or counter to generate the sequence number can lead to poor performance.**</span></span> <span data-ttu-id="b9079-115">Para obter mais informações, consulte os [números de sequência relacionados à seção números de linha de código e não ordem de execução](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) .</span><span class="sxs-lookup"><span data-stu-id="b9079-115">For more information, see the [Sequence numbers relate to code line numbers and not execution order](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order) section.</span></span>

<span data-ttu-id="b9079-116">`BuiltContent` componente:</span><span class="sxs-lookup"><span data-stu-id="b9079-116">`BuiltContent` component:</span></span>

```razor
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

> [!WARNING]
> <span data-ttu-id="b9079-117">Os tipos no `Microsoft.AspNetCore.Components.RenderTree` permitem o processamento dos *resultados* de operações de renderização.</span><span class="sxs-lookup"><span data-stu-id="b9079-117">The types in `Microsoft.AspNetCore.Components.RenderTree` allow processing of the *results* of rendering operations.</span></span> <span data-ttu-id="b9079-118">Esses são detalhes internos da implementação da estrutura mais incrivelmente.</span><span class="sxs-lookup"><span data-stu-id="b9079-118">These are internal details of the Blazor framework implementation.</span></span> <span data-ttu-id="b9079-119">Esses tipos devem ser considerados *instáveis* e sujeitos a alterações em versões futuras.</span><span class="sxs-lookup"><span data-stu-id="b9079-119">These types should be considered *unstable* and subject to change in future releases.</span></span>

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a><span data-ttu-id="b9079-120">Números de sequência se relacionam a números de linha de código e não a ordem de execução</span><span class="sxs-lookup"><span data-stu-id="b9079-120">Sequence numbers relate to code line numbers and not execution order</span></span>

<span data-ttu-id="b9079-121">Os arquivos de componente Razor ( *. Razor*) são sempre compilados.</span><span class="sxs-lookup"><span data-stu-id="b9079-121">Razor component files (*.razor*) are always compiled.</span></span> <span data-ttu-id="b9079-122">A compilação é uma vantagem potencial sobre a interpretação do código, pois a etapa de compilação pode ser usada para injetar informações que melhoram o desempenho do aplicativo em tempo de execução.</span><span class="sxs-lookup"><span data-stu-id="b9079-122">Compilation is a potential advantage over interpreting code because the compile step can be used to inject information that improves app performance at runtime.</span></span>

<span data-ttu-id="b9079-123">Um exemplo importante desses aprimoramentos envolve *números de sequência*.</span><span class="sxs-lookup"><span data-stu-id="b9079-123">A key example of these improvements involves *sequence numbers*.</span></span> <span data-ttu-id="b9079-124">Os números de sequência indicam ao tempo de execução que as saídas vieram de quais linhas de código distintas e ordenadas.</span><span class="sxs-lookup"><span data-stu-id="b9079-124">Sequence numbers indicate to the runtime which outputs came from which distinct and ordered lines of code.</span></span> <span data-ttu-id="b9079-125">O tempo de execução usa essas informações para gerar comparações de árvore eficientes em tempo linear, o que é muito mais rápido do que normalmente é possível para um algoritmo de comparação de árvore geral.</span><span class="sxs-lookup"><span data-stu-id="b9079-125">The runtime uses this information to generate efficient tree diffs in linear time, which is far faster than is normally possible for a general tree diff algorithm.</span></span>

<span data-ttu-id="b9079-126">Considere o seguinte arquivo de componente Razor ( *. Razor*):</span><span class="sxs-lookup"><span data-stu-id="b9079-126">Consider the following Razor component (*.razor*) file:</span></span>

```razor
@if (someFlag)
{
    <text>First</text>
}

Second
```

<span data-ttu-id="b9079-127">O código anterior é compilado para algo semelhante ao seguinte:</span><span class="sxs-lookup"><span data-stu-id="b9079-127">The preceding code compiles to something like the following:</span></span>

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

<span data-ttu-id="b9079-128">Quando o código é executado pela primeira vez, se `someFlag` for `true`, o Construtor receberá:</span><span class="sxs-lookup"><span data-stu-id="b9079-128">When the code executes for the first time, if `someFlag` is `true`, the builder receives:</span></span>

| <span data-ttu-id="b9079-129">Sequência</span><span class="sxs-lookup"><span data-stu-id="b9079-129">Sequence</span></span> | <span data-ttu-id="b9079-130">Type</span><span class="sxs-lookup"><span data-stu-id="b9079-130">Type</span></span>      | <span data-ttu-id="b9079-131">data</span><span class="sxs-lookup"><span data-stu-id="b9079-131">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="b9079-132">0</span><span class="sxs-lookup"><span data-stu-id="b9079-132">0</span></span>        | <span data-ttu-id="b9079-133">Nó de texto</span><span class="sxs-lookup"><span data-stu-id="b9079-133">Text node</span></span> | <span data-ttu-id="b9079-134">Primeiro</span><span class="sxs-lookup"><span data-stu-id="b9079-134">First</span></span>  |
| <span data-ttu-id="b9079-135">1</span><span class="sxs-lookup"><span data-stu-id="b9079-135">1</span></span>        | <span data-ttu-id="b9079-136">Nó de texto</span><span class="sxs-lookup"><span data-stu-id="b9079-136">Text node</span></span> | <span data-ttu-id="b9079-137">Segundo</span><span class="sxs-lookup"><span data-stu-id="b9079-137">Second</span></span> |

<span data-ttu-id="b9079-138">Imagine que `someFlag` se torna `false`e a marcação é renderizada novamente.</span><span class="sxs-lookup"><span data-stu-id="b9079-138">Imagine that `someFlag` becomes `false`, and the markup is rendered again.</span></span> <span data-ttu-id="b9079-139">Desta vez, o Construtor recebe:</span><span class="sxs-lookup"><span data-stu-id="b9079-139">This time, the builder receives:</span></span>

| <span data-ttu-id="b9079-140">Sequência</span><span class="sxs-lookup"><span data-stu-id="b9079-140">Sequence</span></span> | <span data-ttu-id="b9079-141">Type</span><span class="sxs-lookup"><span data-stu-id="b9079-141">Type</span></span>       | <span data-ttu-id="b9079-142">data</span><span class="sxs-lookup"><span data-stu-id="b9079-142">Data</span></span>   |
| :------: | ---------- | :----: |
| <span data-ttu-id="b9079-143">1</span><span class="sxs-lookup"><span data-stu-id="b9079-143">1</span></span>        | <span data-ttu-id="b9079-144">Nó de texto</span><span class="sxs-lookup"><span data-stu-id="b9079-144">Text node</span></span>  | <span data-ttu-id="b9079-145">Segundo</span><span class="sxs-lookup"><span data-stu-id="b9079-145">Second</span></span> |

<span data-ttu-id="b9079-146">Quando o tempo de execução executa uma comparação, ele vê que o item na sequência `0` foi removido e, portanto, gera o seguinte *script de edição*trivial:</span><span class="sxs-lookup"><span data-stu-id="b9079-146">When the runtime performs a diff, it sees that the item at sequence `0` was removed, so it generates the following trivial *edit script*:</span></span>

* <span data-ttu-id="b9079-147">Remova o primeiro nó de texto.</span><span class="sxs-lookup"><span data-stu-id="b9079-147">Remove the first text node.</span></span>

### <a name="the-problem-with-generating-sequence-numbers-programmatically"></a><span data-ttu-id="b9079-148">O problema com a geração de números de sequência programaticamente</span><span class="sxs-lookup"><span data-stu-id="b9079-148">The problem with generating sequence numbers programmatically</span></span>

<span data-ttu-id="b9079-149">Imagine, em vez disso, que você escreveu a seguinte lógica do construtor de árvore de renderização:</span><span class="sxs-lookup"><span data-stu-id="b9079-149">Imagine instead that you wrote the following render tree builder logic:</span></span>

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

<span data-ttu-id="b9079-150">Agora, a primeira saída é:</span><span class="sxs-lookup"><span data-stu-id="b9079-150">Now, the first output is:</span></span>

| <span data-ttu-id="b9079-151">Sequência</span><span class="sxs-lookup"><span data-stu-id="b9079-151">Sequence</span></span> | <span data-ttu-id="b9079-152">Type</span><span class="sxs-lookup"><span data-stu-id="b9079-152">Type</span></span>      | <span data-ttu-id="b9079-153">data</span><span class="sxs-lookup"><span data-stu-id="b9079-153">Data</span></span>   |
| :------: | --------- | :----: |
| <span data-ttu-id="b9079-154">0</span><span class="sxs-lookup"><span data-stu-id="b9079-154">0</span></span>        | <span data-ttu-id="b9079-155">Nó de texto</span><span class="sxs-lookup"><span data-stu-id="b9079-155">Text node</span></span> | <span data-ttu-id="b9079-156">Primeiro</span><span class="sxs-lookup"><span data-stu-id="b9079-156">First</span></span>  |
| <span data-ttu-id="b9079-157">1</span><span class="sxs-lookup"><span data-stu-id="b9079-157">1</span></span>        | <span data-ttu-id="b9079-158">Nó de texto</span><span class="sxs-lookup"><span data-stu-id="b9079-158">Text node</span></span> | <span data-ttu-id="b9079-159">Segundo</span><span class="sxs-lookup"><span data-stu-id="b9079-159">Second</span></span> |

<span data-ttu-id="b9079-160">Esse resultado é idêntico ao caso anterior, portanto, não existem problemas negativos.</span><span class="sxs-lookup"><span data-stu-id="b9079-160">This outcome is identical to the prior case, so no negative issues exist.</span></span> <span data-ttu-id="b9079-161">`someFlag` é `false` no segundo processamento e a saída é:</span><span class="sxs-lookup"><span data-stu-id="b9079-161">`someFlag` is `false` on the second rendering, and the output is:</span></span>

| <span data-ttu-id="b9079-162">Sequência</span><span class="sxs-lookup"><span data-stu-id="b9079-162">Sequence</span></span> | <span data-ttu-id="b9079-163">Type</span><span class="sxs-lookup"><span data-stu-id="b9079-163">Type</span></span>      | <span data-ttu-id="b9079-164">data</span><span class="sxs-lookup"><span data-stu-id="b9079-164">Data</span></span>   |
| :------: | --------- | ------ |
| <span data-ttu-id="b9079-165">0</span><span class="sxs-lookup"><span data-stu-id="b9079-165">0</span></span>        | <span data-ttu-id="b9079-166">Nó de texto</span><span class="sxs-lookup"><span data-stu-id="b9079-166">Text node</span></span> | <span data-ttu-id="b9079-167">Segundo</span><span class="sxs-lookup"><span data-stu-id="b9079-167">Second</span></span> |

<span data-ttu-id="b9079-168">Desta vez, o algoritmo diff vê que *duas* alterações ocorreram e o algoritmo gera o seguinte script de edição:</span><span class="sxs-lookup"><span data-stu-id="b9079-168">This time, the diff algorithm sees that *two* changes have occurred, and the algorithm generates the following edit script:</span></span>

* <span data-ttu-id="b9079-169">Altere o valor do primeiro nó de texto para `Second`.</span><span class="sxs-lookup"><span data-stu-id="b9079-169">Change the value of the first text node to `Second`.</span></span>
* <span data-ttu-id="b9079-170">Remova o segundo nó de texto.</span><span class="sxs-lookup"><span data-stu-id="b9079-170">Remove the second text node.</span></span>

<span data-ttu-id="b9079-171">A geração dos números de sequência perdeu todas as informações úteis sobre onde os `if/else` branches e loops estavam presentes no código original.</span><span class="sxs-lookup"><span data-stu-id="b9079-171">Generating the sequence numbers has lost all the useful information about where the `if/else` branches and loops were present in the original code.</span></span> <span data-ttu-id="b9079-172">Isso resulta em uma comparação **duas vezes mais longa** do que antes.</span><span class="sxs-lookup"><span data-stu-id="b9079-172">This results in a diff **twice as long** as before.</span></span>

<span data-ttu-id="b9079-173">Esse é um exemplo trivial.</span><span class="sxs-lookup"><span data-stu-id="b9079-173">This is a trivial example.</span></span> <span data-ttu-id="b9079-174">Em casos mais realistas com estruturas complexas e profundamente aninhadas, e especialmente com loops, o custo de desempenho é geralmente mais alto.</span><span class="sxs-lookup"><span data-stu-id="b9079-174">In more realistic cases with complex and deeply nested structures, and especially with loops, the performance cost is usually higher.</span></span> <span data-ttu-id="b9079-175">Em vez de identificar imediatamente quais blocos de loop ou ramificações foram inseridos ou removidos, o algoritmo diff precisa recorrer profundamente nas árvores de renderização.</span><span class="sxs-lookup"><span data-stu-id="b9079-175">Instead of immediately identifying which loop blocks or branches have been inserted or removed, the diff algorithm has to recurse deeply into the render trees.</span></span> <span data-ttu-id="b9079-176">Isso geralmente resulta na criação de scripts de edição mais longos porque o algoritmo diff é informado inconscientemente sobre como as estruturas antigas e novas se relacionam entre si.</span><span class="sxs-lookup"><span data-stu-id="b9079-176">This usually results in having to build longer edit scripts because the diff algorithm is misinformed about how the old and new structures relate to each other.</span></span>

### <a name="guidance-and-conclusions"></a><span data-ttu-id="b9079-177">Diretrizes e conclusões</span><span class="sxs-lookup"><span data-stu-id="b9079-177">Guidance and conclusions</span></span>

* <span data-ttu-id="b9079-178">O desempenho do aplicativo será afetado se os números de sequência forem gerados dinamicamente.</span><span class="sxs-lookup"><span data-stu-id="b9079-178">App performance suffers if sequence numbers are generated dynamically.</span></span>
* <span data-ttu-id="b9079-179">A estrutura não pode criar seus próprios números de sequência automaticamente em tempo de execução porque as informações necessárias não existem, a menos que sejam capturadas no momento da compilação.</span><span class="sxs-lookup"><span data-stu-id="b9079-179">The framework can't create its own sequence numbers automatically at runtime because the necessary information doesn't exist unless it's captured at compile time.</span></span>
* <span data-ttu-id="b9079-180">Não grave blocos longos de lógica de `RenderTreeBuilder` implementada manualmente.</span><span class="sxs-lookup"><span data-stu-id="b9079-180">Don't write long blocks of manually-implemented `RenderTreeBuilder` logic.</span></span> <span data-ttu-id="b9079-181">Prefira arquivos *. Razor* e permita que o compilador lide com os números de sequência.</span><span class="sxs-lookup"><span data-stu-id="b9079-181">Prefer *.razor* files and allow the compiler to deal with the sequence numbers.</span></span> <span data-ttu-id="b9079-182">Se você não conseguir evitar a lógica de `RenderTreeBuilder` manual, divida blocos longos de código em partes menores encapsuladas em chamadas `OpenRegion`/`CloseRegion`.</span><span class="sxs-lookup"><span data-stu-id="b9079-182">If you're unable to avoid manual `RenderTreeBuilder` logic, split long blocks of code into smaller pieces wrapped in `OpenRegion`/`CloseRegion` calls.</span></span> <span data-ttu-id="b9079-183">Cada região tem seu próprio espaço separado de números de sequência, para que você possa reiniciar de zero (ou qualquer outro número arbitrário) dentro de cada região.</span><span class="sxs-lookup"><span data-stu-id="b9079-183">Each region has its own separate space of sequence numbers, so you can restart from zero (or any other arbitrary number) inside each region.</span></span>
* <span data-ttu-id="b9079-184">Se os números de sequência forem codificados, o algoritmo diff só exigirá que os números de sequência aumentem de valor.</span><span class="sxs-lookup"><span data-stu-id="b9079-184">If sequence numbers are hardcoded, the diff algorithm only requires that sequence numbers increase in value.</span></span> <span data-ttu-id="b9079-185">O valor inicial e as lacunas são irrelevantes.</span><span class="sxs-lookup"><span data-stu-id="b9079-185">The initial value and gaps are irrelevant.</span></span> <span data-ttu-id="b9079-186">Uma opção legítima é usar o número de linha de código como o número de sequência, ou começar de zero e aumentar por um ou centenas (ou qualquer intervalo preferencial).</span><span class="sxs-lookup"><span data-stu-id="b9079-186">One legitimate option is to use the code line number as the sequence number, or start from zero and increase by ones or hundreds (or any preferred interval).</span></span> 
* Blazor<span data-ttu-id="b9079-187"> usa números de sequência, enquanto outras estruturas de interface do usuário de diferenciação de árvore não as usam.</span><span class="sxs-lookup"><span data-stu-id="b9079-187"> uses sequence numbers, while other tree-diffing UI frameworks don't use them.</span></span> <span data-ttu-id="b9079-188">A comparação é muito mais rápida quando números de sequência são usados e Blazor tem a vantagem de uma etapa de compilação que lida com números de sequência automaticamente para desenvolvedores que criam arquivos *. Razor* .</span><span class="sxs-lookup"><span data-stu-id="b9079-188">Diffing is far faster when sequence numbers are used, and Blazor has the advantage of a compile step that deals with sequence numbers automatically for developers authoring *.razor* files.</span></span>
