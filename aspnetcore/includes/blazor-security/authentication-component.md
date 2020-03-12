<span data-ttu-id="f3d14-101">A página produzida pelo componente `Authentication` (*páginas/autenticação. Razor*) define as rotas necessárias para lidar com diferentes estágios de autenticação.</span><span class="sxs-lookup"><span data-stu-id="f3d14-101">The page produced by the `Authentication` component (*Pages/Authentication.razor*) defines the routes required for handling different authentication stages.</span></span>

<span data-ttu-id="f3d14-102">O componente `RemoteAuthenticatorView`:</span><span class="sxs-lookup"><span data-stu-id="f3d14-102">The `RemoteAuthenticatorView` component:</span></span>

* <span data-ttu-id="f3d14-103">É fornecido pelo pacote de `Microsoft.AspNetCore.Components.WebAssembly.Authentication`.</span><span class="sxs-lookup"><span data-stu-id="f3d14-103">Is provided by the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span>
* <span data-ttu-id="f3d14-104">Gerencia a execução das ações apropriadas em cada estágio de autenticação.</span><span class="sxs-lookup"><span data-stu-id="f3d14-104">Manages performing the appropriate actions at each stage of authentication.</span></span>

```razor
@page "/authentication/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code {
    [Parameter]
    public string Action { get; set; }
}
```
