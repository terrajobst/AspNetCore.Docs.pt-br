<span data-ttu-id="c30bb-101">Embora um aplicativo de servidor mais incrivelmente seja o pré-processamento, determinadas ações, como a chamada para JavaScript, não são possíveis porque uma conexão com o navegador não foi estabelecida.</span><span class="sxs-lookup"><span data-stu-id="c30bb-101">While a Blazor Server app is prerendering, certain actions, such as calling into JavaScript, aren't possible because a connection with the browser hasn't been established.</span></span> <span data-ttu-id="c30bb-102">Os componentes podem precisar ser renderizados de forma diferente quando renderizados.</span><span class="sxs-lookup"><span data-stu-id="c30bb-102">Components may need to render differently when prerendered.</span></span>

<span data-ttu-id="c30bb-103">Para atrasar as chamadas de interoperabilidade do JavaScript até que a conexão com o navegador seja estabelecida, você pode usar o evento `OnAfterRenderAsync` ciclo de vida do componente.</span><span class="sxs-lookup"><span data-stu-id="c30bb-103">To delay JavaScript interop calls until after the connection with the browser is established, you can use the `OnAfterRenderAsync` component lifecycle event.</span></span> <span data-ttu-id="c30bb-104">Esse evento é chamado somente depois que o aplicativo é totalmente renderizado e a conexão do cliente é estabelecida.</span><span class="sxs-lookup"><span data-stu-id="c30bb-104">This event is only called after the app is fully rendered and the client connection is established.</span></span>

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
            JSRuntime.InvokeVoidAsync(
                "setElementValue", myInput, "Value set after render");
        }
    }
}
```

<span data-ttu-id="c30bb-105">O componente a seguir demonstra como usar a interoperabilidade do JavaScript como parte da lógica de inicialização de um componente de forma que seja compatível com o pré-processamento.</span><span class="sxs-lookup"><span data-stu-id="c30bb-105">The following component demonstrates how to use JavaScript interop as part of a component's initialization logic in a way that's compatible with prerendering.</span></span> <span data-ttu-id="c30bb-106">O componente mostra que é possível disparar uma atualização de renderização de dentro `OnAfterRenderAsync`.</span><span class="sxs-lookup"><span data-stu-id="c30bb-106">The component shows that it's possible to trigger a rendering update from inside `OnAfterRenderAsync`.</span></span> <span data-ttu-id="c30bb-107">O desenvolvedor deve evitar a criação de um loop infinito nesse cenário.</span><span class="sxs-lookup"><span data-stu-id="c30bb-107">The developer must avoid creating an infinite loop in this scenario.</span></span>

<span data-ttu-id="c30bb-108">Onde `JSRuntime.InvokeAsync` é chamado, `ElementRef` é usado somente no `OnAfterRenderAsync` e não em nenhum método de ciclo de vida anterior porque não há nenhum elemento JavaScript até que o componente seja renderizado.</span><span class="sxs-lookup"><span data-stu-id="c30bb-108">Where `JSRuntime.InvokeAsync` is called, `ElementRef` is only used in `OnAfterRenderAsync` and not in any earlier lifecycle method because there's no JavaScript element until after the component is rendered.</span></span>

<span data-ttu-id="c30bb-109">`StateHasChanged` é chamado para reprocessar o componente com o novo estado obtido da chamada de interoperabilidade do JavaScript.</span><span class="sxs-lookup"><span data-stu-id="c30bb-109">`StateHasChanged` is called to rerender the component with the new state obtained from the JavaScript interop call.</span></span> <span data-ttu-id="c30bb-110">O código não cria um loop infinito porque `StateHasChanged` é chamado somente quando `infoFromJs` é `null`.</span><span class="sxs-lookup"><span data-stu-id="c30bb-110">The code doesn't create an infinite loop because `StateHasChanged` is only called when `infoFromJs` is `null`.</span></span>

```cshtml
@page "/prerendered-interop"
@using Microsoft.AspNetCore.Components
@using Microsoft.JSInterop
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
