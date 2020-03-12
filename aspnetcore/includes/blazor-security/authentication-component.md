A página produzida pelo componente `Authentication` (*páginas/autenticação. Razor*) define as rotas necessárias para lidar com diferentes estágios de autenticação.

O componente `RemoteAuthenticatorView`:

* É fornecido pelo pacote de `Microsoft.AspNetCore.Components.WebAssembly.Authentication`.
* Gerencia a execução das ações apropriadas em cada estágio de autenticação.

```razor
@page "/authentication/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code {
    [Parameter]
    public string Action { get; set; }
}
```
