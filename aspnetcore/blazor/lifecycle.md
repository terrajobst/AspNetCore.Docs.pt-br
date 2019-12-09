---
title: Ciclo de ASP.NET Core Blazor
author: guardrex
description: Saiba como usar métodos de ciclo de vida de componente Razor em aplicativos ASP.NET Core Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
uid: blazor/lifecycle
ms.openlocfilehash: e600e7c7a6a8c646a655520bd5c127f2cd662753
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74944025"
---
# <a name="aspnet-core-opno-locblazor-lifecycle"></a><span data-ttu-id="258fb-103">Ciclo de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="258fb-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="258fb-104">De [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="258fb-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="258fb-105">O Blazor Framework inclui métodos de ciclo de vida síncronos e assíncronos.</span><span class="sxs-lookup"><span data-stu-id="258fb-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="258fb-106">Substitua métodos de ciclo de vida para executar operações adicionais em componentes durante a inicialização e a renderização do componente.</span><span class="sxs-lookup"><span data-stu-id="258fb-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="258fb-107">Métodos de ciclo de vida</span><span class="sxs-lookup"><span data-stu-id="258fb-107">Lifecycle methods</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="258fb-108">Métodos de inicialização de componente</span><span class="sxs-lookup"><span data-stu-id="258fb-108">Component initialization methods</span></span>

<span data-ttu-id="258fb-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> executar código que inicializa um componente.</span><span class="sxs-lookup"><span data-stu-id="258fb-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> execute code that initializes a component.</span></span> <span data-ttu-id="258fb-110">Esses métodos são chamados apenas uma vez quando o componente é instanciado primeiro.</span><span class="sxs-lookup"><span data-stu-id="258fb-110">These methods are only called one time when the component is first instantiated.</span></span>

<span data-ttu-id="258fb-111">Para executar uma operação assíncrona, use `OnInitializedAsync` e a palavra-chave `await` na operação:</span><span class="sxs-lookup"><span data-stu-id="258fb-111">To perform an asynchronous operation, use `OnInitializedAsync` and the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="258fb-112">O trabalho assíncrono durante a inicialização do componente deve ocorrer durante o evento de ciclo de vida `OnInitializedAsync`.</span><span class="sxs-lookup"><span data-stu-id="258fb-112">Asynchronous work during component initialization must occur during the `OnInitializedAsync` lifecycle event.</span></span>

<span data-ttu-id="258fb-113">Para uma operação síncrona, use `OnInitialized`:</span><span class="sxs-lookup"><span data-stu-id="258fb-113">For a synchronous operation, use `OnInitialized`:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

### <a name="before-parameters-are-set"></a><span data-ttu-id="258fb-114">Antes de os parâmetros serem definidos</span><span class="sxs-lookup"><span data-stu-id="258fb-114">Before parameters are set</span></span>

<span data-ttu-id="258fb-115"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> define os parâmetros fornecidos pelo pai do componente na árvore de renderização:</span><span class="sxs-lookup"><span data-stu-id="258fb-115"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="258fb-116"><xref:Microsoft.AspNetCore.Components.ParameterView> contém todo o conjunto de valores de parâmetro sempre que `SetParametersAsync` é chamado.</span><span class="sxs-lookup"><span data-stu-id="258fb-116"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the entire set of parameter values each time `SetParametersAsync` is called.</span></span>

<span data-ttu-id="258fb-117">A implementação padrão de `SetParametersAsync` define o valor de cada propriedade com o atributo `[Parameter]` ou `[CascadingParameter]` que tem um valor correspondente no `ParameterView`.</span><span class="sxs-lookup"><span data-stu-id="258fb-117">The default implementation of `SetParametersAsync` sets the value of each property with the `[Parameter]` or `[CascadingParameter]` attribute that has a corresponding value in the `ParameterView`.</span></span> <span data-ttu-id="258fb-118">Os parâmetros que não têm um valor correspondente em `ParameterView` são deixados inalterados.</span><span class="sxs-lookup"><span data-stu-id="258fb-118">Parameters that don't have a corresponding value in `ParameterView` are left unchanged.</span></span>

<span data-ttu-id="258fb-119">Se `base.SetParametersAync` não for invocado, o código personalizado poderá interpretar o valor dos parâmetros de entrada de qualquer forma necessária.</span><span class="sxs-lookup"><span data-stu-id="258fb-119">If `base.SetParametersAync` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="258fb-120">Por exemplo, não há nenhum requisito para atribuir os parâmetros de entrada às propriedades na classe.</span><span class="sxs-lookup"><span data-stu-id="258fb-120">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="258fb-121">Depois que os parâmetros são definidos</span><span class="sxs-lookup"><span data-stu-id="258fb-121">After parameters are set</span></span>

<span data-ttu-id="258fb-122"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> são chamados quando um componente recebe parâmetros de seu pai e os valores são atribuídos às propriedades.</span><span class="sxs-lookup"><span data-stu-id="258fb-122"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> are called when a component has received parameters from its parent and the values are assigned to properties.</span></span> <span data-ttu-id="258fb-123">Esses métodos são executados após a inicialização do componente e cada vez que novos valores de parâmetro são especificados:</span><span class="sxs-lookup"><span data-stu-id="258fb-123">These methods are executed after component initialization and each time new parameter values are specified:</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="258fb-124">O trabalho assíncrono ao aplicar parâmetros e valores de propriedade deve ocorrer durante o evento de ciclo de vida `OnParametersSetAsync`.</span><span class="sxs-lookup"><span data-stu-id="258fb-124">Asynchronous work when applying parameters and property values must occur during the `OnParametersSetAsync` lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

### <a name="after-component-render"></a><span data-ttu-id="258fb-125">Após renderização de componente</span><span class="sxs-lookup"><span data-stu-id="258fb-125">After component render</span></span>

<span data-ttu-id="258fb-126"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> são chamados após a conclusão da renderização de um componente.</span><span class="sxs-lookup"><span data-stu-id="258fb-126"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> are called after a component has finished rendering.</span></span> <span data-ttu-id="258fb-127">Referências de elemento e componente são preenchidas neste ponto.</span><span class="sxs-lookup"><span data-stu-id="258fb-127">Element and component references are populated at this point.</span></span> <span data-ttu-id="258fb-128">Use este estágio para executar etapas de inicialização adicionais usando o conteúdo renderizado, como a ativação de bibliotecas JavaScript de terceiros que operam nos elementos DOM renderizados.</span><span class="sxs-lookup"><span data-stu-id="258fb-128">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="258fb-129">O parâmetro `firstRender` para `OnAfterRenderAsync` e `OnAfterRender`:</span><span class="sxs-lookup"><span data-stu-id="258fb-129">The `firstRender` parameter for `OnAfterRenderAsync` and `OnAfterRender`:</span></span>

* <span data-ttu-id="258fb-130">É definido como `true` na primeira vez que a instância do componente é renderizada.</span><span class="sxs-lookup"><span data-stu-id="258fb-130">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="258fb-131">Pode ser usado para garantir que o trabalho de inicialização seja executado apenas uma vez.</span><span class="sxs-lookup"><span data-stu-id="258fb-131">Can be used to ensure that initialization work is only performed once.</span></span>

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
> <span data-ttu-id="258fb-132">O trabalho assíncrono imediatamente após a renderização deve ocorrer durante o evento de ciclo de vida `OnAfterRenderAsync`.</span><span class="sxs-lookup"><span data-stu-id="258fb-132">Asynchronous work immediately after rendering must occur during the `OnAfterRenderAsync` lifecycle event.</span></span>
>
> <span data-ttu-id="258fb-133">Mesmo que você retorne um <xref:System.Threading.Tasks.Task> de `OnAfterRenderAsync`, a estrutura não agendará um ciclo de processamento adicional para o componente depois que a tarefa for concluída.</span><span class="sxs-lookup"><span data-stu-id="258fb-133">Even if you return a <xref:System.Threading.Tasks.Task> from `OnAfterRenderAsync`, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="258fb-134">Isso é para evitar um loop de renderização infinito.</span><span class="sxs-lookup"><span data-stu-id="258fb-134">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="258fb-135">Ele é diferente dos outros métodos de ciclo de vida, que agendam um ciclo de processamento adicional depois que a tarefa retornada é concluída.</span><span class="sxs-lookup"><span data-stu-id="258fb-135">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="258fb-136">`OnAfterRender` e `OnAfterRenderAsync` *não são chamados durante o pré-processamento no servidor.*</span><span class="sxs-lookup"><span data-stu-id="258fb-136">`OnAfterRender` and `OnAfterRenderAsync` *aren't called when prerendering on the server.*</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="258fb-137">Suprimir atualização da interface do usuário</span><span class="sxs-lookup"><span data-stu-id="258fb-137">Suppress UI refreshing</span></span>

<span data-ttu-id="258fb-138">Substitua <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> para suprimir a atualização da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="258fb-138">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> to suppress UI refreshing.</span></span> <span data-ttu-id="258fb-139">Se a implementação retornar `true`, a interface do usuário será atualizada:</span><span class="sxs-lookup"><span data-stu-id="258fb-139">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="258fb-140">`ShouldRender` é chamado cada vez que o componente é renderizado.</span><span class="sxs-lookup"><span data-stu-id="258fb-140">`ShouldRender` is called each time the component is rendered.</span></span>

<span data-ttu-id="258fb-141">Mesmo se `ShouldRender` for substituído, o componente será sempre renderizado inicialmente.</span><span class="sxs-lookup"><span data-stu-id="258fb-141">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

## <a name="state-changes"></a><span data-ttu-id="258fb-142">Alterações de estado</span><span class="sxs-lookup"><span data-stu-id="258fb-142">State changes</span></span>

<span data-ttu-id="258fb-143"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> notifica o componente de que seu estado foi alterado.</span><span class="sxs-lookup"><span data-stu-id="258fb-143"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> notifies the component that its state has changed.</span></span> <span data-ttu-id="258fb-144">Quando aplicável, chamar `StateHasChanged` faz com que o componente seja rerenderizado.</span><span class="sxs-lookup"><span data-stu-id="258fb-144">When applicable, calling `StateHasChanged` causes the component to be rerendered.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="258fb-145">Tratar ações assíncronas incompletas no processamento</span><span class="sxs-lookup"><span data-stu-id="258fb-145">Handle incomplete async actions at render</span></span>

<span data-ttu-id="258fb-146">Ações assíncronas executadas em eventos de ciclo de vida podem não ter sido concluídas antes de o componente ser renderizado.</span><span class="sxs-lookup"><span data-stu-id="258fb-146">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="258fb-147">Os objetos podem ser `null` ou preenchidos incompletamente com dados enquanto o método de ciclo de vida está em execução.</span><span class="sxs-lookup"><span data-stu-id="258fb-147">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="258fb-148">Forneça a lógica de renderização para confirmar que os objetos são inicializados.</span><span class="sxs-lookup"><span data-stu-id="258fb-148">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="258fb-149">Renderizar elementos de interface do usuário de espaço reservado (por exemplo, uma mensagem de carregamento) enquanto os objetos são `null`.</span><span class="sxs-lookup"><span data-stu-id="258fb-149">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="258fb-150">No componente `FetchData` dos modelos de Blazor, `OnInitializedAsync` é substituído para Asychronously receber dados de previsão (`forecasts`).</span><span class="sxs-lookup"><span data-stu-id="258fb-150">In the `FetchData` component of the Blazor templates, `OnInitializedAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="258fb-151">Quando `forecasts` é `null`, uma mensagem de carregamento é exibida para o usuário.</span><span class="sxs-lookup"><span data-stu-id="258fb-151">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="258fb-152">Depois que o `Task` retornado pela `OnInitializedAsync` for concluído, o componente será rerenderizado com o estado atualizado.</span><span class="sxs-lookup"><span data-stu-id="258fb-152">After the `Task` returned by `OnInitializedAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="258fb-153">*Páginas/FetchData. Razor* no modelo do Blazor Server:</span><span class="sxs-lookup"><span data-stu-id="258fb-153">*Pages/FetchData.razor* in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="258fb-154">Descarte de componentes com IDisposable</span><span class="sxs-lookup"><span data-stu-id="258fb-154">Component disposal with IDisposable</span></span>

<span data-ttu-id="258fb-155">Se um componente implementar <xref:System.IDisposable>, o [método Dispose](/dotnet/standard/garbage-collection/implementing-dispose) será chamado quando o componente for removido da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="258fb-155">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="258fb-156">O componente a seguir usa `@implements IDisposable` e o método `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="258fb-156">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

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

> [!NOTE]
> <span data-ttu-id="258fb-157">Não há suporte para a chamada de <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> em `Dispose`.</span><span class="sxs-lookup"><span data-stu-id="258fb-157">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> in `Dispose` isn't supported.</span></span> <span data-ttu-id="258fb-158">`StateHasChanged` pode ser invocado como parte da subdivisão do renderizador, portanto, não há suporte para a solicitação de atualizações da interface do usuário nesse ponto.</span><span class="sxs-lookup"><span data-stu-id="258fb-158">`StateHasChanged` might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

## <a name="handle-errors"></a><span data-ttu-id="258fb-159">Tratar erros</span><span class="sxs-lookup"><span data-stu-id="258fb-159">Handle errors</span></span>

<span data-ttu-id="258fb-160">Para obter informações sobre como lidar com erros durante a execução do método de ciclo de vida, consulte <xref:blazor/handle-errors#lifecycle-methods>.</span><span class="sxs-lookup"><span data-stu-id="258fb-160">For information on handling errors during lifecycle method execution, see <xref:blazor/handle-errors#lifecycle-methods>.</span></span>
