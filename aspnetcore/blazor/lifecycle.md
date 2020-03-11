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
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78659925"
---
# <a name="aspnet-core-opno-locblazor-lifecycle"></a>Ciclo de ASP.NET Core Blazor

De [Luke Latham](https://github.com/guardrex) e [Daniel Roth](https://github.com/danroth27)

O Blazor Framework inclui métodos de ciclo de vida síncronos e assíncronos. Substitua métodos de ciclo de vida para executar operações adicionais em componentes durante a inicialização e a renderização do componente.

## <a name="lifecycle-methods"></a>Métodos de ciclo de vida

### <a name="component-initialization-methods"></a>Métodos de inicialização de componente

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync*> e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized*> são invocados quando o componente é inicializado após ter recebido seus parâmetros iniciais de seu componente pai. Use `OnInitializedAsync` quando o componente executa uma operação assíncrona e deve ser atualizado quando a operação é concluída.

Para uma operação síncrona, substitua `OnInitialized`:

```csharp
protected override void OnInitialized()
{
    ...
}
```

Para executar uma operação assíncrona, substitua `OnInitializedAsync` e use a palavra-chave `await` na operação:

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

Blazor aplicativos de servidor que preparam [sua chamada de conteúdo](xref:blazor/hosting-model-configuration#render-mode) `OnInitializedAsync` **_duas vezes_** :

* Uma vez quando o componente é inicialmente renderizado estaticamente como parte da página.
* Uma segunda vez quando o navegador estabelece uma conexão de volta para o servidor.

Para impedir que o código do desenvolvedor em `OnInitializedAsync` seja executado duas vezes, consulte a seção [reconexão com estado após o pré-processamento](#stateful-reconnection-after-prerendering) .

Embora um aplicativo de servidor Blazor esteja sendo renderizado, determinadas ações, como a chamada para JavaScript, não são possíveis porque uma conexão com o navegador não foi estabelecida. Os componentes podem precisar ser renderizados de forma diferente quando renderizados. Para obter mais informações, consulte a seção [detectar quando o aplicativo está sendo processado](#detect-when-the-app-is-prerendering) .

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

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync*> e <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet*> são chamados:

* Quando o componente é inicializado e recebeu seu primeiro conjunto de parâmetros de seu componente pai.
* Quando o componente pai é renderizado novamente e fornece:
  * Somente tipos irmutáveis de primitivo conhecidos dos quais pelo menos um parâmetro foi alterado.
  * Qualquer parâmetro de tipo complexo. A estrutura não pode saber se os valores de um parâmetro de tipo complexo foram modificados internamente e, portanto, trata o conjunto de parâmetros como alterado.

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

[!code-razor[](lifecycle/samples_snapshot/3.x/FetchData.razor?highlight=9,21,25)]

## <a name="component-disposal-with-idisposable"></a>Descarte de componentes com IDisposable

Se um componente implementar <xref:System.IDisposable>, o [método Dispose](/dotnet/standard/garbage-collection/implementing-dispose) será chamado quando o componente for removido da interface do usuário. O componente a seguir usa `@implements IDisposable` e o método `Dispose`:

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
> Não há suporte para a chamada de <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged*> em `Dispose`. `StateHasChanged` pode ser invocado como parte da subdivisão do renderizador, portanto, não há suporte para a solicitação de atualizações da interface do usuário nesse ponto.

## <a name="handle-errors"></a>Tratar erros

Para obter informações sobre como lidar com erros durante a execução do método de ciclo de vida, consulte <xref:blazor/handle-errors#lifecycle-methods>.

## <a name="stateful-reconnection-after-prerendering"></a>Reconexão com estado após o pré-processamento

Em um aplicativo do Blazor Server quando `RenderMode` é `ServerPrerendered`, o componente é inicialmente renderizado estaticamente como parte da página. Depois que o navegador estabelece uma conexão de volta com o servidor, o componente é renderizado *novamente*e o componente agora é interativo. Se o método de ciclo de vida de [{Async} OnInitialized](xref:blazor/lifecycle#component-initialization-methods) para inicializar o componente estiver presente, o método será executado *duas vezes*:

* Quando o componente é renderizado estaticamente.
* Depois que a conexão do servidor tiver sido estabelecida.

Isso pode resultar em uma alteração perceptível nos dados exibidos na interface do usuário quando o componente é finalmente renderizado.

Para evitar o cenário de processamento duplo em um aplicativo do Blazor Server:

* Passe um identificador que pode ser usado para armazenar em cache o estado durante o pré-processamento e para recuperar o estado após a reinicialização do aplicativo.
* Use o identificador durante o pré-processamento para salvar o estado do componente.
* Use o identificador após o pré-processamento para recuperar o estado armazenado em cache.

O código a seguir demonstra uma `WeatherForecastService` atualizada em um aplicativo do Blazor Server baseado em modelo que evita a renderização dupla:

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

Para obter mais informações sobre o `RenderMode`, consulte <xref:blazor/hosting-model-configuration#render-mode>.

## <a name="detect-when-the-app-is-prerendering"></a>Detectar quando o aplicativo está sendo renderizado

[!INCLUDE[](~/includes/blazor-prerendering.md)]
