<span data-ttu-id="ea6b1-101">A página de índice (*wwwroot/index.html*) inclui um script que define o `AuthenticationService` em JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ea6b1-101">The Index page (*wwwroot/index.html*) page includes a script that defines the `AuthenticationService` in JavaScript.</span></span> <span data-ttu-id="ea6b1-102">`AuthenticationService` manipula os detalhes de baixo nível do protocolo OIDC.</span><span class="sxs-lookup"><span data-stu-id="ea6b1-102">`AuthenticationService` handles the low-level details of the the OIDC protocol.</span></span> <span data-ttu-id="ea6b1-103">O aplicativo chama internamente os métodos definidos no script para executar as operações de autenticação.</span><span class="sxs-lookup"><span data-stu-id="ea6b1-103">The app internally calls methods defined in the script to perform the authentication operations.</span></span>

```html
<script src="_content/Microsoft.Authentication.WebAssembly.Msal/
    AuthenticationService.js"></script>
```
