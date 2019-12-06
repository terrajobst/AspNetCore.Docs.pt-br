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
ms.openlocfilehash: 280ea832f492852e425e3e15c61cac54fd1e39d6
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74879680"
---
# <a name="aspnet-core-opno-locblazor-lifecycle"></a>Ciclo de ASP.NET Core Blazor

De [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)

O Blazor Framework inclui métodos de ciclo de vida síncronos e assíncronos. Substitua métodos de ciclo de vida para executar operações adicionais em componentes durante a inicialização e a renderização do componente.

## <a name="lifecycle-methods"></a>Métodos de ciclo de vida

### <a name="component-initialization-methods"></a>Métodos de inicialização de componente

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> executar código que inicializa um componente. Esses métodos são chamados apenas uma vez quando o componente é instanciado primeiro.

Para executar uma operação assíncrona, use `OnInitializedAsync` e a palavra-chave `await` na operação:

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

> [!NOTE]
> O trabalho assíncrono durante a inicialização do componente deve ocorrer durante o evento de ciclo de vida `OnInitializedAsync`.

Para uma operação síncrona, use `OnInitialized`:

```csharp
protected override void OnInitialized()
{
    ...
}
```

### <a name="before-parameters-are-set"></a>Antes de os parâmetros serem definidos

<xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync*> define os parâmetros fornecidos pelo pai do componente na árvore de renderização:

```csharp
public override async Task SetParametersAsync(ParameterView parameters)
{
    await ...

    await base.SetParametersAsync(parameters);
}
```

<xref:Microsoft.AspNetCore.Components.ParameterView> contém todo o conjunto de valores de parâmetro sempre que `SetParametersAsync` é chamado.

A implementação padrão de `SetParametersAsync` define o valor de cada propriedade com o atributo `[Parameter]` ou `[CascadingParameter]` que tem um valor correspondente no `ParameterView`. Os parâmetros que não têm um valor correspondente em `ParameterView` são deixados inalterados.

Se `base.SetParametersAync` não for invocado, o código personalizado poderá interpretar o valor dos parâmetros de entrada de qualquer forma necessária. Por exemplo, não há nenhum requisito para atribuir os parâmetros de entrada às propriedades na classe.

### <a name="after-parameters-are-set"></a>Depois que os parâmetros são definidos

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> são chamados quando um componente recebe parâmetros de seu pai e os valores são atribuídos às propriedades. Esses métodos são executados após a inicialização do componente e cada vez que novos valores de parâmetro são especificados:

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> O trabalho assíncrono ao aplicar parâmetros e valores de propriedade deve ocorrer durante o evento de ciclo de vida `OnParametersSetAsync`.

```csharp
protected override void OnParametersSet()
{
    ...
}
```

### <a name="after-component-render"></a>Após renderização de componente

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync*> e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender*> são chamados após a conclusão da renderização de um componente. Referências de elemento e componente são preenchidas neste ponto. Use este estágio para executar etapas de inicialização adicionais usando o conteúdo renderizado, como a ativação de bibliotecas JavaScript de terceiros que operam nos elementos DOM renderizados.

O parâmetro `firstRender` para `OnAfterRenderAsync` e `OnAfterRender`:

* É definido como `true` na primeira vez que a instância do componente é renderizada.
* Pode ser usado para garantir que o trabalho de inicialização seja executado apenas uma vez.

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
> O trabalho assíncrono imediatamente após a renderização deve ocorrer durante o evento de ciclo de vida `OnAfterRenderAsync`.
>
> Mesmo que você retorne um <xref:System.Threading.Tasks.Task> de `OnAfterRenderAsync`, a estrutura não agendará um ciclo de processamento adicional para o componente depois que a tarefa for concluída. Isso é para evitar um loop de renderização infinito. Ele é diferente dos outros métodos de ciclo de vida, que agendam um ciclo de processamento adicional depois que a tarefa retornada é concluída.

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

`OnAfterRender` e `OnAfterRenderAsync` *não são chamados durante o pré-processamento no servidor.*

### <a name="suppress-ui-refreshing"></a>Suprimir atualização da interface do usuário

Substitua <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender*> para suprimir a atualização da interface do usuário. Se a implementação retornar `true`, a interface do usuário será atualizada:

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

`ShouldRender` é chamado cada vez que o componente é renderizado.

Mesmo se `ShouldRender` for substituído, o componente será sempre renderizado inicialmente.

## <a name="state-changes"></a>Alterações de estado

<xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> notifica o componente de que seu estado foi alterado. Quando aplicável, chamar `StateHasChanged` faz com que o componente seja rerenderizado.

## <a name="handle-incomplete-async-actions-at-render"></a>Tratar ações assíncronas incompletas no processamento

Ações assíncronas executadas em eventos de ciclo de vida podem não ter sido concluídas antes de o componente ser renderizado. Os objetos podem ser `null` ou preenchidos incompletamente com dados enquanto o método de ciclo de vida está em execução. Forneça a lógica de renderização para confirmar que os objetos são inicializados. Renderizar elementos de interface do usuário de espaço reservado (por exemplo, uma mensagem de carregamento) enquanto os objetos são `null`.

No componente `FetchData` dos modelos de Blazor, `OnInitializedAsync` é substituído para Asychronously receber dados de previsão (`forecasts`). Quando `forecasts` é `null`, uma mensagem de carregamento é exibida para o usuário. Depois que o `Task` retornado pela `OnInitializedAsync` for concluído, o componente será rerenderizado com o estado atualizado.

*Páginas/FetchData. Razor* no modelo do Blazor Server:

[!code-cshtml[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

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

> [!NOTE]
> Não há suporte para a chamada de <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> em `Dispose`. `StateHasChanged` pode ser invocado como parte da subdivisão do renderizador, portanto, não há suporte para a solicitação de atualizações da interface do usuário nesse ponto.

## <a name="handle-errors"></a>Tratar erros

Para obter informações sobre como lidar com erros durante a execução do método de ciclo de vida, consulte <xref:blazor/handle-errors#lifecycle-methods>.
