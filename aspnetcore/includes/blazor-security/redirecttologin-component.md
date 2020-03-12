O componente `RedirectToLogin` (*Shared/RedirectToLogin. Razor*):

* Gerencia o redirecionamento de usuários não autorizados para a página de logon.
* Preserva a URL atual que o usuário está tentando acessar para que possa ser retornada a essa página se a autenticação for bem-sucedida.

```razor
@inject NavigationManager Navigation
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo($"authentication/login?returnUrl={Navigation.Uri}");
    }
}
```
