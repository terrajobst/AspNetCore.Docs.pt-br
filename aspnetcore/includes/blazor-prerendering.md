Embora um aplicativo do lado do servidor mais alto seja renderizado, determinadas ações, como a chamada ao JavaScript, não são possíveis porque uma conexão com o navegador não foi estabelecida. Os componentes podem precisar ser renderizados de forma diferente quando renderizados.

Para atrasar as chamadas de interoperabilidade do JavaScript até que a conexão com o navegador seja estabelecida `OnAfterRenderAsync` , você pode usar o evento de ciclo de vida do componente. Esse evento é chamado somente depois que o aplicativo é totalmente renderizado e a conexão do cliente é estabelecida.

```cshtml
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<input @ref="myInput" value="Value set during render" />

@code {
    private ElementReference myInput;

    protected override void OnAfterRender(bool firstRender)
    {
        if (firstRender)
        {
            JSRuntime.InvokeAsync<object>(
                "setElementValue", myInput, "Value set after render");
        }
    }
}
```

O componente a seguir demonstra como usar a interoperabilidade do JavaScript como parte da lógica de inicialização de um componente de forma que seja compatível com o pré-processamento. O componente mostra que é possível disparar uma atualização de renderização de dentro `OnAfterRenderAsync`do. O desenvolvedor deve evitar a criação de um loop infinito nesse cenário.

Em `JSRuntime.InvokeAsync` que é chamado `ElementRef` , é usado apenas `OnAfterRenderAsync` em e não em nenhum método de ciclo de vida anterior porque não há nenhum elemento JavaScript até que o componente seja renderizado.

`StateHasChanged`é chamado para reprocessar o componente com o novo estado obtido da chamada de interoperabilidade JavaScript. O código não cria um loop infinito porque `StateHasChanged` é chamado somente quando `infoFromJs` é `null`.

```cshtml
@page "/prerendered-interop"
@using Microsoft.AspNetCore.Components
@using Microsoft.JSInterop
@inject IComponentContext ComponentContext
@inject IJSRuntime JSRuntime

<p>
    Get value via JS interop call:
    <strong id="val-get-by-interop">@(infoFromJs ?? "No value yet")</strong>
</p>

<p>
    Set value via JS interop call:
    <input id="val-set-by-interop" @ref="myElem" />
</p>

@code {
    private string infoFromJs;
    private ElementReference myElem;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender && infoFromJs == null)
        {
            infoFromJs = await JSRuntime.InvokeAsync<string>(
                "setElementValue", myElem, "Hello from interop call");

            StateHasChanged();
        }
    }
}
```

Para renderizar condicionalmente um conteúdo diferente com base em se o aplicativo está atualmente renderizando o conteúdo `IsConnected` , use a `IComponentContext` Propriedade no serviço. Ao executar o servidor, `IsConnected` o só retornará `true` se houver uma conexão ativa com o cliente. Ele sempre retorna `true` ao executar o lado do cliente.

```cshtml
@page "/isconnected-example"
@using Microsoft.AspNetCore.Components.Services
@inject IComponentContext ComponentContext

<h1>IsConnected Example</h1>

<p>
    Current state:
    <strong id="connected-state">
        @(ComponentContext.IsConnected ? "connected" : "not connected")
    </strong>
</p>

<p>
    Clicks:
    <strong id="count">@count</strong>
    <button id="increment-count" @onclick="@(() => count++)">Click me</button>
</p>

@code {
    private int count;
}
```
