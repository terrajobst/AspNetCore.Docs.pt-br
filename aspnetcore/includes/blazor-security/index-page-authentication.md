A página de índice (*wwwroot/index.html*) inclui um script que define o `AuthenticationService` em JavaScript. `AuthenticationService` manipula os detalhes de baixo nível do protocolo OIDC. O aplicativo chama internamente os métodos definidos no script para executar as operações de autenticação.

```html
<script src="_content/Microsoft.AspNetCore.Components.WebAssembly.Authentication/
    AuthenticationService.js"></script>
```
