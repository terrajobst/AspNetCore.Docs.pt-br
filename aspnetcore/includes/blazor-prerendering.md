<span data-ttu-id="72db0-101">Embora um aplicativo do lado do servidor mais alto seja renderizado, determinadas ações, como a chamada ao JavaScript, não são possíveis porque uma conexão com o navegador não foi estabelecida.</span><span class="sxs-lookup"><span data-stu-id="72db0-101">While a Blazor server-side app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="72db0-102">Os componentes podem precisar ser renderizados de forma diferente quando renderizados.</span><span class="sxs-lookup"><span data-stu-id="72db0-102">Components may need to render differently when prerendered.</span></span>

<span data-ttu-id="72db0-103">Para atrasar as chamadas de interoperabilidade do JavaScript até que a conexão com o navegador seja estabelecida `OnAfterRenderAsync` , você pode usar o evento de ciclo de vida do componente.</span><span class="sxs-lookup"><span data-stu-id="72db0-103">To delay JavaScript interop calls until after the connection with the browser is established, you can use the `OnAfterRenderAsync` component lifecycle event.</span></span> <span data-ttu-id="72db0-104">Esse evento é chamado somente depois que o aplicativo é totalmente renderizado e a conexão do cliente é estabelecida.</span><span class="sxs-lookup"><span data-stu-id="72db0-104">This event is only called after the app is fully rendered and the client connection is established.</span></span>

```cshtml
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<input @ref="myInput" value="Value set during render" />

@code {
    private ElementReference myInput;

    protected override void OnAfterRender()
    {
        JSRuntime.InvokeAsync<object>(
            "setElementValue", myInput, "Value set after render");
    }
}
```

<span data-ttu-id="72db0-105">O componente a seguir demonstra como usar a interoperabilidade do JavaScript como parte da lógica de inicialização de um componente de forma que seja compatível com o pré-processamento.</span><span class="sxs-lookup"><span data-stu-id="72db0-105">The following component demonstrates how to use JavaScript interop as part of a component's initialization logic in a way that's compatible with prerendering.</span></span> <span data-ttu-id="72db0-106">O componente mostra que é possível disparar uma atualização de renderização de dentro `OnAfterRenderAsync`do.</span><span class="sxs-lookup"><span data-stu-id="72db0-106">The component shows that it's possible to trigger a rendering update from inside `OnAfterRenderAsync`.</span></span> <span data-ttu-id="72db0-107">O desenvolvedor deve evitar a criação de um loop infinito nesse cenário.</span><span class="sxs-lookup"><span data-stu-id="72db0-107">The developer must avoid creating an infinite loop in this scenario.</span></span>

<span data-ttu-id="72db0-108">Em `JSRuntime.InvokeAsync` que é chamado `ElementRef` , é usado apenas `OnAfterRenderAsync` em e não em nenhum método de ciclo de vida anterior porque não há nenhum elemento JavaScript até que o componente seja renderizado.</span><span class="sxs-lookup"><span data-stu-id="72db0-108">Where `JSRuntime.InvokeAsync` is called, `ElementRef` is only used in `OnAfterRenderAsync` and not in any earlier lifecycle method because there's no JavaScript element until after the component is rendered.</span></span>

<span data-ttu-id="72db0-109">`StateHasChanged`é chamado para reprocessar o componente com o novo estado obtido da chamada de interoperabilidade JavaScript.</span><span class="sxs-lookup"><span data-stu-id="72db0-109">`StateHasChanged` is called to rerender the component with the new state obtained from the JavaScript interop call.</span></span> <span data-ttu-id="72db0-110">O código não cria um loop infinito porque `StateHasChanged` é chamado somente quando `infoFromJs` é `null`.</span><span class="sxs-lookup"><span data-stu-id="72db0-110">The code doesn't create an infinite loop because `StateHasChanged` is only called when `infoFromJs` is `null`.</span></span>

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

    protected override async Task OnAfterRenderAsync()
    {
        // TEMPORARY: Currently we need this guard to avoid making the interop
        // call during prerendering. Soon this will be unnecessary because we
        // will change OnAfterRenderAsync so that it won't run during the
        // prerendering phase.
        if (!ComponentContext.IsConnected)
        {
            return;
        }

        if (infoFromJs == null)
        {
            infoFromJs = await JSRuntime.InvokeAsync<string>(
                "setElementValue", myElem, "Hello from interop call");

            StateHasChanged();
        }
    }
}
```

<span data-ttu-id="72db0-111">Para renderizar condicionalmente um conteúdo diferente com base em se o aplicativo está atualmente renderizando o conteúdo `IsConnected` , use a `IComponentContext` Propriedade no serviço.</span><span class="sxs-lookup"><span data-stu-id="72db0-111">To conditionally render different content based on whether the app is currently prerendering content, use the `IsConnected` property on the `IComponentContext` service.</span></span> <span data-ttu-id="72db0-112">Ao executar o servidor, `IsConnected` o só retornará `true` se houver uma conexão ativa com o cliente.</span><span class="sxs-lookup"><span data-stu-id="72db0-112">When running server-side, `IsConnected` only returns `true` if there's an active connection to the client.</span></span> <span data-ttu-id="72db0-113">Ele sempre retorna `true` ao executar o lado do cliente.</span><span class="sxs-lookup"><span data-stu-id="72db0-113">It always returns `true` when running client-side.</span></span>

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
