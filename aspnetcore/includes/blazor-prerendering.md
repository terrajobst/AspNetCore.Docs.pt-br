---
no-loc:
- Blazor
- SignalR
ms.openlocfilehash: 5f3e22e04fe18149ec5a8acb42f42a8ef83a7664
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78659715"
---
Embora um aplicativo de servidor Blazor esteja sendo renderizado, determinadas ações, como a chamada para JavaScript, não são possíveis porque uma conexão com o navegador não foi estabelecida. Os componentes podem precisar ser renderizados de forma diferente quando renderizados.

Para atrasar as chamadas de interoperabilidade do JavaScript até que a conexão com o navegador seja estabelecida, você pode usar o [evento de ciclo de vida do componente OnAfterRenderAsync](xref:blazor/lifecycle#after-component-render). Esse evento é chamado somente depois que o aplicativo é totalmente renderizado e a conexão do cliente é estabelecida.

```cshtml
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<div @ref="divElement">Text during render</div>

@code {
    private ElementReference divElement;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            await JSRuntime.InvokeVoidAsync(
                "setElementText", divElement, "Text after render");
        }
    }
}
```

Para o código de exemplo anterior, forneça uma `setElementText` função JavaScript dentro do elemento `<head>` de *wwwroot/index.html* (Blazor Webassembly) ou *pages/_Host. cshtml* (servidorBlazor). A função é chamada com `IJSRuntime.InvokeVoidAsync` e não retorna um valor:

```html
<script>
  window.setElementText = (element, text) => element.innerText = text;
</script>
```

> [!WARNING]
> O exemplo anterior modifica o Modelo de Objeto do Documento (DOM) diretamente para fins de demonstração. A modificação direta do DOM com o JavaScript não é recomendada na maioria dos cenários porque o JavaScript pode interferir no controle de alterações de Blazor.

O componente a seguir demonstra como usar a interoperabilidade do JavaScript como parte da lógica de inicialização de um componente de forma que seja compatível com o pré-processamento. O componente mostra que é possível disparar uma atualização de renderização de dentro `OnAfterRenderAsync`. O desenvolvedor deve evitar a criação de um loop infinito nesse cenário.

Onde `JSRuntime.InvokeAsync` é chamado, `ElementRef` é usado somente no `OnAfterRenderAsync` e não em nenhum método de ciclo de vida anterior porque não há nenhum elemento JavaScript até que o componente seja renderizado.

[StateHasChanged](xref:blazor/lifecycle#state-changes) é chamado para reprocessar o componente com o novo estado obtido da chamada de interoperabilidade JavaScript. O código não cria um loop infinito porque `StateHasChanged` é chamado somente quando `infoFromJs` é `null`.

```cshtml
@page "/prerendered-interop"
@using Microsoft.AspNetCore.Components
@using Microsoft.JSInterop
@inject IJSRuntime JSRuntime

<p>
    Get value via JS interop call:
    <strong id="val-get-by-interop">@(infoFromJs ?? "No value yet")</strong>
</p>

Set value via JS interop call:
<div id="val-set-by-interop" @ref="divElement"></div>

@code {
    private string infoFromJs;
    private ElementReference divElement;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender && infoFromJs == null)
        {
            infoFromJs = await JSRuntime.InvokeAsync<string>(
                "setElementText", divElement, "Hello from interop call!");

            StateHasChanged();
        }
    }
}
```

Para o código de exemplo anterior, forneça uma `setElementText` função JavaScript dentro do elemento `<head>` de *wwwroot/index.html* (Blazor Webassembly) ou *pages/_Host. cshtml* (servidorBlazor). A função é chamada com `IJSRuntime.InvokeAsync` e retorna um valor:

```html
<script>
  window.setElementText = (element, text) => {
    element.innerText = text;
    return text;
  };
</script>
```

> [!WARNING]
> O exemplo anterior modifica o Modelo de Objeto do Documento (DOM) diretamente para fins de demonstração. A modificação direta do DOM com o JavaScript não é recomendada na maioria dos cenários porque o JavaScript pode interferir no controle de alterações de Blazor.
