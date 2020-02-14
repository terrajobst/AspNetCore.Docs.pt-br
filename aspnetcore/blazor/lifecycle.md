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
ms.openlocfilehash: ecacd0a9728cbefd716e9dc7cd8a8c62f3df6e0d
ms.sourcegitcommit: d2ba66023884f0dca115ff010bd98d5ed6459283
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77213383"
---
# <a name="aspnet-core-opno-locblazor-lifecycle"></a><span data-ttu-id="cec86-103">Ciclo de ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="cec86-103">ASP.NET Core Blazor lifecycle</span></span>

<span data-ttu-id="cec86-104">De [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="cec86-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="cec86-105">O Blazor Framework inclui métodos de ciclo de vida síncronos e assíncronos.</span><span class="sxs-lookup"><span data-stu-id="cec86-105">The Blazor framework includes synchronous and asynchronous lifecycle methods.</span></span> <span data-ttu-id="cec86-106">Substitua métodos de ciclo de vida para executar operações adicionais em componentes durante a inicialização e a renderização do componente.</span><span class="sxs-lookup"><span data-stu-id="cec86-106">Override lifecycle methods to perform additional operations on components during component initialization and rendering.</span></span>

## <a name="lifecycle-methods"></a><span data-ttu-id="cec86-107">Métodos de ciclo de vida</span><span class="sxs-lookup"><span data-stu-id="cec86-107">Lifecycle methods</span></span>

### <a name="component-initialization-methods"></a><span data-ttu-id="cec86-108">Métodos de inicialização de componente</span><span class="sxs-lookup"><span data-stu-id="cec86-108">Component initialization methods</span></span>

<span data-ttu-id="cec86-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> são invocados quando o componente é inicializado após ter recebido seus parâmetros iniciais de seu componente pai.</span><span class="sxs-lookup"><span data-stu-id="cec86-109"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> are invoked when the component is initialized after having received its initial parameters from its parent component.</span></span> <span data-ttu-id="cec86-110">Use `OnInitializedAsync` quando o componente executa uma operação assíncrona e deve ser atualizado quando a operação é concluída.</span><span class="sxs-lookup"><span data-stu-id="cec86-110">Use `OnInitializedAsync` when the component performs an asynchronous operation and should refresh when the operation is completed.</span></span>

<span data-ttu-id="cec86-111">Para uma operação síncrona, substitua `OnInitialized`:</span><span class="sxs-lookup"><span data-stu-id="cec86-111">For a synchronous operation, override `OnInitialized`:</span></span>

```csharp
protected override void OnInitialized()
{
    ...
}
```

<span data-ttu-id="cec86-112">Para executar uma operação assíncrona, substitua `OnInitializedAsync` e use a palavra-chave `await` na operação:</span><span class="sxs-lookup"><span data-stu-id="cec86-112">To perform an asynchronous operation, override `OnInitializedAsync` and use the `await` keyword on the operation:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

Blazor<span data-ttu-id="cec86-113"> aplicativos de servidor que preparam [sua chamada de conteúdo](xref:blazor/hosting-model-configuration#render-mode) `OnInitializedAsync` **_duas vezes_** :</span><span class="sxs-lookup"><span data-stu-id="cec86-113"> Server apps that [prerender their content](xref:blazor/hosting-model-configuration#render-mode) call `OnInitializedAsync` **_twice_**:</span></span>

* <span data-ttu-id="cec86-114">Uma vez quando o componente é inicialmente renderizado estaticamente como parte da página.</span><span class="sxs-lookup"><span data-stu-id="cec86-114">Once when the component is initially rendered statically as part of the page.</span></span>
* <span data-ttu-id="cec86-115">Uma segunda vez quando o navegador estabelece uma conexão de volta para o servidor.</span><span class="sxs-lookup"><span data-stu-id="cec86-115">A second time when the browser establishes a connection back to the server.</span></span>

<span data-ttu-id="cec86-116">Para impedir que o código do desenvolvedor em `OnInitializedAsync` seja executado duas vezes, consulte a seção [reconexão com estado após o pré-processamento](#stateful-reconnection-after-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="cec86-116">To prevent developer code in `OnInitializedAsync` from running twice, see the [Stateful reconnection after prerendering](#stateful-reconnection-after-prerendering) section.</span></span>

<span data-ttu-id="cec86-117">Embora um aplicativo de servidor Blazor esteja sendo renderizado, determinadas ações, como a chamada para JavaScript, não são possíveis porque uma conexão com o navegador não foi estabelecida.</span><span class="sxs-lookup"><span data-stu-id="cec86-117">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="cec86-118">Os componentes podem precisar ser renderizados de forma diferente quando renderizados.</span><span class="sxs-lookup"><span data-stu-id="cec86-118">Components may need to render differently when prerendered.</span></span> <span data-ttu-id="cec86-119">Para obter mais informações, consulte a seção [detectar quando o aplicativo está sendo processado](#detect-when-the-app-is-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="cec86-119">For more information, see the [Detect when the app is prerendering](#detect-when-the-app-is-prerendering) section.</span></span>

### <a name="before-parameters-are-set"></a><span data-ttu-id="cec86-120">Antes de os parâmetros serem definidos</span><span class="sxs-lookup"><span data-stu-id="cec86-120">Before parameters are set</span></span>

<span data-ttu-id="cec86-121"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> define os parâmetros fornecidos pelo pai do componente na árvore de renderização:</span><span class="sxs-lookup"><span data-stu-id="cec86-121"><xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> sets parameters supplied by the component's parent in the render tree:</span></span>

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<span data-ttu-id="cec86-122"><xref:Microsoft.AspNetCore.Components.ParameterView> contém todo o conjunto de valores de parâmetro sempre que `SetParametersAsync` é chamado.</span><span class="sxs-lookup"><span data-stu-id="cec86-122"><xref:Microsoft.AspNetCore.Components.ParameterView> contains the entire set of parameter values each time `SetParametersAsync` is called.</span></span>

<span data-ttu-id="cec86-123">A implementação padrão de `SetParametersAsync` define o valor de cada propriedade com o atributo `[Parameter]` ou `[CascadingParameter]` que tem um valor correspondente no `ParameterView`.</span><span class="sxs-lookup"><span data-stu-id="cec86-123">The default implementation of `SetParametersAsync` sets the value of each property with the `[Parameter]` or `[CascadingParameter]` attribute that has a corresponding value in the `ParameterView`.</span></span> <span data-ttu-id="cec86-124">Os parâmetros que não têm um valor correspondente em `ParameterView` são deixados inalterados.</span><span class="sxs-lookup"><span data-stu-id="cec86-124">Parameters that don't have a corresponding value in `ParameterView` are left unchanged.</span></span>

<span data-ttu-id="cec86-125">Se `base.SetParametersAync` não for invocado, o código personalizado poderá interpretar o valor dos parâmetros de entrada de qualquer forma necessária.</span><span class="sxs-lookup"><span data-stu-id="cec86-125">If `base.SetParametersAync` isn't invoked, the custom code can interpret the incoming parameters value in any way required.</span></span> <span data-ttu-id="cec86-126">Por exemplo, não há nenhum requisito para atribuir os parâmetros de entrada às propriedades na classe.</span><span class="sxs-lookup"><span data-stu-id="cec86-126">For example, there's no requirement to assign the incoming parameters to the properties on the class.</span></span>

### <a name="after-parameters-are-set"></a><span data-ttu-id="cec86-127">Depois que os parâmetros são definidos</span><span class="sxs-lookup"><span data-stu-id="cec86-127">After parameters are set</span></span>

<span data-ttu-id="cec86-128"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> são chamados:</span><span class="sxs-lookup"><span data-stu-id="cec86-128"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> are called:</span></span>

* <span data-ttu-id="cec86-129">Quando o componente é inicializado e recebeu seu primeiro conjunto de parâmetros de seu componente pai.</span><span class="sxs-lookup"><span data-stu-id="cec86-129">When the component is initialized and has received its first set of parameters from its parent component.</span></span>
* <span data-ttu-id="cec86-130">Quando o componente pai é renderizado novamente e fornece:</span><span class="sxs-lookup"><span data-stu-id="cec86-130">When the parent component re-renders and supplies:</span></span>
  * <span data-ttu-id="cec86-131">Somente tipos irmutáveis de primitivo conhecidos dos quais pelo menos um parâmetro foi alterado.</span><span class="sxs-lookup"><span data-stu-id="cec86-131">Only known primitive immutable types of which at least one parameter has changed.</span></span>
  * <span data-ttu-id="cec86-132">Qualquer parâmetro de tipo complexo.</span><span class="sxs-lookup"><span data-stu-id="cec86-132">Any complex-typed parameters.</span></span> <span data-ttu-id="cec86-133">A estrutura não pode saber se os valores de um parâmetro de tipo complexo foram modificados internamente e, portanto, trata o conjunto de parâmetros como alterado.</span><span class="sxs-lookup"><span data-stu-id="cec86-133">The framework can't know whether the values of a complex-typed parameter have mutated internally, so it treats the parameter set as changed.</span></span>

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> <span data-ttu-id="cec86-134">O trabalho assíncrono ao aplicar parâmetros e valores de propriedade deve ocorrer durante o evento de ciclo de vida `OnParametersSetAsync`.</span><span class="sxs-lookup"><span data-stu-id="cec86-134">Asynchronous work when applying parameters and property values must occur during the `OnParametersSetAsync` lifecycle event.</span></span>

```csharp
protected override void OnParametersSet()
{
    ...
}
```

### <a name="after-component-render"></a><span data-ttu-id="cec86-135">Após renderização de componente</span><span class="sxs-lookup"><span data-stu-id="cec86-135">After component render</span></span>

<span data-ttu-id="cec86-136"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> são chamados após a conclusão da renderização de um componente.</span><span class="sxs-lookup"><span data-stu-id="cec86-136"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> and <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> are called after a component has finished rendering.</span></span> <span data-ttu-id="cec86-137">Referências de elemento e componente são preenchidas neste ponto.</span><span class="sxs-lookup"><span data-stu-id="cec86-137">Element and component references are populated at this point.</span></span> <span data-ttu-id="cec86-138">Use este estágio para executar etapas de inicialização adicionais usando o conteúdo renderizado, como a ativação de bibliotecas JavaScript de terceiros que operam nos elementos DOM renderizados.</span><span class="sxs-lookup"><span data-stu-id="cec86-138">Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.</span></span>

<span data-ttu-id="cec86-139">O parâmetro `firstRender` para `OnAfterRenderAsync` e `OnAfterRender`:</span><span class="sxs-lookup"><span data-stu-id="cec86-139">The `firstRender` parameter for `OnAfterRenderAsync` and `OnAfterRender`:</span></span>

* <span data-ttu-id="cec86-140">É definido como `true` na primeira vez que a instância do componente é renderizada.</span><span class="sxs-lookup"><span data-stu-id="cec86-140">Is set to `true` the first time that the component instance is rendered.</span></span>
* <span data-ttu-id="cec86-141">Pode ser usado para garantir que o trabalho de inicialização seja executado apenas uma vez.</span><span class="sxs-lookup"><span data-stu-id="cec86-141">Can be used to ensure that initialization work is only performed once.</span></span>

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
> <span data-ttu-id="cec86-142">O trabalho assíncrono imediatamente após a renderização deve ocorrer durante o evento de ciclo de vida `OnAfterRenderAsync`.</span><span class="sxs-lookup"><span data-stu-id="cec86-142">Asynchronous work immediately after rendering must occur during the `OnAfterRenderAsync` lifecycle event.</span></span>
>
> <span data-ttu-id="cec86-143">Mesmo que você retorne um <xref:System.Threading.Tasks.Task> de `OnAfterRenderAsync`, a estrutura não agendará um ciclo de processamento adicional para o componente depois que a tarefa for concluída.</span><span class="sxs-lookup"><span data-stu-id="cec86-143">Even if you return a <xref:System.Threading.Tasks.Task> from `OnAfterRenderAsync`, the framework doesn't schedule a further render cycle for your component once that task completes.</span></span> <span data-ttu-id="cec86-144">Isso é para evitar um loop de renderização infinito.</span><span class="sxs-lookup"><span data-stu-id="cec86-144">This is to avoid an infinite render loop.</span></span> <span data-ttu-id="cec86-145">Ele é diferente dos outros métodos de ciclo de vida, que agendam um ciclo de processamento adicional depois que a tarefa retornada é concluída.</span><span class="sxs-lookup"><span data-stu-id="cec86-145">It's different from the other lifecycle methods, which schedule a further render cycle once the returned task completes.</span></span>

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

<span data-ttu-id="cec86-146">`OnAfterRender` e `OnAfterRenderAsync` *não são chamados durante o pré-processamento no servidor.*</span><span class="sxs-lookup"><span data-stu-id="cec86-146">`OnAfterRender` and `OnAfterRenderAsync` *aren't called when prerendering on the server.*</span></span>

### <a name="suppress-ui-refreshing"></a><span data-ttu-id="cec86-147">Suprimir atualização da interface do usuário</span><span class="sxs-lookup"><span data-stu-id="cec86-147">Suppress UI refreshing</span></span>

<span data-ttu-id="cec86-148">Substitua <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> para suprimir a atualização da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="cec86-148">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> to suppress UI refreshing.</span></span> <span data-ttu-id="cec86-149">Se a implementação retornar `true`, a interface do usuário será atualizada:</span><span class="sxs-lookup"><span data-stu-id="cec86-149">If the implementation returns `true`, the UI is refreshed:</span></span>

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

<span data-ttu-id="cec86-150">`ShouldRender` é chamado cada vez que o componente é renderizado.</span><span class="sxs-lookup"><span data-stu-id="cec86-150">`ShouldRender` is called each time the component is rendered.</span></span>

<span data-ttu-id="cec86-151">Mesmo se `ShouldRender` for substituído, o componente será sempre renderizado inicialmente.</span><span class="sxs-lookup"><span data-stu-id="cec86-151">Even if `ShouldRender` is overridden, the component is always initially rendered.</span></span>

## <a name="state-changes"></a><span data-ttu-id="cec86-152">Alterações de estado</span><span class="sxs-lookup"><span data-stu-id="cec86-152">State changes</span></span>

<span data-ttu-id="cec86-153"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> notifica o componente de que seu estado foi alterado.</span><span class="sxs-lookup"><span data-stu-id="cec86-153"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> notifies the component that its state has changed.</span></span> <span data-ttu-id="cec86-154">Quando aplicável, chamar `StateHasChanged` faz com que o componente seja rerenderizado.</span><span class="sxs-lookup"><span data-stu-id="cec86-154">When applicable, calling `StateHasChanged` causes the component to be rerendered.</span></span>

## <a name="handle-incomplete-async-actions-at-render"></a><span data-ttu-id="cec86-155">Tratar ações assíncronas incompletas no processamento</span><span class="sxs-lookup"><span data-stu-id="cec86-155">Handle incomplete async actions at render</span></span>

<span data-ttu-id="cec86-156">Ações assíncronas executadas em eventos de ciclo de vida podem não ter sido concluídas antes de o componente ser renderizado.</span><span class="sxs-lookup"><span data-stu-id="cec86-156">Asynchronous actions performed in lifecycle events might not have completed before the component is rendered.</span></span> <span data-ttu-id="cec86-157">Os objetos podem ser `null` ou preenchidos incompletamente com dados enquanto o método de ciclo de vida está em execução.</span><span class="sxs-lookup"><span data-stu-id="cec86-157">Objects might be `null` or incompletely populated with data while the lifecycle method is executing.</span></span> <span data-ttu-id="cec86-158">Forneça a lógica de renderização para confirmar que os objetos são inicializados.</span><span class="sxs-lookup"><span data-stu-id="cec86-158">Provide rendering logic to confirm that objects are initialized.</span></span> <span data-ttu-id="cec86-159">Renderizar elementos de interface do usuário de espaço reservado (por exemplo, uma mensagem de carregamento) enquanto os objetos são `null`.</span><span class="sxs-lookup"><span data-stu-id="cec86-159">Render placeholder UI elements (for example, a loading message) while objects are `null`.</span></span>

<span data-ttu-id="cec86-160">No componente `FetchData` dos modelos de Blazor, `OnInitializedAsync` é substituído para Asychronously receber dados de previsão (`forecasts`).</span><span class="sxs-lookup"><span data-stu-id="cec86-160">In the `FetchData` component of the Blazor templates, `OnInitializedAsync` is overridden to asychronously receive forecast data (`forecasts`).</span></span> <span data-ttu-id="cec86-161">Quando `forecasts` é `null`, uma mensagem de carregamento é exibida para o usuário.</span><span class="sxs-lookup"><span data-stu-id="cec86-161">When `forecasts` is `null`, a loading message is displayed to the user.</span></span> <span data-ttu-id="cec86-162">Depois que o `Task` retornado pela `OnInitializedAsync` for concluído, o componente será rerenderizado com o estado atualizado.</span><span class="sxs-lookup"><span data-stu-id="cec86-162">After the `Task` returned by `OnInitializedAsync` completes, the component is rerendered with the updated state.</span></span>

<span data-ttu-id="cec86-163">*Páginas/FetchData. Razor* no modelo do Blazor Server:</span><span class="sxs-lookup"><span data-stu-id="cec86-163">*Pages/FetchData.razor* in the Blazor Server template:</span></span>

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a><span data-ttu-id="cec86-164">Descarte de componentes com IDisposable</span><span class="sxs-lookup"><span data-stu-id="cec86-164">Component disposal with IDisposable</span></span>

<span data-ttu-id="cec86-165">Se um componente implementar <xref:System.IDisposable>, o [método Dispose](/dotnet/standard/garbage-collection/implementing-dispose) será chamado quando o componente for removido da interface do usuário.</span><span class="sxs-lookup"><span data-stu-id="cec86-165">If a component implements <xref:System.IDisposable>, the [Dispose method](/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI.</span></span> <span data-ttu-id="cec86-166">O componente a seguir usa `@implements IDisposable` e o método `Dispose`:</span><span class="sxs-lookup"><span data-stu-id="cec86-166">The following component uses `@implements IDisposable` and the `Dispose` method:</span></span>

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
> <span data-ttu-id="cec86-167">Não há suporte para a chamada de <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> em `Dispose`.</span><span class="sxs-lookup"><span data-stu-id="cec86-167">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> in `Dispose` isn't supported.</span></span> <span data-ttu-id="cec86-168">`StateHasChanged` pode ser invocado como parte da subdivisão do renderizador, portanto, não há suporte para a solicitação de atualizações da interface do usuário nesse ponto.</span><span class="sxs-lookup"><span data-stu-id="cec86-168">`StateHasChanged` might be invoked as part of tearing down the renderer, so requesting UI updates at that point isn't supported.</span></span>

## <a name="handle-errors"></a><span data-ttu-id="cec86-169">Tratar erros</span><span class="sxs-lookup"><span data-stu-id="cec86-169">Handle errors</span></span>

<span data-ttu-id="cec86-170">Para obter informações sobre como lidar com erros durante a execução do método de ciclo de vida, consulte <xref:blazor/handle-errors#lifecycle-methods>.</span><span class="sxs-lookup"><span data-stu-id="cec86-170">For information on handling errors during lifecycle method execution, see <xref:blazor/handle-errors#lifecycle-methods>.</span></span>

## <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="cec86-171">Reconexão com estado após o pré-processamento</span><span class="sxs-lookup"><span data-stu-id="cec86-171">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="cec86-172">Em um aplicativo do Blazor Server quando `RenderMode` é `ServerPrerendered`, o componente é inicialmente renderizado estaticamente como parte da página.</span><span class="sxs-lookup"><span data-stu-id="cec86-172">In a Blazor Server app when `RenderMode` is `ServerPrerendered`, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="cec86-173">Depois que o navegador estabelece uma conexão de volta com o servidor, o componente é renderizado *novamente*e o componente agora é interativo.</span><span class="sxs-lookup"><span data-stu-id="cec86-173">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="cec86-174">Se o método de ciclo de vida de [{Async} OnInitialized](xref:blazor/lifecycle#component-initialization-methods) para inicializar o componente estiver presente, o método será executado *duas vezes*:</span><span class="sxs-lookup"><span data-stu-id="cec86-174">If the [OnInitialized{Async}](xref:blazor/lifecycle#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="cec86-175">Quando o componente é renderizado estaticamente.</span><span class="sxs-lookup"><span data-stu-id="cec86-175">When the component is prerendered statically.</span></span>
* <span data-ttu-id="cec86-176">Depois que a conexão do servidor tiver sido estabelecida.</span><span class="sxs-lookup"><span data-stu-id="cec86-176">After the server connection has been established.</span></span>

<span data-ttu-id="cec86-177">Isso pode resultar em uma alteração perceptível nos dados exibidos na interface do usuário quando o componente é finalmente renderizado.</span><span class="sxs-lookup"><span data-stu-id="cec86-177">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="cec86-178">Para evitar o cenário de processamento duplo em um aplicativo do Blazor Server:</span><span class="sxs-lookup"><span data-stu-id="cec86-178">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="cec86-179">Passe um identificador que pode ser usado para armazenar em cache o estado durante o pré-processamento e para recuperar o estado após a reinicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cec86-179">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="cec86-180">Use o identificador durante o pré-processamento para salvar o estado do componente.</span><span class="sxs-lookup"><span data-stu-id="cec86-180">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="cec86-181">Use o identificador após o pré-processamento para recuperar o estado armazenado em cache.</span><span class="sxs-lookup"><span data-stu-id="cec86-181">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="cec86-182">O código a seguir demonstra uma `WeatherForecastService` atualizada em um aplicativo do Blazor Server baseado em modelo que evita a renderização dupla:</span><span class="sxs-lookup"><span data-stu-id="cec86-182">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

```csharp
public class WeatherForecastService
{
    private static readonly string[] _summaries = new[]
    {
        "Freezing", "Bracing", "Chilly", "Cool", "Mild",
        "Warm", "Balmy", "Hot", "Sweltering", "Scorching"
    };
    
    public WeatherForecastService(IMemoryCache memoryCache)
    {
        MemoryCache = memoryCache;
    }
    
    public IMemoryCache MemoryCache { get; }

    public Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        return MemoryCache.GetOrCreateAsync(startDate, async e =>
        {
            e.SetOptions(new MemoryCacheEntryOptions
            {
                AbsoluteExpirationRelativeToNow = 
                    TimeSpan.FromSeconds(30)
            });

            var rng = new Random();

            await Task.Delay(TimeSpan.FromSeconds(10));

            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = startDate.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = _summaries[rng.Next(_summaries.Length)]
            }).ToArray();
        });
    }
}
```

<span data-ttu-id="cec86-183">Para obter mais informações sobre o `RenderMode`, consulte <xref:blazor/hosting-model-configuration#render-mode>.</span><span class="sxs-lookup"><span data-stu-id="cec86-183">For more information on the `RenderMode`, see <xref:blazor/hosting-model-configuration#render-mode>.</span></span>

## <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="cec86-184">Detectar quando o aplicativo está sendo renderizado</span><span class="sxs-lookup"><span data-stu-id="cec86-184">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]
