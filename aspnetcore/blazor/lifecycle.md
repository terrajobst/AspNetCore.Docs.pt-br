---
title: Ciclo de ASP.NET Core Blazor
author: guardrex
description: Saiba como usar métodos de ciclo de vida de componente Razor em aplicativos ASP.NET Core Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/lifecycle
ms.openlocfilehash: df5bb676df59b538179a69978040521c4ee78ed1
ms.sourcegitcommit: cbd30479f42cbb3385000ef834d9c7d021fd218d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76146362"
---
# <a name="aspnet-core-opno-locblazor-lifecycle"></a><span data-ttu-id="1d3e0-103">Ciclo de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="1d3e0-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="1d3e0-104">De [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="1d3e0-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="1d3e0-105">O Blazor Framework inclui métodos de ciclo de vida síncronos e assíncronos.</span><span class="sxs-lookup"><span data-stu-id="1d3e0-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="1d3e0-106">Substitua métodos de ciclo de vida para executar operações adicionais em componentes durante a inicialização e a renderização do componente.</span><span class="sxs-lookup"><span data-stu-id="1d3e0-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="1d3e0-107">Métodos de ciclo de vida</span><span class="sxs-lookup"><span data-stu-id="1d3e0-107">Lifecycle methods</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="1d3e0-108">Métodos de inicialização de componente</span><span class="sxs-lookup"><span data-stu-id="1d3e0-108">Component initialization methods</span></span>

<span data-ttu-id="1d3e0-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> são invocados quando o componente é inicializado após ter recebido seus parâmetros iniciais de seu componente pai.</span><span class="sxs-lookup"><span data-stu-id="1d3e0-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> are invoked when the component is initialized after having received its initial parameters from its parent component.</span></span> <span data-ttu-id="1d3e0-110">Use `OnInitializedAsync` quando o componente executa uma operação assíncrona e deve ser atualizado quando a operação é concluída.</span><span class="sxs-lookup"><span data-stu-id="1d3e0-110">Use `OnInitializedAsync` when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span> <span data-ttu-id="1d3e0-111">Esses métodos são chamados apenas uma vez quando o componente é instanciado primeiro.</span><span class="sxs-lookup"><span data-stu-id="1d3e0-111">These methods are only called one time when the component is first instantiated.</span></span>

<span data-ttu-id="1d3e0-112">Para uma operação síncrona, substitua `OnInitialized`:</span><span class="sxs-lookup"><span data-stu-id="1d3e0-112">For a synchronous operation, override `OnInitialized`:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="1d3e0-113">Para executar uma operação assíncrona, substitua `OnInitializedAsync` e use a palavra-chave `await` na operação:</span><span class="sxs-lookup"><span data-stu-id="1d3e0-113">To perform an asynchronous operation, override `OnInitializedAsync` and use the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

### <a name="before-parameters-are-set"></a><span data-ttu-id="1d3e0-114">Antes de os parâmetros serem definidos</span><span class="sxs-lookup"><span data-stu-id="1d3e0-114">Before parameters are set</span></span>

<span data-ttu-id="1d3e0-115"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> define os parâmetros fornecidos pelo pai do componente na árvore de renderização:</span><span class="sxs-lookup"><span data-stu-id="1d3e0-115"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="1d3e0-116"><xref:Microsoft.AspNetCore.Components.ParameterView> contém todo o conjunto de valores de parâmetro sempre que `SetParametersAsync` é chamado.</span><span class="sxs-lookup"><span data-stu-id="1d3e0-116"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the entire set of parameter values each time `SetParametersAsync` is called.</span></span>

<span data-ttu-id="1d3e0-117">A implementação padrão de `SetParametersAsync` define o valor de cada propriedade com o atributo `[Parameter]` ou `[CascadingParameter]` que tem um valor correspondente no `ParameterView`.</span><span class="sxs-lookup"><span data-stu-id="1d3e0-117">The default implementation of `SetParametersAsync` sets the value of each property with the `[Parameter]` or `[CascadingParameter]` attribute that has a corresponding value in the `ParameterView`.</span></span> <span data-ttu-id="1d3e0-118">Os parâmetros que não têm um valor correspondente em `ParameterView` são deixados inalterados.</span><span class="sxs-lookup"><span data-stu-id="1d3e0-118">Parameters that don't have a corresponding value in `ParameterView` are left unchanged.</span></span>

<span data-ttu-id="1d3e0-119">Se `base.SetParametersAync` não for invocado, o código personalizado poderá interpretar o valor dos parâmetros de entrada de qualquer forma necessária.</span><span class="sxs-lookup"><span data-stu-id="1d3e0-119">If `base.SetParametersAync` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="1d3e0-120">Por exemplo, não há nenhum requisito para atribuir os parâmetros de entrada às propriedades na classe.</span><span class="sxs-lookup"><span data-stu-id="1d3e0-120">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="1d3e0-121">Depois que os parâmetros são definidos</span><span class="sxs-lookup"><span data-stu-id="1d3e0-121">After parameters are set</span></span>

<span data-ttu-id="1d3e0-122"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> são chamados:</span><span class="sxs-lookup"><span data-stu-id="1d3e0-122"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> are called:</span></span>

* <span data-ttu-id="1d3e0-123">Quando o componente é inicializado e recebeu seu primeiro conjunto de parâmetros de seu componente pai.</span><span class="sxs-lookup"><span data-stu-id="1d3e0-123">When the component is initialized and has received its first set of parameters from its parent component.</span></span>
* <span data-ttu-id="1d3e0-124">Quando o componente pai é renderizado novamente e fornece:</span><span class="sxs-lookup"><span data-stu-id="1d3e0-124">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="1d3e0-125">Somente tipos irmutáveis de primitivo conhecidos dos quais pelo menos um parâmetro foi alterado.</span><span class="sxs-lookup"><span data-stu-id="1d3e0-125">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="1d3e0-126">Qualquer parâmetro de tipo complexo.</span><span class="sxs-lookup"><span data-stu-id="1d3e0-126">Any complex-typed parameters.</span></span> <span data-ttu-id="1d3e0-127">A estrutura não pode saber se os valores de um parâmetro de tipo complexo foram modificados internamente e, portanto, trata o conjunto de parâmetros como alterado.</span><span class="sxs-lookup"><span data-stu-id="1d3e0-127">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="1d3e0-128">O trabalho assíncrono ao aplicar parâmetros e valores de propriedade deve ocorrer durante o evento de ciclo de vida `OnParametersSetAsync`.</span><span class="sxs-lookup"><span data-stu-id="1d3e0-128">Asynchronous work when applying parameters and property values must occur during the `OnParametersSetAsync` lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

### <a name="after-component-render"></a><span data-ttu-id="1d3e0-129">Após renderização de componente</span><span class="sxs-lookup"><span data-stu-id="1d3e0-129">After component render</span></span>

<span data-ttu-id="1d3e0-130"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> são chamados após a conclusão da renderização de um componente.</span><span class="sxs-lookup"><span data-stu-id="1d3e0-130"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> are called after a component has finished rendering.</span></span> <span data-ttu-id="1d3e0-131">Referências de elemento e componente são preenchidas neste ponto.</span><span class="sxs-lookup"><span data-stu-id="1d3e0-131">Element and component references are populated at this point.</span></span> <span data-ttu-id="1d3e0-132">Use este estágio para executar etapas de inicialização adicionais usando o conteúdo renderizado, como a ativação de bibliotecas JavaScript de terceiros que operam nos elementos DOM renderizados.</span><span class="sxs-lookup"><span data-stu-id="1d3e0-132">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="1d3e0-133">O parâmetro `firstRender` para `OnAfterRenderAsync` e `OnAfterRender`:</span><span class="sxs-lookup"><span data-stu-id="1d3e0-133">The `firstRender` parameter for `OnAfterRenderAsync` and `OnAfterRender`:</span></span>

* <span data-ttu-id="1d3e0-134">É definido como `true` na primeira vez que a instância do componente é renderizada.</span><span class="sxs-lookup"><span data-stu-id="1d3e0-134">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="1d3e0-135">Pode ser usado para garantir que o trabalho de inicialização seja executado apenas uma vez.</span><span class="sxs-lookup"><span data-stu-id="1d3e0-135">Can be used to ensure that initialization work is only performed once.</span></span>

```csharp
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (firstRender)
    {
        await ...
    }
}
```

> [!NOTE]
> <span data-ttu-id="1d3e0-136">O trabalho assíncrono imediatamente após a renderização deve ocorrer durante o evento de ciclo de vida `OnAfterRenderAsync`.</span><span class="sxs-lookup"><span data-stu-id="1d3e0-136">Asynchronous work immediately after rendering must occur during the `OnAfterRenderAsync` lifecycle event.</span></span>
>
> <span data-ttu-id="1d3e0-137">Mesmo que você retorne um <xref:System.Threading.Tasks.Task> de `OnAfterRenderAsync`, a estrutura não agendará um ciclo de processamento adicional para o componente depois que a tarefa for concluída.</span><span class="sxs-lookup"><span data-stu-id="1d3e0-137">Even if you return a <xref:System.Threading.Tasks.Task> from `OnAfterRenderAsync`, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="1d3e0-138">Isso é para evitar um loop de renderização infinito.</span><span class="sxs-lookup"><span data-stu-id="1d3e0-138">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="1d3e0-139">Ele é diferente dos outros métodos de ciclo de vida, que agendam um ciclo de processamento adicional depois que a tarefa retornada é concluída.</span><span class="sxs-lookup"><span data-stu-id="1d3e0-139">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="1d3e0-140">`OnAfterRender` e `OnAfterRenderAsync` *não são chamados durante o pré-processamento no servidor.*</span><span class="sxs-lookup"><span data-stu-id="1d3e0-140">`OnAfterRender` and `OnAfterRenderAsync` *aren't called when prerendering on the server.*</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="1d3e0-141">Suprimir atualização da interface do usuário</span><span class="sxs-lookup"><span data-stu-id="1d3e0-141">Suppress UI refreshing</span></span>

<span data-ttu-id="1d3e0-142">Substitua <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> para suprimir a atualização da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="1d3e0-142">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> to suppress UI refreshing.</span></span> <span data-ttu-id="1d3e0-143">Se a implementação retornar `true`, a interface do usuário será atualizada:</span><span class="sxs-lookup"><span data-stu-id="1d3e0-143">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="1d3e0-144">`ShouldRender` é chamado cada vez que o componente é renderizado.</span><span class="sxs-lookup"><span data-stu-id="1d3e0-144">`ShouldRender` is called each time the component is rendered.</span></span>

<span data-ttu-id="1d3e0-145">Mesmo se `ShouldRender` for substituído, o componente será sempre renderizado inicialmente.</span><span class="sxs-lookup"><span data-stu-id="1d3e0-145">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

## <a name="state-changes"></a><span data-ttu-id="1d3e0-146">Alterações de estado</span><span class="sxs-lookup"><span data-stu-id="1d3e0-146">State changes</span></span>

<span data-ttu-id="1d3e0-147"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> notifica o componente de que seu estado foi alterado.</span><span class="sxs-lookup"><span data-stu-id="1d3e0-147"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> notifies the component that its state has changed.</span></span> <span data-ttu-id="1d3e0-148">Quando aplicável, chamar `StateHasChanged` faz com que o componente seja rerenderizado.</span><span class="sxs-lookup"><span data-stu-id="1d3e0-148">When applicable, calling `StateHasChanged` causes the component to be rerendered.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="1d3e0-149">Tratar ações assíncronas incompletas no processamento</span><span class="sxs-lookup"><span data-stu-id="1d3e0-149">Handle incomplete async actions at render</span></span>

<span data-ttu-id="1d3e0-150">Ações assíncronas executadas em eventos de ciclo de vida podem não ter sido concluídas antes de o componente ser renderizado.</span><span class="sxs-lookup"><span data-stu-id="1d3e0-150">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="1d3e0-151">Os objetos podem ser `null` ou preenchidos incompletamente com dados enquanto o método de ciclo de vida está em execução.</span><span class="sxs-lookup"><span data-stu-id="1d3e0-151">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="1d3e0-152">Forneça a lógica de renderização para confirmar que os objetos são inicializados.</span><span class="sxs-lookup"><span data-stu-id="1d3e0-152">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="1d3e0-153">Renderizar elementos de interface do usuário de espaço reservado (por exemplo, uma mensagem de carregamento) enquanto os objetos são `null`.</span><span class="sxs-lookup"><span data-stu-id="1d3e0-153">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="1d3e0-154">No componente `FetchData` dos modelos de Blazor, `OnInitializedAsync` é substituído para Asychronously receber dados de previsão (`forecasts`).</span><span class="sxs-lookup"><span data-stu-id="1d3e0-154">In the `FetchData` component of the Blazor templates, `OnInitializedAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="1d3e0-155">Quando `forecasts` é `null`, uma mensagem de carregamento é exibida para o usuário.</span><span class="sxs-lookup"><span data-stu-id="1d3e0-155">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="1d3e0-156">Depois que o `Task` retornado pela `OnInitializedAsync` for concluído, o componente será rerenderizado com o estado atualizado.</span><span class="sxs-lookup"><span data-stu-id="1d3e0-156">After the `Task` returned by `OnInitializedAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="1d3e0-157">*Páginas/FetchData. Razor* no modelo do Blazor Server:</span><span class="sxs-lookup"><span data-stu-id="1d3e0-157">*Pages/FetchData.razor* in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="1d3e0-158">Descarte de componentes com IDisposable</span><span class="sxs-lookup"><span data-stu-id="1d3e0-158">Component disposal with IDisposable</span></span>

<span data-ttu-id="1d3e0-159">Se um componente implementar <xref:System.IDisposable>, o [método Dispose](/dotnet/standard/garbage-collection/implementing-dispose) será chamado quando o componente for removido da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="1d3e0-159">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="1d3e0-160">O componente a seguir usa `@implements IDisposable` e o método `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="1d3e0-160">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

```razor
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

> [!NOTE]
> <span data-ttu-id="1d3e0-161">Não há suporte para a chamada de <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> em `Dispose`.</span><span class="sxs-lookup"><span data-stu-id="1d3e0-161">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> in `Dispose` isn't supported.</span></span> <span data-ttu-id="1d3e0-162">`StateHasChanged` pode ser invocado como parte da subdivisão do renderizador, portanto, não há suporte para a solicitação de atualizações da interface do usuário nesse ponto.</span><span class="sxs-lookup"><span data-stu-id="1d3e0-162">`StateHasChanged` might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

## <a name="handle-errors"></a><span data-ttu-id="1d3e0-163">Tratar erros</span><span class="sxs-lookup"><span data-stu-id="1d3e0-163">Handle errors</span></span>

<span data-ttu-id="1d3e0-164">Para obter informações sobre como lidar com erros durante a execução do método de ciclo de vida, consulte <xref:blazor/handle-errors#lifecycle-methods>.</span><span class="sxs-lookup"><span data-stu-id="1d3e0-164">For information on handling errors during lifecycle method execution, see <xref:blazor/handle-errors#lifecycle-methods>.</span></span>
